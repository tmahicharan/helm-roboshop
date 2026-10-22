# RoboShop Helm Charts

## Overview

This repository contains Helm charts for deploying selected RoboShop microservices to Kubernetes.

The charts currently included are:

- **Cart** — cart service, ConfigMap, Deployment, and Service.
- **Catalogue** — catalogue service, ConfigMap, Deployment, and Service.
- **MongoDB** — MongoDB Deployment and Service.
- **User** — user service, ConfigMap, Deployment, and Service.

The manifests use the `roboshop` Kubernetes namespace and include container health probes for several services.

## Repository Structure

```text
helm-roboshop/
├── cart/
│   ├── Chart.yaml
│   ├── templates/
│   │   └── manifest.yaml
│   ├── values.yaml
│   ├── values-dev.yaml
│   └── values-prod.yaml
├── catalogue/
│   ├── Chart.yaml
│   ├── templates/
│   │   └── manifest.yaml
│   ├── values.yaml
│   ├── values-dev.yaml
│   └── values-prod.yaml
├── mongodb/
│   ├── Chart.yaml
│   ├── templates/
│   │   └── manifest.yaml
│   └── values.yaml
└── user/
    ├── Chart.yaml
    ├── templates/
    │   └── manifest.yaml
    ├── values.yaml
    ├── values-dev.yaml
    └── values-prod.yaml
```

## Technologies

- Kubernetes
- Helm
- YAML
- Container images

## Prerequisites

Before deploying, prepare:

- A Kubernetes cluster and a working `kubectl` context.
- Helm CLI.
- Access to the container registries referenced in the values files.
- Correct image names and tags for each service.
- Required service dependencies and application configuration.

## Configuration

Each chart uses `values.yaml` for default settings. Some charts also provide development and production values files.

Review the image configuration, service ports, namespace, and application dependencies before deploying. Replace placeholder image tags such as `IMAGE_VERSION` with valid tags.

Do not commit credentials or other sensitive configuration to the repository.

## Validation

Validate each chart before attempting deployment:

```bash
helm lint ./cart
helm lint ./catalogue
helm lint ./mongodb
helm lint ./user
```

Render the templates for inspection:

```bash
helm template cart ./cart
helm template catalogue ./catalogue
helm template mongodb ./mongodb
helm template user ./user
```

These commands are validation examples. The charts need the configuration issues listed below to be corrected before successful rendering or deployment can be expected.

## Configuration Issues to Review

The inspected files show potential issues that should be resolved before deployment:

1. The `Chart.yaml` files for `cart`, `catalogue`, and `user` contain duplicate `apiVersion` keys. The cart and user charts also declare the name `catalogue`.
2. The catalogue image value contains a version tag while the template appends another tag.
3. The cart template uses `.values` in several expressions; Helm template values are accessed through `.Values`.
4. The user template also uses inconsistent `.values` and `values` expressions.
5. The cart development values reference the catalogue image rather than the cart image.
6. The cart production values contain catalogue/MongoDB-related settings rather than the cart settings expected from its default values.
7. The user chart's ConfigMap expects `REDIS_URL` and `MONGO_URL`, but these keys are not present in the inspected default values.
8. The catalogue chart's development values should be checked against the template's expected `owner.component`, `owner.version`, and `mongo.url` values.

These are documentation findings from inspecting the current files; they have not been corrected or validated with Helm.

## Deployment Guidance

After correcting and validating the charts, deployment can follow this general pattern:

```bash
helm upgrade --install cart ./cart \
  --namespace roboshop \
  --create-namespace \
  --values ./cart/values-dev.yaml
```

Use the corresponding chart directory and environment values file for other services. Confirm the image tags, namespace, dependencies, and environment configuration before running deployment commands.

## Security and Cost Notes

- Review container image sources and tags before deployment.
- Use least-privilege access to registries and Kubernetes resources.
- Review cloud resource costs when deploying to a managed cluster.
- Test in a development environment before using these charts in production.

## Disclaimer

This repository is intended for learning and demonstration. Validate and test all configuration in your own environment before production use.
