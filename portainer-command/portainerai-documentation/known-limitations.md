# Known limitations

Portainer-Command is in beta. None of the following blocks installing it, but you should know about them before you roll it out.

## Git hosts

Proposals work on GitHub, GitLab, Azure DevOps, Bitbucket Cloud, Bitbucket Data Center, and other Git servers. See [Connect your Git host](using-portainer-command/connect-to-github.md#supported-git-hosts). Some limits apply:

* Gitea isn't supported.
* A self-hosted Git server with a certificate from a private CA can't be reached.
* On a plain Git server, change requests and their comments live in Portainer-Command.
* A rejected proposal on Bitbucket Cloud can't be reopened. Ask the agent to propose the change again.

## Proposing into what's already deployed

* Portainer stacks, edge stacks, and GitOps workflow deployments are edit-only: no new files or renames. Deleting every file of a stack or edge stack removes it; deleting some is refused.
* Argo CD on a separate management cluster, an Argo CD Application that deploys to another cluster, and Rancher Fleet aren't supported.
* Approving an Argo CD or Flux proposal merges it but doesn't sync it. A reconciler that syncs manually waits for someone to sync it.
* An emergency halt closes the environment to agents, but doesn't stop Portainer, Argo CD, or Flux applying a merge.

## OAuth and LDAP authentication

* **Read-only grants.** On Portainer 2.45.2 or later, or 3.0, read-only grants are scoped kubeconfigs and work under any authentication method. On older Portainer, a grant is a temporary Portainer user with a password, which OAuth and LDAP refuse. There, `request_environment_access` can't issue a kubeconfig and tells the agent to upgrade Portainer. Agents can still read an environment's manifests and propose changes normally.
* **One-click transport key setup.** Portainer-Command can only create the transport user on internal authentication. On OAuth or LDAP, **Settings** lists the steps: create the user in your identity provider, sign in as it once to create an access token, and paste the token in. See [Settings](using-portainer-command/admin/settings.md#agent-gateway-transport-key).

## Async Edge environments

Async Edge agents don't hold a tunnel to Portainer, so no credential can reach their cluster, and live read access is refused for them. Proposals still work: the change travels as Git and reaches the Edge agent through its command queue.

## Two credentials per agent connection

Agent traffic passes through Portainer's add-on gateway, so each MCP client sends two headers: the instance-wide **transport key** to get past the gateway, and the connection's own **agent token** to identify the user. The **Connect your agent** page generates the configuration for you, so this only matters if you configure a client by hand. See [MCP tools](architecture/mcp.md#authentication).

## Custom resources aren't readable

A read-only grant covers Kubernetes' built-in resource types. Custom resources, such as Kyverno policies or policy reports, aren't visible to an agent through a grant. On Portainer 2.45.2 or 3.0, RBAC objects such as Roles and RoleBindings aren't readable either.

## A proposer can approve their own agent's proposal

An agent has no way to approve a proposal; approval always happens in the Portainer-Command UI. However, no rule stops the person whose agent opened a proposal from approving it themselves, as long as they hold the rights to deploy it.

## AI Workspaces share Portainer-Command's cluster

AI Workspaces must run on the same cluster as Portainer-Command. Running workspaces on a separate cluster isn't supported yet.

## Grants are recorded, not their actions

Portainer-Command records that a read-only grant existed, who held it, why, and for how long. It doesn't yet record which reads were made with it. The service account each grant acts as is shown on the session, so you can match it against your cluster's own audit log.
