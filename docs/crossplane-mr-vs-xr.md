# Crossplane: Managed Resource (MR) vs Composite Resource (XR)

## Overview

| Type | Description |
|------|-------------|
| **Managed Resource (MR)** | Direct 1:1 mapping to a cloud resource |
| **Composite Resource (XR)** | An abstraction that creates multiple MRs via a Composition |

---

## Managed Resource (MR)

A **direct 1:1 mapping** to a cloud resource. What you write is what gets created.

```yaml
# You write this → AWS creates exactly 1 ECS Cluster
apiVersion: ecs.aws.upbound.io/v1beta1
kind: Cluster
metadata:
  name: my-cluster
spec:
  forProvider:
    region: eu-west-3
```

- **Low-level** - you specify all the cloud details
- **No abstraction** - the provider talks directly to AWS/GCP/Azure
- **1 YAML = 1 cloud resource**

---

## Composite Resource (XR)

An **abstraction** that creates multiple MRs behind the scenes via a Composition.

```yaml
# You write this simple thing...
apiVersion: example.crossplane.io/v1
kind: App
metadata:
  name: my-app
spec:
  image: nginx:latest
```

The Composition transforms it into multiple resources:

```yaml
# Crossplane creates these MRs automatically:
- Deployment (with replicas, labels, etc.)
- Service (with ports, selectors, etc.)
- Maybe more (ConfigMaps, Ingress, etc.)
```

---

## Visual Summary

```
┌─────────────────────────────────────────────────────────┐
│                    YOU WRITE                            │
├────────────────────────┬────────────────────────────────┤
│   Managed Resource     │    Composite Resource          │
│   (MR)                 │    (XR)                        │
│                        │                                │
│   ECS Cluster YAML     │    App YAML (simple)           │
│         │              │         │                      │
│         ▼              │         ▼                      │
│   ┌──────────┐         │    ┌───────────┐               │
│   │ Provider │         │    │Composition│               │
│   └────┬─────┘         │    └─────┬─────┘               │
│        │               │          │                     │
│        ▼               │          ▼                     │
│   1 ECS Cluster        │    Multiple MRs:               │
│   in AWS               │    - Deployment                │
│                        │    - Service                   │
│                        │    - etc.                      │
└────────────────────────┴────────────────────────────────┘
```

---

## Analogy: McDonald's Order

| Crossplane Concept | McDonald's Analogy |
|--------------------|--------------------|
| **XR** (Composite Resource) | Ordering "1 Big Mac" |
| **Composition** | The assembly instructions McDonald's follows |
| **MRs** (Managed Resources) | Bun, patties, lettuce, sauce, etc. that get assembled |
| **XRD** (Definition) | The menu item definition (Big Mac exists, you can customize size) |

### Another Analogy: Cooking

| Crossplane Concept | Cooking Analogy |
|--------------------|-----------------|
| **XR** | The order: "1 pancake please" |
| **Composition** | The recipe that transforms ingredients into a pancake |
| **MRs** | The actual ingredients: flour, eggs, milk |
| **XRD** | The menu (defines what you can order and options like "add chocolate chips") |

---

## When to Use Which?

| Use Case | Recommendation |
|----------|----------------|
| Simple, one-off resource | **MR** |
| Platform team building abstractions for dev teams | **XR + Composition** |
| Hiding cloud complexity from developers | **XR + Composition** |
| Creating multiple related resources together | **XR + Composition** |
| Direct control over every field | **MR** |

---

## Key Components for XR

To use Composite Resources, you need:

1. **XRD** (CompositeResourceDefinition) - Defines the schema/API for your abstraction
2. **Composition** - Defines what MRs get created when an XR is requested
3. **XR** (Composite Resource) - The actual instance you create

---

## Useful Commands

```bash
# For Managed Resources - dry run
kubectl apply --dry-run=server -o yaml -f my-mr.yaml

# For Composite Resources - render what would be created
crossplane render my-xr.yaml composition.yaml functions.yaml
```
