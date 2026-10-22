# OpenShift Kustomize Multi-Environment Deployment

This repository uses **Kustomize** to manage Kubernetes / OpenShift manifests across three distinct environments (`dev`, `stage`, and `prod`) using a shared `base` configuration and targeted `overlays`.

---

## 📁 Directory Structure

```text
kustomize-demo/
├── README.md
├── base/
│   ├── kustomization.yaml
│   ├── deployment.yaml
│   ├── configmap.yaml
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

graph TD
    subgraph Shared Base
        B["base/
        - deployment.yaml
        - configmap.yaml
        - service.yaml"]
    end

    B --> D
    B --> S
    B --> P

    subgraph Environment Overlays
        D["overlays/dev/
        - Replicas: 1
        - CPU: 50m/200m
        - Env: development"]
        S["overlays/stage/
        - Replicas: 2
        - CPU: 100m/500m
        - Env: staging"]
        P["overlays/prod/
        - Replicas: 3
        - CPU: 200m/1000m
        - Env: production"]
    end

    D -->|oc apply -k| CN1((Dev Namespace))
    S -->|oc apply -k| CN2((Stage Namespace))
    P -->|oc apply -k| CN3((Prod Namespace))