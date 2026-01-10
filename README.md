# GitHub Kubernetes CI/CD Templates

[![Helm Chart](https://img.shields.io/badge/Helm-3.0-blue?logo=helm)](https://helm.sh)
[![GitHub Actions](https://img.shields.io/badge/GitHub_Actions-CI-2088FF?logo=github-actions)](https://github.com/features/actions)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

Welcome to the **GitHub K8s CI/CD Templates** repository. This project provides a set of **battle-tested, reusable GitHub Actions workflows** and a **standard Helm chart** to streamline the build, validation, and deployment of Kubernetes applications.

## 🌟 Why use this?
- **GitHub Native**: Built for **GHCR** (GitHub Container Registry). No extra secrets or credentials needed—just `GITHUB_TOKEN`.
- **Standardization**: Enforce consistent build and deployment logic across all microservices.
- **Best Practices**: Built-in support for **Docker caching**, **timeouts**, and **Helm linting**.

## 📂 Repository Structure

```
├── .github/workflows/    # Reusable CI/CD workflows (L1, L2, L3)
├── helm/
│   └── app-chart/        # Standard Helm chart for applications (w/ JSON Schema)
└── README.md             # This documentation
```

## 🚀 Reusable Workflows

### L1 - PR Build & Helm Package
**Path:** `.github/workflows/l1-pr-build-and-package.yml`
*Triggers on*: Pull Request.
- **Builds** the Docker image (validation only, no push).
- **Lints** the Helm chart.
- **Packages** the chart as an artifact.

### L2 - Push, Validate & Build
**Path:** `.github/workflows/l2-push-validate-and-build.yml`
*Triggers on*: Push to branches.
- **Validates** application code.
- **Builds and Pushes** image to **GHCR**.
- **Auth**: Uses `GITHUB_TOKEN` automatically.
- **Inputs**:
    - `image_name`: defaults to repo name.
    - `dockerfile_path`: defaults to `./Dockerfile`.

### L3 - Merge & Deploy
**Path:** `.github/workflows/l3-merge-deploy.yml`
*Triggers on*: Merge to Main.
- **Deploys** to ephemeral `kind` cluster (validation) or real cluster.
- **Features**: Deployment concurrency lock, Rollout status check.

## ☸️ Helm Chart
Located in `helm/app-chart`.
Includes a `values.schema.json` to automatically validate configuration inconsistencies.

**Verification:**
```bash
# Lint the chart
helm lint helm/app-chart

# Render templates
helm template helm/app-chart
```

## 📦 How to Use
In your service repository, create `.github/workflows/ci.yml`:

```yaml
name: CI
on:
  push:
    branches: [ "main" ]

jobs:
  build:
    uses: haimazulay/github-k8s-cicd-templates/.github/workflows/l2-push-validate-and-build.yml@main
    permissions:
      contents: read
      packages: write # Required for GHCR push
    with:
      image_name: my-app
```
