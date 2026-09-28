# Requirements

Portainer-Command installs as an add-on to Portainer Business. Before you install it, make sure you have the following in place.

## Portainer Business with add-ons

Portainer-Command is installed from the Portainer add-ons catalog, so you need a [Portainer Business installation](https://docs.portainer.io/start/install) that supports [add-ons](https://docs.portainer.io/admin/add-ons), with a valid license.

## A Kubernetes environment to install on

The add-on installs onto a Kubernetes environment managed by Portainer, running Kubernetes 1.21 or later. The environment needs:

* A **default StorageClass**, or one you choose, that can provision an 8 GiB `ReadWriteOnce` volume for Portainer-Command's database. We recommend a StorageClass that encrypts volumes at rest.
* Enough capacity for Portainer-Command itself, which is small: the add-on requests 50m CPU and 64 MiB of memory, and its database requests 100m CPU and 256 MiB.
* If you plan to use [AI Workspaces](user/workspace.md), capacity for one pod per active user. Each workspace requests 100m CPU and 256 MiB of memory, with a default limit of 2 CPUs and 2 GiB. Workspaces must run on the same cluster as Portainer-Command. To enforce the workspace [network modes](admin/settings.md#workspace-network), the cluster's CNI must enforce Kubernetes NetworkPolicy.

## Kubernetes environments managed with GitOps

Portainer-Command assumes GitOps is the source of truth for the environments it governs. For each environment you want agents to propose changes to, you need:

* A **GitHub** repository that holds the environment's manifests. Other Git hosts aren't supported yet.
* That repository added as a GitOps source in Portainer.

Agents can still read live state on an environment without a repository, but they can't read its manifests or propose changes.

## Internal authentication for read-only sessions

To let agents read live cluster state, Portainer must use **internal authentication**. On an OAuth or LDAP instance, agents can still read manifests and propose changes, but they can't get read-only sessions. See [Known limitations](known-limitations.md#oauth-and-ldap-authentication).

## For each user: a GitHub credential

Proposals are pull requests authored with each user's own Git credential. Every user who wants their agent to propose changes, and every user who approves proposals, needs a GitHub personal access token for the repositories involved. See [Connect to GitHub](user/connect-to-github.md).

## Optional: a model provider for AI Workspaces

If you enable AI Workspaces, they need a model to talk to. You can use:

* An Anthropic API key.
* An OpenAI API key.
* Any OpenAI-compatible endpoint, such as an LLM gateway, vLLM, or an Ollama you run yourself.

Users who connect their own agent, such as Claude Code or Cursor, use their own model and don't need this.

## Next step

Once these are in place, continue to [Install](installation.md).
