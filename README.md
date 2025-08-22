# 🚀 GitOps Workflow using ArgoCD on Kubernetes (EC2)

## 📌 Project Overview

This project demonstrates the implementation of a **GitOps workflow** using **ArgoCD** on a **Kubernetes cluster deployed on an AWS EC2 instance** (via K3s).  
It automates application deployment by syncing Kubernetes manifests from a GitHub repository.

---

## 📖 Table of Contents

- [Introduction](#introduction)
- [Architecture](#architecture)
- [Tools Used](#tools-used)
- [Setup Instructions](#setup-instructions)
- [Project Workflow](#project-workflow)
- [Screenshots](#screenshots)
- [Conclusion](#conclusion)

---

# 📝 Introduction

GitOps is a modern approach to Continuous Deployment (CD) where the desired state of infrastructure and applications is stored in Git.  
This project sets up ArgoCD on a Kubernetes cluster (K3s) hosted on AWS EC2 and connects it to a GitHub repository containing Kubernetes manifests for an NGINX application.

---

# 🧱 Architecture

```text
GitHub Repo (YAML Manifests)
           |
           v
      ArgoCD on EC2 (K3s)
           |
           v
Kubernetes Cluster (Deploys App)
```


# 🛠 Tools Used

| Tool    | Purpose                             |
| ------- | ----------------------------------- |
| AWS EC2 | Host Ubuntu VM for K3s              |
| K3s     | Lightweight Kubernetes Distribution |
| ArgoCD  | GitOps Continuous Delivery Tool     |
| GitHub  | Stores Kubernetes manifests         |
| NGINX   | Sample application                  |
| kubectl | Kubernetes CLI tool                 |


# ⚙️ Setup Instructions

1️⃣ Launch EC2 Instance
OS: Ubuntu 20.04 or 22.04
Type: t2.medium (2 vCPU, 4GB RAM)
Open ports: 22, 80, 443, and 30000-32767


2️⃣ Install K3s

``` curl -sfL https://get.k3s.io | sh -```

Copy kubeconfig:

```mkdir -p ~/.kube```
```sudo cp /etc/rancher/k3s/k3s.yaml ~/.kube/config```
```sudo chown $USER:$USER ~/.kube/config```

3️⃣ Install ArgoCD

``` kubectl create namespace argocd```
```kubectl apply -n argocd \```
``` -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml```

Expose UI:

```
kubectl -n argocd patch svc argocd-server \
  -p '{"spec": {"type": "NodePort"}}'
kubectl -n argocd get svc argocd-server

```


Login:

```
kubectl -n argocd get secret argocd-initial-admin-secret \
  -o jsonpath="{.data.password}" | base64 -d; echo
```


4️⃣ Deploy Application via GitOps

Push deployment.yaml and service.yaml to GitHub repo.
Create application.yaml:

```
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: nginx-gitops
  namespace: argocd
spec:
  project: default
  source:
    repoURL: https://github.com/<YOUR_GH_USERNAME>/my-gitops-app.git
    targetRevision: main
    path: .
  destination:
    server: https://kubernetes.default.svc
    namespace: default
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
```


Apply:

```
kubectl apply -f application.yaml
```

# 🔄 Project Workflow

You push changes to deployment.yaml in GitHub.

ArgoCD detects changes and syncs the cluster.

Kubernetes deploys/updates the NGINX app.

Service is exposed via NodePort → Accessible via EC2 Public IP.


# 📷 Screenshots

| Description        | Screenshot                              |
| ------------------ | --------------------------------------- |
| ArgoCD Dashboard   | <img width="1337" height="601" alt="Screenshot 2025-08-22 150809" src="https://github.com/user-attachments/assets/a67119d4-b4d2-46ad-87e4-37e4eac0e752" />|
| NGINX Pods Running | <img width="1352" height="257" alt="Screenshot 2025-08-22 150731" src="https://github.com/user-attachments/assets/37e881f1-6bb4-4568-85aa-d0858a919ff5" />|
| App in Browser     | <img width="1326" height="564" alt="Screenshot 2025-08-22 150359" src="https://github.com/user-attachments/assets/51914511-6054-482d-8624-fae75ca25de6" />|




# ✅ Conclusion

This project successfully implemented a GitOps pipeline using ArgoCD on a Kubernetes cluster hosted on AWS EC2 (via K3s).

It showcased:

Declarative deployments from Git

Automated sync with ArgoCD

Real-world GitOps workflow for Kubernetes




# 👨‍💻 Author

Prashant Gour

GitHub: [@TechnicalGaur](https://github.com/TechnicalGaur)




# 📄 License

This project is licensed under the MIT License.


---

