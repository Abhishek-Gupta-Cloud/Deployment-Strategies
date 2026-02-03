# 🚀 Canary Deployment using Kubernetes (Production Ready)

This repository demonstrates a **production-level Canary Deployment strategy in Kubernetes** using **pure Kubernetes resources** (without service mesh or third-party tools).

The goal is to safely release a new application version to a small subset of users, monitor it, and then gradually roll it out to all users.

---

## 📌 What is Canary Deployment?

Canary deployment is a release strategy where:
- A **new version (v2)** is deployed alongside the **stable version (v1)**
- Only a **small percentage of traffic** is routed to the new version
- If the new version is stable, traffic is increased gradually
- If issues occur, an **instant rollback** is performed

This approach minimizes risk in production environments.

---

## 🏗️ Architecture Overview

Users
|
Ingress
|
Service (Single Service)
|
| Pods v1 | Pods v2 |

- Traffic distribution is controlled using **replica count**
- Kubernetes Service load-balances requests across all Pods

---

## 📁 Project Structure

Canary-Deployment/
├── Namespace.yml
├── ConfigMap.yml
├── Secret.yml
├── Canary_deployment-v1.yml
├── Canary_deployment-v2.yml
├── Service.yml
├── HorizontalPod_auto_Scaler.yml
├── ingress.yml
└── Readme.md


---

## 📄 File Descriptions

### `Namespace.yml`
Creates an isolated Kubernetes namespace for production workloads.

### `ConfigMap.yml`
Stores non-sensitive configuration values such as environment variables.

### `Secret.yml`
Stores sensitive information like passwords and tokens.

### `Canary_deployment-v1.yml`
Stable version of the application that serves most of the traffic.

### `Canary_deployment-v2.yml`
Canary version of the application that serves a small portion of traffic.

### `Service.yml`
Single Kubernetes Service that load-balances traffic between v1 and v2 Pods.

### `HorizontalPod_auto_Scaler.yml`
Automatically scales Pods based on CPU utilization.

### `ingress.yml`
Exposes the application externally using Ingress and domain-based routing.

---

## 🚀 Deployment Steps

### 1️⃣ Clone the repository
```
git clone <your-repo-url>
```
cd Canary-Deployment
2️⃣ Apply all Kubernetes manifests
```
kubectl apply -f .

Kubernetes automatically handles resource dependencies.

🔍 Verify Deployment
```
kubectl get all -n production
kubectl get ingress -n production
kubectl get hpa -n production
```
🔁 Canary Promotion Strategy
Stage	  v1 Replicas	v2 Replicas
Initial  	9	          1
Step 1	    7	          3
Step 2	    5	          5
Final	    0	          10
Apply changes by updating replica counts and running:

```
kubectl apply -f .
```
❌ Rollback Strategy
If the canary version fails:
```
kubectl scale deployment app-v2 -n production --replicas=0
```
All traffic is immediately routed back to the stable version.

⚠️ Important Notes
Kubernetes does not support true percentage-based traffic splitting by default

Replica-based traffic distribution is a basic canary approach

For advanced canary deployments, tools like Argo Rollouts or service meshes are used

🎯 Use Cases
Production application releases

Microservices deployments

High-availability systems

Zero-downtime upgrades