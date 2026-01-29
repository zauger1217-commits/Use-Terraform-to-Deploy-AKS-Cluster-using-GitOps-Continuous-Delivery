# ☁️ AKS GitOps Lab with Terraform & Argo CD 🚀

## 📌 Overview

This lab demonstrates how to deploy an **Azure Kubernetes Service
(AKS)** cluster using **Terraform**, deploy applications using
**Kubernetes manifests**, and manage continuous delivery using **Argo CD
(GitOps)**.

The goal of this project was to gain hands-on experience with: -
Infrastructure as Code (IaC) - Kubernetes fundamentals - GitOps
workflows - Real-world troubleshooting in Azure

------------------------------------------------------------------------

## 🏗️ Architecture

☁️ **Azure Subscription**\
→ 📦 **Resource Group**\
→ ☸️ **AKS Cluster (1 Node)**\
→ 🐳 **NGINX Deployment (3 → 5 Pods)**\
→ 🔁 **Argo CD managing state from GitHub**

------------------------------------------------------------------------

## 📂 Repository Structure

    .
    ├── main.tf
    ├── providers.tf
    ├── outputs.tf
    ├── .gitignore
    ├── README.md
    └── manifests/
        └── nginx/
            ├── deployment.yaml
            └── service.yaml

------------------------------------------------------------------------

## ⚙️ Technologies Used

-   ☁️ Microsoft Azure
-   ☸️ Azure Kubernetes Service (AKS)
-   🧱 Terraform
-   🔄 Argo CD
-   🐳 Kubernetes
-   🧪 kubectl
-   🐙 GitHub

------------------------------------------------------------------------

## 🚀 Deployment Steps

### 1️⃣ Deploy AKS with Terraform

``` bash
terraform init
terraform plan
terraform apply
```

⚠️ **Note:** Azure required registering the `Microsoft.ContainerService`
provider and selecting an allowed VM SKU due to quota restrictions.

------------------------------------------------------------------------

### 2️⃣ Configure kubectl

``` bash
az aks get-credentials --resource-group example-resources --name example-aks1
kubectl get nodes
```

------------------------------------------------------------------------

### 3️⃣ Deploy NGINX Manually (Initial Validation)

``` bash
kubectl create deployment nginx --image=nginx --replicas=3
```

This step validated cluster functionality before moving to GitOps.

------------------------------------------------------------------------

### 4️⃣ Convert to Declarative YAML

The deployment was exported and cleaned to remove runtime fields.

📄 `deployment.yaml`

``` yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
        - name: nginx
          image: nginx:1.25
          ports:
            - containerPort: 80
```

📄 `service.yaml`

``` yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-svc
spec:
  type: ClusterIP
  selector:
    app: nginx
  ports:
    - port: 80
      targetPort: 80
```

------------------------------------------------------------------------

### 5️⃣ Install Argo CD

``` bash
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

Access UI:

``` bash
kubectl port-forward svc/argocd-server -n argocd 8080:443
```

------------------------------------------------------------------------

### 6️⃣ Configure Argo CD Application

-   Repository: GitHub repo
-   Path: `manifests/nginx`
-   Revision: `HEAD`
-   Sync Policy: Automated

------------------------------------------------------------------------

### 7️⃣ GitOps in Action: Scaling Pods

Edit:

``` yaml
replicas: 5
```

Then:

``` bash
git add manifests/nginx/deployment.yaml
git commit -m "Scale nginx from 3 to 5 replicas"
git push
```

🟢 Argo CD automatically synced and scaled the application to **5
pods**.

------------------------------------------------------------------------

## 🛠️ Issues Encountered & Fixes

### ❌ kubectl not recognized

✔️ Fixed by restarting VS Code to reload PATH.

### ❌ AKS provider not registered

✔️ Registered `Microsoft.ContainerService`.

### ❌ VM SKU not allowed / quota exceeded

✔️ Switched VM size to `Standard_D2_v3`.

### ❌ Argo CD not syncing changes

✔️ Fixed Application path from `(root)` → `manifests/nginx`.

### ❌ Git push rejected

✔️ Resolved using `git pull --allow-unrelated-histories`.

------------------------------------------------------------------------

## 🧠 Core Concepts Learned

-   Infrastructure as Code (Terraform)
-   Kubernetes Deployments, Pods, Services
-   GitOps principles
-   Declarative vs Imperative workflows
-   Continuous Delivery with Argo CD
-   Azure quotas, SKUs, and regions

------------------------------------------------------------------------

## 🧪 Skills Demonstrated

✅ Azure AKS provisioning\
✅ Terraform IaC\
✅ Kubernetes workload deployment\
✅ Git & GitHub version control\
✅ GitOps continuous delivery\
✅ Troubleshooting real cloud issues

------------------------------------------------------------------------

## 📈 Next Steps

-   Add Ingress Controller 🌐
-   Deploy multiple applications
-   Implement Helm charts
-   Enable RBAC and security hardening 🔐

------------------------------------------------------------------------

## 🙌 Final Notes

This lab simulates real-world cloud engineering workflows and
demonstrates how Git can serve as the **single source of truth** for
Kubernetes deployments using Argo CD.
