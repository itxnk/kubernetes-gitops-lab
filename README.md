# Kubernetes GitOps Lab

A declarative Kubernetes deployment managed from Git with Kustomize and Argo CD.

## Architecture
```text
GitHub repository (this repo)
       |
       v
Argo CD watches k8s/overlays/dev
       |
       v
Kubernetes API
       |
       v
Deployment -> ReplicaSet -> Pods (nginx)
       |
       v
ClusterIP Service :80
```

## Deploy manually
Requirements: a Kubernetes cluster and kubectl.

```bash
kubectl apply -k k8s/overlays/dev
kubectl get all -n portfolio-demo
kubectl port-forward -n portfolio-demo svc/web 8080:80
```
Open http://localhost:8080.

## Connect Argo CD
Apply the Application manifest after installing Argo CD:
```bash
kubectl apply -f argocd/application.yaml
argocd app get portfolio-demo
argocd app sync portfolio-demo
```
The Application points to this repository's `k8s/overlays/dev` path on `main`. For a private repository, configure repository credentials in Argo CD.

## Change the app
Edit the image or replica count in `k8s/base/deployment.yaml`, commit and push. Argo CD will detect the Git change; with automated sync enabled it will reconcile the cluster.

## Important
This is a safe demo using the public nginx image. It does not create a cloud cluster or expose a public load balancer. Configure resource requests, probes, policies, secrets and ingress before production use.
