# Chaos Experiment Templates

Reusable Harness Chaos Engineering experiment templates (`ChaosExperimentTemplate` / `litmuschaos.io/v1beta1`).

## Folders

| Folder | Description |
|--------|-------------|
| `k8s-templates/` | Kubernetes faults (pod delete, network loss/latency) |
| `aws-templates/` | AWS faults (AZ blackhole) |
| `cf-templates/` | Cloud Foundry faults (coming soon) |
| `vm-templates/` | VM faults (coming soon) |

Import a YAML file into Harness Chaos Engineering, provide runtime inputs, and run against your chaos infrastructure.
