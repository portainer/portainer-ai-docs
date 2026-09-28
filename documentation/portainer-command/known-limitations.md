# Known limitations

Portainer-Command is in beta. None of the following blocks installing it, but you should know about them before you roll it out.

## GitHub only

Proposals open pull requests on **GitHub**. Other Git hosts aren't supported yet, so the repositories your environments deploy from must be on GitHub for agents to propose changes.

## OAuth and LDAP authentication

Two features rely on Portainer-Command creating Portainer users with a password, which Portainer refuses when it uses OAuth or LDAP authentication:

* **Read-only grants.** On an OAuth or LDAP instance, `request_environment_access` can't issue a kubeconfig, and the tool says so. Agents can still read an environment's manifests and propose changes normally.
* **One-click transport key setup.** An administrator has to create the transport user in the identity provider, sign in as it once to create an access token, and paste the token into **Settings**. See [Settings](admin/settings.md).

## Async Edge environments

Async Edge agents don't hold a tunnel to Portainer, so no credential can reach their cluster, and live read access is refused for them. Proposals still work: the change travels as Git and reaches the Edge agent through its command queue.

## Two credentials per agent connection

Agent traffic passes through Portainer's add-on gateway, so each MCP client sends two headers: the instance-wide **transport key** to get past the gateway, and the connection's own **agent token** to identify the user. The **Connect your agent** page generates the configuration for you, so this only matters if you configure a client by hand. See [MCP tools](mcp.md#authentication).

## Custom resources aren't readable

A read-only grant covers Kubernetes' built-in resource types. Custom resources, such as Kyverno policies or policy reports, aren't visible to an agent through a grant.

## A proposer can approve their own agent's proposal

An agent has no way to approve a proposal; approval always happens in the Portainer-Command UI. However, there is currently no rule that stops the person whose agent opened a proposal from approving it themselves, as long as they hold the rights to deploy it.

## AI Workspaces share Portainer-Command's cluster

AI Workspaces must run on the same cluster as Portainer-Command. Running workspaces on a separate cluster isn't supported yet.

## Grants are recorded, not their actions

Portainer-Command records that a read-only grant existed, who held it, why, and for how long. It doesn't yet record which reads were made with it. The service account each grant acts as is shown on the session, so you can match it against your cluster's own audit log.
