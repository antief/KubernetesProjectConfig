# Kubernetes project configuration

Kubernetes manifests and Kustomize overlays for the [Todo project](https://github.com/antief/KubernetesSubmissions).

Argo CD synchronizes `overlays/staging` from `main` and `overlays/production` from `production-release`. Source repository CI publishes images and updates the corresponding overlay. Production configuration changes are promoted with a source release.

Create both namespaces:

```bash
kubectl apply -f overlays/staging/namespace.yaml
kubectl apply -f overlays/production/namespace.yaml
```

Provision [NATS and the production webhook Secret](https://github.com/antief/KubernetesSubmissions/tree/main/broadcaster#nats) and [database Secrets](https://github.com/antief/KubernetesSubmissions/tree/main/todo-backend#secrets-and-deployment), then register the applications:

```bash
kubectl apply -n argocd -f argocd/project-staging-application.yaml
kubectl apply -n argocd -f argocd/project-production-application.yaml
```

Staging logs broadcasts and has no database backup. Production forwards webhook messages and saves daily PostgreSQL dumps on the `todo-backups` PVC. Local backups survive Pod restarts but not loss of the host or cluster.
