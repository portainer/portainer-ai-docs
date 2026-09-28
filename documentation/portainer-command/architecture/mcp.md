# MCP tools

Portainer-Command exposes a Model Context Protocol (MCP) server that any MCP-capable agent can connect to, including Claude Code, Claude Desktop, Cursor, Windsurf, and the Portainer-Command built-in [AI Workspace](../user/workspace.md). This page is the reference for how it works. To connect a client, see [Connect your agent](../user/connect-your-agent.md).

## Endpoint

| Item      | Value                                                   |
| --------- | ------------------------------------------------------- |
| Endpoint  | `https://<your-portainer>/addons/portainer-command/mcp` |
| Transport | Streamable HTTP, `POST` only                            |
| Sessions  | None. The server is stateless.                          |

## Authentication

Every request carries two headers:

```
X-API-Key:       ptr_…   the instance's shared transport key, for Portainer's add-on gateway
X-Command-Token: cmd_…   this connection's agent token, for Portainer-Command
```

The transport key gets the request past Portainer's add-on gateway. It's one key for the whole instance, set by an administrator in [Settings](../admin/settings.md#agent-gateway-transport-key), and it identifies nobody. The **agent token** identifies the user. It's created on the **Connect your agent** page, bound to that user's own Portainer credential, stored only as a hash, and expires after 90 days.

Every Portainer call an agent causes is made with its owner's own Portainer access token, so the agent sees exactly what its owner sees.

## The tool surface

There are six tools, and none of them can change a cluster.

| Tool                             | Kind      | What it does                                                                         |
| -------------------------------- | --------- | ------------------------------------------------------------------------------------ |
| `list_environments`              | Read-only | Lists the Kubernetes environments you can reach, their status, and what each allows. |
| `request_environment_access`     | Issues    | Gets a temporary, read-only kubeconfig for one environment.                          |
| `revoke_environment_access`      | Revokes   | Hands a kubeconfig back before it expires.                                           |
| `request_environment_repository` | Issues    | Gets a clone URL and short-lived credential for the environment's manifests.         |
| `open_environment_proposal`      | Proposes  | Turns a pushed branch into one pull request for a person to review.                  |
| `list_environment_proposals`     | Read-only | Lists what became of the proposals opened for an environment.                        |

The tool surface is small on purpose. Every tool added is a tool an agent could misuse and a reviewer has to reason about. Rather than a tool for each kind of Kubernetes resource, an agent reads live state with a read-only kubeconfig and the Kubernetes API it already knows.

### list\_environments

Lists the environments the agent's owner can reach in Portainer, not the whole instance. Each environment reports three capabilities, because an administrator sets each one separately:

| Capability             | Tool it enables                  | Set by                                                              |
| ---------------------- | -------------------------------- | ------------------------------------------------------------------- |
| Read live              | `request_environment_access`     | **Allow agents to read live state**                                 |
| Read GitOps            | `request_environment_repository` | **Allow agents to read GitOps state**, plus a configured repository |
| Propose GitOps changes | `open_environment_proposal`      | **Allow agents to propose changes to GitOps state**                 |

When all three are off, the environment is closed to agents. Async Edge environments report `liveAccess: unavailable-async-edge`, because they can't be read live.

### request\_environment\_access

Returns a temporary, read-only kubeconfig for one environment, which talks to the cluster through Portainer's Kubernetes proxy. Every permitted read works (`kubectl get`, `describe`, `logs`, or raw API calls), every write is refused, and the credential expires on its own. The agent states a reason, which is recorded on the session.

What a credential can read:

* Only the namespaces in the intersection of what Portainer lets the owner read and what an administrator has opened to agents. Reads are per namespace. List namespaces, then iterate.
* No system namespaces, unless an administrator names one exactly.
* Only the one environment it was issued for.
* No Secrets, unless an administrator has allowed them. The result tells the agent about any namespace where Secrets are readable anyway. See [Secrets](../admin/environments.md#secrets).
* No custom resources.

Sessions last 60 minutes by default, and at most 8 hours. Issued sessions appear on [My sessions](../user/my-sessions.md).

### revoke\_environment\_access

Hands back a kubeconfig before it expires. This takes effect immediately: Portainer removes the credential's service account and role bindings from the cluster.

### request\_environment\_repository

Returns an authenticated Git clone URL, a short-lived credential, and the exact clone command to run. The repository is served by Portainer-Command. It's a filtered view that holds only the directory an administrator configured for the environment, rooted at that directory, with recent history. Nothing outside the directory exists in it.

The agent works with ordinary Git: clone, edit, commit to a feature branch, and push. Pushing to the main branch is refused, and a pushed branch deploys nothing until the agent calls `open_environment_proposal`. To bring in an upstream manifest or Helm chart, the agent downloads it into its clone and commits it.

### open\_environment\_proposal

Turns a pushed branch into a pull request for a person to review. It doesn't touch the cluster. The branch's net change against the main branch becomes one commit, authored with the owner's own Git credential, against the real GitOps repository.

Portainer-Command re-checks the environment's policy first, and refuses the proposal with a reason if:

* Proposals aren't allowed on the environment.
* A change targets a namespace outside the allowlist.
* A file outside a vendored Helm chart isn't a YAML manifest, or a document has no `kind`.
* The change includes a cluster-scoped object and the environment doesn't allow them.
* The branch adds a new Helm chart without declaring the namespace its release is installed into.

The proposal then appears on the [Proposals](../user/proposals.md) page.

### list\_environment\_proposals

Lists what became of the pull requests opened for an environment: still open, approved and deployed, rejected, or merged but failed, with the reviewer's reason when there is one. Agents are told to check this rather than assume a proposal landed.

## Verifying the endpoint

To confirm the endpoint is responding, list the tools:

```bash
curl -s -X POST https://your-portainer/addons/portainer-command/mcp \
  -H 'X-API-Key: ptr_…' \
  -H 'X-Command-Token: cmd_…' \
  -H 'Content-Type: application/json' \
  -H 'Accept: application/json, text/event-stream' \
  -d '{"jsonrpc":"2.0","id":1,"method":"tools/list"}'
```

A working endpoint returns a JSON-RPC response listing the six tools.

If a client reports a content-type error or a redirect to a login page, the request didn't get past Portainer's add-on gateway. Check that the `X-API-Key` header carries the current transport key. If an administrator has rotated the key, create a new connection.
