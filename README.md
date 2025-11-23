# Project : Elevating Kubernetes Micro-services with Istio

## Repo URL
https://github.com/DinukaSanjana/microservices-demo.git

## Background Concepts

### Microservices
- Product Catalog Service
- Cart Service
- Payment Service
- Shipping Service

Uses Google Cloud e-commerce application with 10 microservices.

### Kubernetes
- Container Orchestration system.

#### Parts of Kubernetes
- Container
- Pod
- Node: Uses AWS Elastic Kubernetes Service (EKS) with 3-node cluster.
- Cluster
- Service
- Deployment: Uses kubernetes-manifests.yaml.

## Istio
Service Mesh.

### How Istio Works (Sidecar Proxy)
1. Label namespace: istio-injection=enabled.
2. Application Pod gets sidecar proxy.
3. Pods show READY 2/2.
4. Traffic through sidecar proxy.

Features:
- Collect metrics.
- Encrypt traffic for security.
- Traffic management.

Focus on Observability.

## Project Steps

### Step 1: Environment Setup
- Kubernetes Cluster: AWS EKS with 3 worker nodes.
- Management Machine: EC2 instance.
- Tools: aws-cli, kubectl.
- Configure kubectl for cluster access.

### Step 2: Deploy Microservices
- Clone repo from GitHub.
- kubectl apply -f kubernetes-manifests.yaml.
- Test via frontend Load Balancer IP.

### Step 3: Istio Setup
- Download Istio.
- istioctl install.
- Label default namespace: istio-injection=enabled.

### Verify Injection
- Pods restart and show 2/2.

### Step 4: Istio Add-ons for Observability
Add-ons: Kiali, Prometheus, Grafana, Jaeger.

### Prometheus
- Monitoring system.
- Metrics Scraper: Collects requests per second, latency, CPU usage.

### Grafana
- Data visualization.
- Dashboards: Graphs, gauges.
- Pre-configured: Istio Mesh Dashboard, Service Dashboard.

## Summary
- Problem: Complex management of microservices.
- Solution: Kubernetes foundation, Istio for communication management, security, observability.
- Result: Insights without code changes, optimize system.

Elevating Kubernetes Micro-services with Istio: A Journey into Enhanced Observability.

This project showcases how Istio enhances Kubernetes microservices by improving security, routing, and observability. We demonstrate Istio's capabilities using an application, highlighting its benefits for managing microservices with better scalability and visibility. The setup includes installing Istio and deploying the application with observability tools like Kiali, Jaeger, Prometheus, and Grafana.

## Istio Architecture

### 1. Data Plane
- Sidecar Proxies.
- Istio injects Envoy proxy container into application pod.
- Application service traffic through proxy.
- Proxies send data to Control Plane.

### 2. Control Plane
- Manages and configures sidecar proxies.
- Components: Pilot, Galley, Citadel.

## Repo Structure
- microservices-demo/src/ ⇒ all the services
- microservices-demo/release/ ⇒ Kubernetes Manifest File

## Create Node Group
Create a Kubernetes cluster with three nodes as "n1", "n2", "n3".

### If Nodes are not Healthy
Step 1: Check CNI Add-on

```
aws eks list-addons --cluster-name test1
```

If vpc-cni missing, install:

```
aws eks create-addon --cluster-name test1 --addon-name vpc-cni --addon-version v1.18.2-eksbuild.1 --resolve-conflicts OVERWRITE
```

One node group creates and manages three nodes.

### 1. Node
- Worker Node: Actual server (physical or AWS EC2 instance).
- Pods run on Nodes.
