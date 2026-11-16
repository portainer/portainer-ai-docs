# Overview

This section covers how Portainer-Command is built, and how it relates to Portainer and your Git provider. The short version: the agent never holds a credential that can change a cluster, and the only way anything it does reaches a cluster is as a change request a person approves.

## The high-level flow

```
Agent (AI Workspace, or your own MCP client)
  → Portainer-Command (MCP server, git endpoint, policy)
    → reads:   Portainer's Kubernetes proxy (read-only, scoped, expiring)
    → changes: a change request on your Git host (authored as the agent's owner)
      → a person approves in Portainer-Command
        → Portainer, Argo CD, or Flux (delivers the merged change)
          → Kubernetes
```

## The three pieces

Portainer-Command is made of three parts.

1. **An MCP server.** Six tools, none of which can change a cluster. Live state comes from a temporary read-only credential, desired state comes from a `git clone` of the environment's own directory, and the one tool shaped like a write opens a change request. See [MCP tools](mcp.md).
2. **AI Workspaces.** A hosted agent per user, run in your own cluster, already connected to the MCP server, with Portainer-Command rendering the chat. See [AI Workspace](../using-portainer-command/workspace/workspace.md).
3. **An administration plane.** Per-environment policy, the approval gate, a fleet-wide timeline, spend tracking, halt, and rollback. See [Administration](../using-portainer-command/admin/).

## Reading live state

When an agent calls `request_environment_access`, Portainer-Command:

1. Checks, as the requesting user, that they can see the environment in Portainer, and that an administrator has allowed live reads on it.
2. Works out which namespaces the credential may read: the **intersection** of what Portainer lets the user read and what an administrator has opened to agents on that environment. System namespaces are excluded unless an administrator names one exactly.
3. On Portainer 2.45.2 or 3.0, asks Portainer, as the requesting user, for a scoped kubeconfig bound to Portainer's read-only role in each namespace of the intersection. Portainer refuses any namespace the user can't read. On older Portainer, creates a temporary Portainer user with the **Read-only** role on that one environment, and binds its service account to a read-only Kubernetes `Role` in each namespace of the intersection.
4. Returns a kubeconfig that points at **Portainer's Kubernetes proxy**, with an expiry.

Because the kubeconfig points at Portainer, the agent never holds a cluster credential and never needs a network route to the Kubernetes API server. Portainer's RBAC sits in front of every request, and any write is refused by the cluster itself.

Nothing in this path grants a write, or reads beyond the granted namespaces. The only cluster-wide reads are listing namespaces and, on older Portainer, reading ClusterRoles and ClusterRoleBindings when the requesting user can read them. Revoking a grant, or letting it expire, deletes the scoped kubeconfig, or removes the temporary user's role bindings and deletes the user, and the token stops working immediately.

A sweep runs every 60 seconds to expire and clean up grants. Each temporary user's expiry is encoded in its username (`command-agent-e<env>-x<epoch>-<random>`), so an administrator who spots one in Portainer's user list can tell what it is and when it will be removed. Scoped kubeconfigs on Portainer 2.45.2 or 3.0 aren't Portainer users; Portainer enforces their expiry itself.

## Reading and changing desired state

When an agent calls `request_environment_repository`, it gets a clone URL for a repository **served by Portainer-Command itself**, not your real GitOps repository. That repository holds only the directory an administrator configured for the environment, with that directory as its root and recent history of the files in it. Nothing outside the directory exists in it. If the agent names a live object instead, the repository holds only the files that deploy it, from the repository its Portainer stack, edge stack, workflow deployment, Argo CD Application, or Flux object deploys from.

The agent works with ordinary Git: clone, edit, commit to a feature branch, and push. The push is validated (paths, file types, sizes) and **held inside Portainer-Command**. Nothing reaches the Git host yet, and pushing to the deploy branch is refused.

When the agent calls `open_environment_proposal`, Portainer-Command re-checks the environment's policy, turns the branch's net change into one commit against the real repository using the **user's own Git credential**, and opens a change request (kept in Portainer-Command on a plain Git server). The proposal then appears on the [Proposals](../using-portainer-command/proposals.md) page.

In the environment's own repository, one manifest file maps to one Portainer stack. That's what makes "remove this file" mean "delete what it deployed", and it's why Portainer-Command never needs write access to a cluster of its own.

## Approving and delivering

Before offering the approve button, Portainer-Command checks that the approver could deploy the change themselves, on every environment the merge updates. Approving merges the change request and creates or updates the Portainer stack using the approver's own credential. Edge stacks are redeployed with Portainer-Command's administrator credential on the approver's behalf. Argo CD, Flux, and Portainer GitOps workflow deployments apply the merge themselves, and Portainer-Command tracks delivery until they have. Removing a file deletes the stack, which deletes what it deployed.

Undoing a proposal is a revert commit, opened as another proposal. Rolling an environment back to an earlier state works the same way. Rollback is Git, not a separate feature.

## Where state lives

* **Portainer** holds the credentials behind read-only grants and enforces their expiry. Temporary users (Portainer older than 2.45.2) can still be listed, expired, and revoked if Portainer-Command's database is lost. Scoped kubeconfigs can't be listed, so they can't be revoked early, but Portainer still ends them at expiry, at most eight hours out.
* **Portainer's add-on config store** holds instance-wide settings.
* **A Postgres database**, installed alongside Portainer-Command by the same chart with its own persistent volume, holds the history around those credentials (reasons, issue and expiry times, revocations) plus proposals, agent tokens, workspaces, and the timeline.
* **Credentials Portainer-Command holds itself**, such as users' Git tokens, the Portainer access tokens behind agent tokens, and model provider keys, are encrypted at rest (AES-256-GCM) under a key the chart generates and keeps across upgrades.

Losing the database loses the audit trail, not control of the credentials.

## Next: Security model

How identity, scope, and approval fit together is covered in [Security model](security.md).
