# ArgoCD GitOps Demo Repository

## 🚀 Overview

This repository provides a **fully automated GitOps-based deployment setup** using **ArgoCD**. It showcases how to deploy applications to Kubernetes while ensuring **declarative, auditable, and scalable** deployments.

## 🎯 **Prerequisites**

Ensure you have the following installed:

- [Docker](https://docs.docker.com/get-docker/)
- [kubectl](https://kubernetes.io/docs/tasks/tools/)
- [Helm](https://helm.sh/docs/intro/install/)
- [KinD](https://kind.sigs.k8s.io/docs/user/quick-start/)
- [ArgoCD CLI](https://argo-cd.readthedocs.io/en/stable/cli_installation/)

## 🛠 **Installation & Setup**

### 1️⃣ Clone the Repository

```sh
git clone https://github.com/NoNickeD/argocd-demo.git
cd argocd-demo
```

### 2️⃣ Deploy a Local Kubernetes Cluster (KinD)

```sh
task full-deploy-local
```

This will:

- Create a **KinD cluster**
- Install **ArgoCD, Cert-Manager, and Metrics Server**
- Configure **Kubernetes context**
- Set up **RBAC, Network Policies, and Auto Scaling**

### 3️⃣ Deploy TLS Certificates

```sh
kubectl apply -f ./certmanager/cluster-issuer.yaml
kubectl apply -f ./certmanager/argocd-cert.yaml
kubectl --namespace argocd get certificate,issuers.cert-manager.io
```

### 4️⃣ Access ArgoCD UI

```sh
kubectl --namespace argocd port-forward svc/argocd-server 8080:80
open http://localhost:8080
```

### 5️⃣ Login to ArgoCD

```sh
argocd login localhost:8080 --username admin --password $(kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d; echo) --insecure
```

### 6️⃣ Authenticate with GitHub Container Registry

```sh
export CR_PAT=ghp_************************************  # Replace with your token
echo $CR_PAT | docker login ghcr.io -u NoNickeD --password-stdin
```

### 7️⃣ Build & Push Docker Image

```sh
cd ./app/
docker build -t ghcr.io/nonicked/argocd-demo:latest .
docker push ghcr.io/nonicked/argocd-demo:latest
```

### 8️⃣ Add Git Repository to ArgoCD

```sh
# Change username and repo with yours.
argocd repo add https://github.com/NoNickeD/argocd-demo.git --username "NoNickeD" --password "ghp_************************************"
```

### 9️⃣ Deploy Application via ArgoCD

```sh
# Change repo with yours.
argocd app create echo-server \
  --repo https://github.com/NoNickeD/argocd-demo.git \
  --path k8s/base \
  --dest-server https://kubernetes.default.svc \
  --dest-namespace default \
  --sync-policy automated \
  --self-heal
```

### 🔟 Verify Deployment

```sh
argocd app list
kubectl get pods -n default
```

![ArgoCD](/images/argocd-demo.png)

## 🗑 **Cleanup**

To delete the local Kubernetes cluster:

```sh
task delete-local-cluster
```

## 🚀 **Conclusion**

This demo provides a **GitOps-based Kubernetes deployment pipeline** using **ArgoCD and Kustomize**. The setup ensures **secure, scalable, and reliable** application rollouts with automated drift detection and rollback capabilities.
