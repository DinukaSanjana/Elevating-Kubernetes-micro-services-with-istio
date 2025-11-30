# Project: Elevating Kubernetes Micro-services with Istio

## Repository
https://github.com/DinukaSanjana/microservices-demo.git

<!--
## Problem Fixing References
- https://gemini.google.com/share/4b9c80f17000  
- https://chatgpt.com/share/69099c4f-8fb4-800a-8c21-0a4057e88d88  
- https://gemini.google.com/share/6c89bde39e4f
-->

## What are Microservices?
Small, independent services that each perform a single business function and communicate over APIs.

### E-commerce Website: Monolith vs Microservices Architecture
| Monolith Architecture                               | Microservices Architecture                                      |
|-----------------------------------------------------|-----------------------------------------------------------------|
| Entire app in one codebase                          | App split into many small, independent services                |
| One failure can crash the whole site                | One service failure doesn’t affect others                      |
| Hard to scale individual parts                      | Scale only the services that need it                           |
| Deployment takes longer                             | Faster, independent deployments                                 |

Highest count of microservices cannot be managed manually → Need automation and orchestration → Kubernetes + Istio.

## Kubernetes Core Concepts
```
Cluster
└── Node (Real computer – EC2 Instance / VM)
    └── Pod (Smallest deployable unit in Kubernetes)
        └── Container(s)
```

- **Container** → Packaged application + dependencies  
- **Pod** → One or more containers that always run together (smallest unit)  
- **Node** → Physical or virtual machine (EC2 instance)  
- **Cluster** → Group of nodes managed by Kubernetes control plane  

Pod is the smallest unit of Kubernetes. One Pod can have multiple containers.

## What is Istio & Why Use It in DevOps?
**Istio** is an open-source **Service Mesh** that acts as a dedicated communication layer between microservices.

### Why Istio?
- Secure service-to-service communication (mTLS)  
- Advanced traffic control (routing, retries, timeouts, canary)  
- Deep observability without changing application code  
- Centralized policy enforcement  

### How Istio Works – Sidecar Proxy Pattern
- No code changes required  
- Enable sidecar injection: `kubectl label namespace default istio-injection=enabled`  
- Istio automatically injects an **Envoy proxy container** as a **sidecar** into every pod  
- Pod becomes: `[Application Container] + [Istio Sidecar Proxy]`  
- All inbound/outbound traffic flows through the sidecar  
- Sidecar collects metrics, enforces policies, encrypts traffic  

```bash
kubectl get pods
# Shows READY 2/2 → Application container + Sidecar proxy
```

## Step-by-Step Implementation

### 1. Create EKS Cluster + Node Group
- Create Kubernetes cluster (AWS EKS)  
- Create one node group with 3 nodes (n1, n2, n3)

### 2. Management EC2 Instance Setup
```bash
# Create EC2 instance
# Install tools
sudo apt install -y aws-cli kubectl

# Configure AWS credentials
aws configure
```

### 3. Connect to EKS Cluster
```bash
aws eks update-kubeconfig --region us-east-1 --name my_cluster
# Reason: Updates ~/.kube/config so kubectl can talk to your EKS cluster
```

```bash
kubectl get nodes
# Reason: Verify you can see the 3 worker nodes
```

### 4. Install Istio (Using Official Documentation)
```bash
curl -L https://istio.io/downloadIstio | sh -
cd istio-*
export PATH=$PWD/bin:$PATH
istioctl install --set profile=demo -y
# Reason: Installs Istio control plane with demo profile (includes add-ons)
```

### 5. Enable Automatic Sidecar Injection
```bash
kubectl label namespace default istio-injection=enabled --overwrite
# Reason: All new pods in default namespace will get Istio sidecar injected
```

### 6. Deploy the Microservices Demo Application
```bash
git clone https://github.com/DinukaSanjana/microservices-demo.git
cd microservices-demo/release
kubectl apply -f kubernetes-manifests.yaml
# Reason: Deploys all 10+ microservices + frontend
```

```bash
kubectl get pods
# After restart → shows 2/2 containers (app + sidecar)
```

```bash
kubectl get svc
# Copy frontend-external LoadBalancer URL → Open in browser
# Reason: Test the e-commerce app is working
```

### 7. Install Observability Add-ons (Kiali, Prometheus, Grafana, Jaeger)
```bash
cd ~/istio-*/samples/addons
kubectl apply -f .
# Reason: Deploys Kiali, Prometheus, Grafana, Jaeger in istio-system namespace
```

```bash
kubectl get pods -n istio-system
# Reason: Verify add-ons are running
```

### 8. Expose Kiali Dashboard
```bash
kubectl patch svc kiali -n istio-system -p '{"spec":{"type":"LoadBalancer"}}'
# Reason: Changes Kiali service type to LoadBalancer → gets public URL
```

→ Open Kiali LoadBalancer URL : `20001` → See full service mesh graph

### 9. Expose Prometheus
```bash
kubectl edit svc prometheus -n istio-system
# Change type: ClusterIP → NodePort
# Save & quit
```

```bash
kubectl get svc -n istio-system
# Look for prometheus → shows port like 9090:32XXX
```

→ Open any worker node public IP : `32XXX` → Prometheus UI

**Security Group Rule** (on EKS worker nodes SG):  
Allow Custom TCP → Port `32XXX` → Source: Anywhere

### 10. Expose Grafana
```bash
kubectl edit svc grafana -n istio-system
# Change type: ClusterIP → NodePort
# Save & quit
```

```bash
kubectl get svc -n istio-system
# Look for grafana → shows port like 3000:32YYY
```

→ Open any worker node public IP : `32YYY` → Grafana (default login: admin / admin)

**Security Group Rule**: Allow Custom TCP → Grafana NodePort → Anywhere

## Summary – What You Achieve
- Running production-grade microservices on EKS  
- Zero code changes with full Istio service mesh  
- Automatic mTLS, traffic management, and observability  
- Visual service mesh with Kiali  
- Metrics with Prometheus  
- Beautiful dashboards with Grafana  

**Project Outcome**: A fully observable, secure, and resilient microservices architecture using Kubernetes + Istio.
