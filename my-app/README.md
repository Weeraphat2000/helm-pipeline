# ArgoCD Integration with Helm

This project demonstrates how to use Helm charts with ArgoCD for continuous delivery on Kubernetes.

## How to Install ArgoCD

You can install ArgoCD on your Kubernetes cluster using the following steps:

1. **Install ArgoCD in the argocd namespace:**

```sh
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

2. **(Optional) Expose the ArgoCD API Server:**
   For local access, you can port-forward the ArgoCD API server:

```sh
kubectl port-forward svc/argocd-server -n argocd 8080:443
```

Then access the UI at [https://localhost:8080](https://localhost:8080).

3. **Get the initial admin password:**

```sh
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d; echo
```

For more details, see the [official ArgoCD installation guide](https://argo-cd.readthedocs.io/en/stable/getting_started/).

## What is ArgoCD?

ArgoCD is a declarative, GitOps continuous delivery tool for Kubernetes. It allows you to manage your Kubernetes resources using Git repositories as the source of truth.

## How to Use This Project with ArgoCD

1. **Install ArgoCD**

   - Follow the [official ArgoCD installation guide](https://argo-cd.readthedocs.io/en/stable/getting_started/).

2. **Add Your Helm Chart to Git**

   - Ensure your `my-app` Helm chart is committed to your Git repository.

3. **Create an ArgoCD Application**
   - You can create an ArgoCD Application via the UI or by applying a manifest. Example manifest:

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: my-app
  namespace: argocd
spec:
  project: default
  source:
    repoURL: "https://github.com/your-org/your-repo.git"
    targetRevision: HEAD
    path: my-app
    helm:
      valueFiles:
        - values.yaml
  destination:
    server: "https://kubernetes.default.svc"
    namespace: my-app
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
```

4. **Sync the Application**
   - Use the ArgoCD UI or CLI to sync and deploy your Helm chart.

## Useful Commands

- ArgoCD CLI login:
  ```sh
  argocd login <ARGOCD_SERVER>
  ```
- Sync an application:
  ```sh
  argocd app sync my-app
  ```

## References

- [ArgoCD Documentation](https://argo-cd.readthedocs.io/)
- [Helm Documentation](https://helm.sh/docs/)
