# DemoHelm

A quick-start guide to Helm concepts, chart structure, and managing application releases on Kubernetes.

---

## What Is Helm?

- **Without Helm:** Managing applications on Kubernetes requires writing and updating static YAML manifests for every environment. Changing an image tag, replica count, or port across staging and production means manually finding and replacing values across multiple files.
- **With Helm:** Helm serves as both a **package manager** and a **templating engine** for Kubernetes. You define resources once with dynamic placeholders, provide values from a central configuration file, and manage releases with unified commands for installing, upgrading, and rolling back.

---

## Core Terminology

- **Chart:** A directory containing resource definitions, metadata, and default values required to run an application on Kubernetes. Think of it as a reusable software blueprint or package.
- **Values (`values.yaml`):** Configuration variables passed into chart templates. This separates parameters such as replica counts and ports from infrastructure logic.
- **Release:** A specific running instance of a chart in a Kubernetes cluster. You can deploy the same chart multiple times with different values; each instance is its own release.
- **Revision:** A snapshot of a release. Every upgrade creates a new revision, allowing you to view deployment history or revert changes.
- **Template (`templates/`):** Kubernetes YAML files containing Go template syntax (for example, `{{ .Values.replicaCount }}`) that render into manifests when combined with values.
- **Helm Lint (`helm lint`):** A static analysis command that checks a chart's structure and templates for errors before deployment.
- **Dry Run (`--dry-run`):** A simulation mode that renders templates without creating or changing cluster resources.
- **Port Forwarding:** A local networking bridge created by `kubectl` to route traffic from your machine to a service or pod inside the cluster.

---

## Chart Directory Structure

```text
DemoHelm/
├── Chart.yaml          # Chart metadata (name, version, description)
├── values.yaml         # Default configuration values
└── templates/          # Manifest blueprints
    ├── deployment.yaml # Application workload specification
    └── service.yaml    # Network routing definition
```

---

## Step-by-Step Instructions

### 1. Validate and Preview

Run these commands from your `DemoHelm` chart directory.

Check for syntax or structural errors:

```bash
helm lint .
```

Preview the rendered Kubernetes manifests:

```bash
helm template demohelm .
```

Simulate an installation:

```bash
helm install demohelm . --dry-run
```

### 2. Deploy to the Cluster

Install the chart to create the release:

```bash
helm install demohelm .
```

### 3. Verify the Deployment

List all active Helm releases:

```bash
helm list
```

Check pods and services associated with the release:

```bash
kubectl get pods -l app=demohelm
kubectl get svc demohelm-service
```

Test application access with local port forwarding:

```bash
kubectl port-forward svc/demohelm-service 8080:80
```

Navigate to <http://localhost:8080> in your browser or run `curl http://localhost:8080` in another terminal. Press `Ctrl+C` to stop port forwarding.

### 4. Upgrade and Roll Back

Manage the lifecycle of the running application without editing files directly on disk.

Upgrade the release with a value override (for example, to scale replicas):

```bash
helm upgrade demohelm . --set replicaCount=4
```

Inspect the release history and revisions:

```bash
helm history demohelm
```

Roll back to a previous revision (for example, revision 1):

```bash
helm rollback demohelm 1
```

### 5. Uninstall

Uninstall the release and delete its related Kubernetes resources:

```bash
helm uninstall demohelm
```
