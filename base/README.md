# OpenShift Kustomize Multi-Environment Deployment

This repository uses **Kustomize** to manage Kubernetes / OpenShift manifests across three distinct environments (`dev`, `stage`, and `prod`) using a shared `base` configuration and targeted `overlays`.

---

## 📁 Directory Structure & Architecture

````text
.
├── README.md
├── kustomization.yaml
├── deployment.yaml
├── config.yaml
├── service.yaml
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

```mermaid
graph TD
    B["Shared manifests<br/>Deployment, ConfigMap, Service"]

    B --> D["dev<br/>Namespace: dev-ns<br/>Replicas declared: 3<br/>CPU request/limit: 200m / 450m<br/>Environment: dev"]
    B --> S["stage<br/>Namespace: stage-ns<br/>Replicas declared: 3<br/>CPU request/limit: 200m / 400m<br/>Environment: stage"]
    B --> P["prod<br/>Namespace: prod-ns<br/>Replicas declared: 3<br/>CPU request/limit: 200m / 1000m<br/>Environment: production"]

    D -->|oc apply -k| DN((Dev Namespace))
    S -->|oc apply -k| SN((Stage Namespace))
    P -->|oc apply -k| PN((Prod Namespace))
```