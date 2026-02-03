🚀 Blue-Green Deployment in Kubernetes
📌 Overview

Blue-Green Deployment is a deployment strategy that allows zero-downtime releases by running two versions of an application simultaneously:

Blue → current production version

Green → new version to be released

Traffic is switched instantly from Blue to Green using a Kubernetes Service selector.

❓ Why Do We Use Blue-Green Deployment?

We use Blue-Green deployment to:

✅ Achieve zero downtime

✅ Reduce deployment risk

✅ Enable instant rollback

✅ Test new versions safely in production

✅ Avoid partial or broken releases

In short:

Users never see a broken application.

⏰ When Should We Use Blue-Green Deployment?

Use Blue-Green deployment when:

You are deploying critical production applications

Downtime is not acceptable

You want safe releases

You need fast rollback

You are working in enterprise / cloud environments

🚫 Not ideal when:

Very small apps

Cost is extremely constrained (double resources needed)

🏗 Architecture Overview
User
 ↓
Ingress / LoadBalancer
 ↓
Production Service (myapp-service)
 ↓
Blue Pods OR Green Pods


Only one version receives production traffic at a time.

📁 Kubernetes Resources Used

Namespace

Blue Deployment

Green Deployment

Production Service

Blue Service (testing)

Green Service (testing)

Optional Ingress

🔵 Blue Deployment

Current live version

Receives production traffic initially

Stable and tested

🟢 Green Deployment

New application version

Deployed alongside Blue

Tested before traffic switch

🌐 Services Explained
1️⃣ Production Service (myapp-service)

Entry point for real users

Controls traffic routing

Selector decides Blue or Green

Example:

selector:
  app: myapp
  version: blue

2️⃣ Blue Service (myapp-blue-service)

Used for testing Blue version

Internal access only

No impact on users

3️⃣ Green Service (myapp-green-service)

Used for testing Green version

Internal / QA access

Safe validation before release

🔁 How Traffic Switching Works
▶ Switch Blue ➜ Green
```
kubectl patch svc myapp-service \
  -n blue-green-demo \
  -p '{"spec":{"selector":{"app":"myapp","version":"green"}}}'
```
◀ Rollback Green ➜ Blue
```
kubectl patch svc myapp-service \
  -n blue-green-demo \
  -p '{"spec":{"selector":{"app":"myapp","version":"blue"}}}'
```
✅ Instant
✅ Zero downtime
✅ No redeploy required

🚦 How to Test Green Safely in Production
✅ Method 1: Separate Service (Recommended)

Test Green without affecting users:
```
kubectl exec -it <any-pod> -n blue-green-demo -- curl myapp-green-service

```
✔ Production-safe
✔ No traffic impact

✅ Method 2: Temporary Ingress

Expose Green using a subdomain:
```
myapp.example.com        → BLUE
green.myapp.example.com → GREEN

```
>>>Traffic routes to GREEN, but the ALB does NOT change.<<<

