# Requirements

Before installing Portainer-Run, make sure the following are in place.

## Portainer

* **Portainer Business Edition**, already deployed and managing the Kubernetes environment(s) you want Portainer-Run to expose. Portainer-Run is an addon on top of Portainer: it does not replace it and cannot run without it.
* Portainer must be installed on a Kubernetes environment (any flavor of Kubernetes), and that environment must exist as an environment in Portainer (see below).

## Kubernetes

* A **default StorageClass** configured on the local cluster, as well as on any Kubernetes environment where you plan to deploy. Without one, PersistentVolumeClaims will remain unbound and affected applications won't start.
* An **Ingress controller** installed on any cluster where you plan to expose applications via Ingress. Portainer-Run can create Ingress resources but does not install or configure an ingress controller itself.
* **metrics-server** installed on any cluster where you want the Metrics tab (CPU/memory sparklines) to populate.

## Git

* A Git repository to act as the GitOps source of truth: for example GitHub, GitHub Enterprise Server, GitLab (SaaS or self-hosted), or Gitea. This is where Portainer-Run commits manifests and, for file-upload deployments, source code.
* A personal access token for that repository with read/write access:
  * **GitHub fine-grained PAT:** Contents (read and write) permission on the target repository.
  * **GitHub classic PAT:** `repo` scope.
  * **GitLab / Gitea:** an access token with equivalent read/write repository permissions.

## Runtime environment for Portainer-Run itself

Portainer-Run runs as a Kubernetes workload alongside the infrastructure it manages. You'll need:

* **Portainer installed on a Kubernetes cluster.** At present we don't support running Portainer-Run on other platforms.
* Persistent storage of at least a few hundred MB for the SQLite database (encrypted Git target credentials) and the deployment status cache.
* Network egress to your Git provider (GitHub, GitLab, or Gitea) and, if you want AI features, to Anthropic and/or OpenAI.

## Optional: AI Assistant and AI-powered log triage

* An **Anthropic API key** and/or an **OpenAI API key**, if you want the Assistant panel and AI-powered log analysis. Without either key set, the Assistant is not shown at all. If both are set, Anthropic takes priority unless `AI_PROVIDER` is set explicitly.

## Supported application types

No additional infrastructure is required, but it's worth knowing what can and can't run before you plan your rollout:

| Detected from                                 | Runtime          |
| --------------------------------------------- | ---------------- |
| `package.json`                                | Node.js 22       |
| `requirements.txt` or any `.py` file          | Python 3.12      |
| `Gemfile` or any `.rb` file                   | Ruby 3.3         |
| Any `.php` file                               | PHP 8.3 (Apache) |
| Static assets only (HTML/CSS/JS/images/fonts) | nginx            |

Deploy supports single-container applications only, with no build or compile step. Go, Java, Rust, .NET, and any other language requiring compilation are out of scope.

## Default resource allocation

Every deployed application receives a sane default resource request and limit automatically: **0.1 CPU / 1 GiB memory requested, 1 CPU / 4 GiB memory as the limit.** No sizing decisions are needed to deploy safely. An administrator can adjust these after deployment from Portainer if a workload needs more or less.

## Next step

Once these are in place, continue to [Quick Start](quick-start.md) to get Portainer-Run running.
