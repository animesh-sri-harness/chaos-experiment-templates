# Chaos Experiment Templates

Reusable [Harness Chaos Engineering](https://developer.harness.io/docs/chaos-engineering/) experiment templates for validating application resilience across Kubernetes, AWS, Cloud Foundry, and virtual machine environments.

Each template is a `ChaosExperimentTemplate` resource (`litmuschaos.io/v1beta1`) that defines faults, probes, actions, and experiment flow. Import a template into Harness, provide runtime inputs, and run the experiment against your infrastructure.

## Repository Structure

| Folder | Description | Status |
|--------|-------------|--------|
| [`k8s-templates/`](k8s-templates/) | Kubernetes workload faults (pod delete, network loss/latency) | Templates available |
| [`aws-templates/`](aws-templates/) | AWS infrastructure faults (availability zone blackhole) | Templates available |
| [`cf-templates/`](cf-templates/) | Cloud Foundry application and platform faults | Coming soon |
| [`vm-templates/`](vm-templates/) | Virtual machine and host-level faults | Coming soon |

## Available Templates

### Kubernetes (`k8s-templates/`)

| Template | Fault | Description |
|----------|-------|-------------|
| [`pod-delete.yaml`](k8s-templates/pod-delete.yaml) | `pod-delete` | Deletes pods for a target workload to test recovery, restart policies, and high availability. |
| [`pod-network-loss.yaml`](k8s-templates/pod-network-loss.yaml) | `pod-network-loss` | Injects packet loss on pod network interfaces to validate timeout handling and retry logic. |
| [`pod-network-latency.yaml`](k8s-templates/pod-network-latency.yaml) | `pod-network-latency` | Adds network latency to pods to test degraded-network behavior and SLO compliance. |

**Common runtime inputs:** target workload kind, namespace, names, and fault-specific parameters (e.g. `NETWORK_PACKET_LOSS_PERCENTAGE`, `NETWORK_LATENCY`, `PODS_AFFECTED_PERC`).

### AWS (`aws-templates/`)

| Template | Fault | Description |
|----------|-------|-------------|
| [`az-blackhole.yaml`](aws-templates/az-blackhole.yaml) | `az-blackhole` | Simulates an availability zone outage by blocking traffic to specified AZs in a VPC, testing multi-AZ failover and regional resilience. |

**Common runtime inputs:** AWS region, VPC IDs, availability zones, and optionally an IAM role ARN (`ASSUME_ROLE_ARN`) for cross-account execution.

## Template Conventions

All templates in this repository follow a consistent structure:

- **Faults** — The chaos injection to apply (e.g. pod delete, network loss, AZ blackhole).
- **Probes** — Health and SLO validation steps. Current templates include Nobl9 SLO probes for before/after fault measurement.
- **Actions** — Supporting steps such as `k8s-delay` to pause between probe and fault phases.
- **Vertices** — Experiment graph defining execution order: baseline probe → delay → fault → recovery probe.
- **Variables** — Experiment-level inputs referenced by probes and faults (e.g. Nobl9 SLO configuration).

Values marked with `<+input>` are supplied at experiment run time in Harness. Values bound to `<+experiment.variables.*>` are resolved from the template's `variables` section.

## Usage

1. **Import** — In Harness Chaos Engineering, create or update a chaos experiment and import the desired YAML template (or sync from this repository if connected via Git).
2. **Configure** — Provide runtime inputs for target workloads, fault parameters, and probe variables (e.g. Nobl9 SLO name, project, objective, metric, timeframe).
3. **Select infrastructure** — Attach the experiment to a Kubernetes or AWS chaos infrastructure that matches the template's `infraType`.
4. **Run** — Execute the experiment in a non-production or controlled environment first. Review probe results and SLO impact before promoting to broader use.

## Adding Templates

Place new experiment YAML files in the appropriate folder:

- Kubernetes faults → `k8s-templates/`
- AWS faults → `aws-templates/`
- Cloud Foundry faults → `cf-templates/`
- VM / host faults → `vm-templates/`

Use existing templates as a reference for naming, probe wiring, and variable patterns.

## Requirements

- Harness Chaos Engineering (enterprise faults such as `pod-delete`, `pod-network-loss`, and `az-blackhole` require appropriate licensing).
- Configured chaos infrastructure (Kubernetes cluster with chaos delegate, or AWS connector with required IAM permissions).
- Nobl9 integration configured if using the included SLO probes (probe blocks can be removed or replaced if not needed).
