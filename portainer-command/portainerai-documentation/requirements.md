# Requirements

Portainer-Command installs as an add-on to Portainer Business. Before you install it, make sure you have the following in place.

## Portainer Business with add-ons

Portainer-Command is installed from the Portainer add-ons catalog, so you need a [Portainer Business installation](https://docs.portainer.io/start/install) that supports [add-ons](https://docs.portainer.io/admin/add-ons), with a valid license.

## A Kubernetes environment to install on

The add-on installs onto a Kubernetes environment managed by Portainer, running Kubernetes 1.21 or later. The environment needs:

* A **default StorageClass**, or one you choose, that can provision an 8 GiB `ReadWriteOnce` volume for Portainer-Command's database. We recommend a StorageClass that encrypts volumes at rest.
* Enough capacity for Portainer-Command itself, which is small: the add-on requests 50m CPU and 128 MiB of memory (with a 1 GiB memory limit), and its database requests 100m CPU and 256 MiB.
* If you plan to use [AI Workspaces](using-portainer-command/workspace/workspace.md), capacity for one pod per active user. Each workspace requests 100m CPU and 256 MiB of memory, with a default limit of 2 CPUs and 2 GiB. Workspaces must run on the same cluster as Portainer-Command. To enforce the workspace [network modes](using-portainer-command/admin/settings.md#workspace-network), the cluster's CNI must enforce Kubernetes NetworkPolicy. By default, workspace pods and their egress proxy run under AppArmor's `RuntimeDefault` profile, so the nodes they run on need AppArmor enabled, or an administrator turns off [**Run workspaces under AppArmor**](using-portainer-command/admin/settings.md#run-workspaces-under-apparmor). The profile only takes effect on Kubernetes 1.30 or later.

## Kubernetes environments managed with GitOps

Portainer-Command assumes GitOps is the source of truth for the environments it governs. For each environment you want agents to propose changes to, you need:

* A Git repository on GitHub, GitLab, Azure DevOps, Bitbucket Cloud, Bitbucket Data Center, or another Git server. See [Connect your Git host](using-portainer-command/connect-to-github.md#supported-git-hosts) for the full list.
* For a home for new manifests, that repository added as a GitOps source in Portainer and chosen on the environment.

Without a repository of its own, agents can still propose into the repositories behind what's already deployed there: Portainer stacks, edge stacks, and GitOps workflow deployments, and, if an administrator allows it, Argo CD and Flux. These need the administrator key in [Settings](using-portainer-command/admin/settings.md#portainer-credential-for-sessions).

## Read-only sessions on OAuth or LDAP

On Portainer 2.45.2 or later, or 3.0, read-only sessions work with any authentication method. On older Portainer they need **internal authentication**: on an OAuth or LDAP instance, agents can still read manifests and propose changes, but they can't get read-only sessions. See [Known limitations](known-limitations.md#oauth-and-ldap-authentication).

## For each user: a Git host credential

Proposals are change requests authored with each user's own Git credential. Every user who wants their agent to propose changes, and every user who approves proposals, needs their own credential for each Git host involved, such as a personal access token. See [Connect your Git host](using-portainer-command/connect-to-github.md).

## Optional: a model provider for AI Workspaces

If you enable AI Workspaces, they need a model to talk to. You can use:

* An Anthropic API key.
* An OpenAI API key.
* Any OpenAI-compatible endpoint, such as an LLM gateway, vLLM, or an Ollama you run yourself.

Users who connect their own agent, such as Claude Code or Cursor, use their own model and don't need this.

## Next step

Once these are in place, continue to [Install](installation.md).
