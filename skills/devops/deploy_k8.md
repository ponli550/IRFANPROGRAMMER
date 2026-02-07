---
name: Deploy K8
description: "standardized Kubernetes environment"
grade: A
tags: [tag1, tag2]
pros:
  - "secured"
cons:
  - "hard to set"
---

# Deploy K8

## Best Practice
(Describe the best practice pattern here)

## Snippet
<!-- JAAVIS:EXEC -->
```bash
# 1. Directory Setup

mkdir -p k8s
PROJECT_NAME=$(basename "$PWD" | tr '[:upper:]' '[:lower:]')

echo "🏗️  Scaffolding Kubernetes manifests for: $PROJECT_NAME"

# 2. Deployment Manifest
cat <<EOF > k8s/deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: $PROJECT_NAME-deployment
  labels:
    app: $PROJECT_NAME
spec:
  replicas: 2
  selector:
    matchLabels:
      app: $PROJECT_NAME
  template:
    metadata:
      labels:
        app: $PROJECT_NAME
    spec:
      containers:
      - name: $PROJECT_NAME
        image: $PROJECT_NAME:latest
        ports:
        - containerPort: 8000
EOF

# 3. Service Manifest
cat <<EOF > k8s/service.yaml
apiVersion: v1
kind: Service
metadata:
  name: $PROJECT_NAME-service
spec:
  selector:
    app: $PROJECT_NAME
  ports:
    - protocol: TCP
      port: 80
      targetPort: 8000
  type: LoadBalancer
EOF

echo "✅ Kubernetes manifests generated in k8s/"
echo "👉 Next: Run 'jaavis deploy' to apply these to your cluster."
```
