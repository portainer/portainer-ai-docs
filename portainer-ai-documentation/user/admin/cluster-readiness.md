# Cluster Readiness

Cluster Readiness is an admin-only page that checks each Kubernetes environment for the prerequisites Portainer-Run deployments depend on, and gives administrators a way to remove an environment from self-service entirely.

## What it checks

For each environment, Cluster Readiness reports on:

* **Ingress controller availability**
* **LoadBalancer provisioning**
* **Storage class configuration**
* **Node health**
* **GPU node availability**

Checks run when you click **Run checks**. Each check reports a plain-English result, so an administrator doesn't need to interpret raw cluster state to know whether an environment is actually ready for business builders to deploy onto.

## Disabling an environment

Administrators can disable an environment directly from this page. Once disabled:

* It's hidden from the environment dropdowns and Applications views, for admins as well as non-admin users.
* The `list_environments` [MCP](../mcp.md) tool no longer returns it.

This is the mechanism to use if a cluster isn't ready yet (missing storage class, no ingress controller) or shouldn't be self-serve for policy reasons, rather than relying on users to know not to deploy there.

## Where this state lives

Disabled state is stored in a ConfigMap named `portainer-run-config` and persists across Portainer-Run restarts.

## How this interacts with MCP

The `list_environments` MCP tool excludes any environment an admin has disabled here, so an AI coding tool deploying via [MCP](../mcp.md) sees the same restricted environment list a human user would see in the UI.
