# 📦 k8s-manifests

> Kubernetes manifests for the GitOps Zero Touch Deployment project.
> This repo is watched by ArgoCD — any change here automatically
> updates the cluster. No manual kubectl commands needed.

## 🔗 Main Project

👉 [infra-gitops](https://github.com/Akioye/infra-gitops) — App code, Terraform, and CI/CD pipelines

## 🏗️ Structure

```
k8s-manifests/
├── apps/
│   ├── staging/
│   │   ├── deployment.yaml    ← Updated automatically by GitHub Actions
│   │   ├── service.yaml       ← LoadBalancer for staging URL
│   │   └── sealed-secret.yaml ← Encrypted secrets safe to commit
│   └── production/
│       ├── deployment.yaml    ← Updated manually when promoting
│       └── service.yaml       ← LoadBalancer for production URL
└── argocd/
    ├── staging.yaml           ← ArgoCD app watching staging folder
    └── production.yaml        ← ArgoCD app watching production folder
```

## 🔄 How It Works

**Staging** — updates automatically:

```
Developer pushes code to infra-gitops
        ↓
GitHub Actions builds image → pushes to ECR
        ↓
GitHub Actions updates apps/staging/deployment.yaml
        ↓
ArgoCD detects change → syncs staging cluster
```

**Production** — promoted manually:

```
You test staging — looks good?
        ↓
Update apps/production/deployment.yaml with same image tag
        ↓
git push
        ↓
ArgoCD detects change → syncs production cluster
```

## 🔁 Rollback

```bash
# Undo last deployment
git revert HEAD
git push

# ArgoCD auto-reverts the cluster automatically
```

## 🔐 Secrets

Secrets are encrypted using Sealed Secrets.
Safe to commit — only the cluster can decrypt them.
Never commit plain text passwords or API keys here.

```bash
# To create a new sealed secret
kubeseal --format yaml < mysecret.yaml > apps/staging/sealed-secret.yaml
```

## 🔗 Related

- [infra-gitops](https://github.com/Akioye/infra-gitops) — Main project repo with app code, Terraform, and GitHub Actions workflows
