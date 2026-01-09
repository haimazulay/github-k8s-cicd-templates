# GitHub Kubernetes CI/CD Templates

Welcome to the **GitHub K8s CI/CD Templates** repository. This project provides a set of reusable GitHub Actions workflows ("templates") and a standard Helm chart to streamline the build, validation, and deployment of Kubernetes applications.

## 📂 Repository Structure

```
├── .github/workflows/    # Reusable CI/CD workflows (L1, L2, L3)
├── helm/
│   └── app-chart/        # Standard Helm chart for applications
└── README.md             # This documentation
```

## 🚀 Reusable Workflows

This repository exposes three "levels" of CI/CD workflows that can be consumed by other repositories using `workflow_call`.

### L1 - PR Build & Helm Package
**Path:** `.github/workflows/l1-pr-build-and-package.yml`

Designed to run on Pull Requests.
- **Builds** the Docker image (validation only, or push if configured).
- **Lints** the Helm chart.
- **Packages** the Helm chart and uploads it as an artifact.

**Inputs:**
- `image_name`: Name of the image (defaults to repository name).
- `dockerfile_path`: Path to `Dockerfile` (default: `./Dockerfile`).
- `helm_chart_dir`: Path to chart directory (default: `./helm/app-chart`).

### L2 - Push, Validate & Build
**Path:** `.github/workflows/l2-push-validate-and-build.yml`

Designed to run on push to main/feature branches.
- **Validates** the application (placeholder for tests/lints).
- **Builds and Pushes** the Docker image to a registry.

**Inputs:**
- `image_name`: Image name.
- `registry`: Container registry (default: `docker.io`).
- `dockerfile_path`: Path to `Dockerfile`.

### L3 - Merge & Deploy
**Path:** `.github/workflows/l3-merge-deploy.yml`

Designed to run on merge to `main`.
- **Builds** a local image.
- **Spins up** an ephemeral `kind` validation cluster.
- **Deploys** the application using the Helm chart.
- **Verifies** the rollout status.

**Inputs:**
- `helm_chart_dir`: Path to chart.
- `k8s_namespace`: Target namespace.
- `helm_release_name`: Release name.
- `local_image_name`: Local image name for kind loading.

## ☸️ Helm Chart

Located in `helm/app-chart`.
This is a standard application chart capable of deploying a Deployment, Service, Ingress, and basic logic.

**Verification:**
Run the following to verify the chart locally:
```bash
helm lint helm/app-chart
helm template helm/app-chart
```

## 📦 How to Use

In your application repository, create a workflow file (e.g., `.github/workflows/ci.yml`) and reference these templates:

```yaml
jobs:
  ci:
    uses: haimazulay/github-k8s-cicd-templates/.github/workflows/l1-pr-build-and-package.yml@main
    with:
      image_name: my-app
```
