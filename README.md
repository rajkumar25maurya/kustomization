# OpenShift Kustomize Multi-Environment Deployment

This repository uses Kustomize to manage a shared NGINX Deployment, ConfigMap, and Service across `dev`, `stage`, and `prod` environments. The `base/` directory contains the common manifests; each environment overlay applies its own namespace, name prefix, replica count, configuration, and resource limits.

## Directory structure

```text
.
├── README.md
├── base/
│   ├── config.yaml
│   ├── deployment.yaml
│   ├── kustomization.yaml
│   └── service.yaml
└── overlays/
    ├── dev/
    │   ├── kustomization.yaml
    │   ├── patch-config.yaml
    │   └── patch-resources.yaml
    ├── stage/
    │   ├── kustomization.yaml
    │   ├── patch-config.yaml
    │   └── patch-resources.yaml
    └── prod/
        ├── kustomization.yaml
        ├── patch-config.yaml
        └── patch-resources.yaml
```

## Environment settings

| Environment | Namespace | Name prefix | Replicas | Environment value | CPU request / limit | Memory request / limit |
|---|---|---|---:|---|---|---|
| Dev | `dev` | `dev-` | 2 | `dev` | `200m` / `450m` | `256Mi` / `550Mi` |
| Stage | `stage` | `stage-` | 3 | `stage` | `200m` / `1000m` | `256Mi` / `1Gi` |
| Prod | `prod` | `prod-` | 3 | `production` | `200m` / `1000m` | `256Mi` / `1Gi` |

Kustomize adds each overlay's name prefix to resource names. For example, the dev Deployment is named `dev-nginx`.

## Deploy an environment

Ensure the target namespace exists, then apply the overlay with `oc apply -k`:

```sh
oc apply -k overlays/dev
oc apply -k overlays/stage
oc apply -k overlays/prod
```

Use `oc apply -k` for an overlay directory. Do not use `oc apply -f overlays/<environment>/`, because that attempts to submit the Kustomization and patch files as standalone Kubernetes resources.

To inspect the rendered manifests before applying:

```sh
oc kustomize overlays/dev
```

## Kustomize configuration

The base Kustomization lists the shared resources. Each overlay references `../../base` and applies its environment-specific patches.

Use `resources:` in overlays to reference the base:

```yaml
resources:
  - ../../base
```

This is the current Kustomize form and avoids the deprecation warning associated with `bases:`.