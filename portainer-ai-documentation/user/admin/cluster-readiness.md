# Cluster Readiness

Cluster Readiness is an admin-only page that checks each Kubernetes environment for the prerequisites Portainer-Run deployments depend on, and gives administrators a way to remove an environment from self-service entirely.

## What it checks

For each environment, Cluster Readiness reports on:

* **Ingress controller availability**
* **LoadBalancer provisioning**
* **Storage class configuration**
* **Node health**
* **GPU node availability**

Each check reports a plain-English result, so an administrator doesn't need to interpret raw cluster state to know whether an environment is actually ready for business builders to deploy onto.

## Disabling an environment

Administrators can disable an environment directly from this page. Once disabled:

* It's hidden from all dropdowns and views for non-admin users.
* It's blocked from receiving new deployments for **everyone**, including admins.
* Non-admin users see a notice on [Applications](../applications.md) if environments have been hidden from them.

This is the mechanism to use if a cluster isn't ready yet (missing storage class, no ingress controller) or shouldn't be self-serve for policy reasons, rather than relying on users to know not to deploy there.

## Where this state lives

Disabled state is stored in a ConfigMap (`portainer-run-config`, in the `kube-system` namespace) and persists across Portainer-Run restarts. On startup, the server tries each connected environment in turn to find one with access to `kube-system`. The first that succeeds is used for both reads and writes of this state.

## How this interacts with MCP

The `list_environments` MCP tool excludes any environment an admin has disabled here, so an AI coding tool deploying via [MCP](../mcp.md) sees the same restricted environment list a human user would see in the UI.
