# WebLogic Kubernetes Operator (WKO) GitOps Infrastructure

Enterprise-ready GitOps repository implementing automated continuous deployment of Oracle WebLogic Kubernetes Operator (WKO) on Google Kubernetes Engine (GKE) via ArgoCD, integrated with External Secrets Management (ESM/ESO) for secure zero-trust credential provisioning.

---

## 🏛 Architecture Overview

```
                      +-----------------------------+
                      |   GitHub Repository         |
                      |   (wko-gitops-infra)        |
                      +--------------+--------------+
                                     |
                                     | GitOps Polling / Webhook
                                     v
                      +-----------------------------+
                      |       ArgoCD Server         |
                      |     (argocd namespace)      |
                      +--------------+--------------+
                                     |
              +----------------------+----------------------+
              |                                             |
              v                                             v
+-------------------------------+             +-------------------------------+
|  External Secrets Operator    |             |  WebLogic Kubernetes Operator |
|  (external-secrets namespace) |             |  (opns namespace)             |
+---------------+---------------+             +---------------+---------------+
                |                                             |
                | Pulls secrets from                          | Watches Domain CRDs
                | Akeyless / GCP SM                           |
                v                                             v
+-------------------------------+             +-------------------------------+
|   K8s Secret                  |             | WebLogic Domain (WLS Cluster) |
|   (weblogic-credentials)      +------------>| (weblogic-domains namespace)  |
+-------------------------------+             +-------------------------------+
```

---

## 📂 Repository Structure

```text
.
├── .github/
│   └── workflows/
│       └── lint.yml                    # Automated GitHub Actions YAML validation
├── bootstrap/                          # ArgoCD Application manifests
│   ├── root-app-of-apps.yaml          # Enterprise App of Apps root manifest
│   ├── weblogic-operator-app.yaml     # ArgoCD app for WKO
│   ├── external-secrets-app.yaml      # ArgoCD app for External Secrets Operator
│   └── weblogic-domain-app.yaml       # ArgoCD app for WebLogic Domain
├── operators/
│   └── weblogic-operator/
│       ├── Chart.yaml                 # Oracle WKO Helm dependency definition
│       └── values.yaml                # Production configuration values
├── external-secrets/
│   ├── Chart.yaml                 # ESO Helm dependency definition
│   └── values.yaml                # ESO values and CRD flags
├── secrets/
│   ├── weblogic-secret-store.yaml     # SecretStore definitions (Akeyless & GCP SM)
│   └── weblogic-admin-credentials.yaml# ExternalSecret declaration
└── domains/
    └── sample-domain/
        └── domain.yaml                # Oracle WebLogic Domain CRD specification
```

---

## 🚀 Deployment Instructions

### 1. Bootstrap via ArgoCD

Apply the WebLogic Operator application directly to ArgoCD:
```bash
kubectl apply -f bootstrap/weblogic-operator-app.yaml
```

Or deploy all components simultaneously using the Enterprise **App of Apps** pattern:
```bash
kubectl apply -f bootstrap/root-app-of-apps.yaml
```

### 2. Verify Deployment

Check WebLogic Operator pod status:
```bash
kubectl get pods -n opns
```

Check ArgoCD application status:
```bash
kubectl get applications -n argocd
```
