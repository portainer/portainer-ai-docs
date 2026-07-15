# Requirements

Before installing Portainer-Run, make sure the following are in place.

## Portainer

* **Portainer Business Edition**, already deployed and managing the Kubernetes environment(s) you want Portainer-Run to expose. Portainer-Run is a self-service surface on top of Portainer: it does not replace it and cannot run without it.
* A **personal access token** for the account Portainer-Run will connect as (Portainer → **Account → Access Tokens**). Portainer-Run inherits this account's RBAC role in full. A cluster-scoped token enumerates namespaces automatically, while a namespace-scoped token requires namespaces to be entered manually on deploy.
* OAuth-authenticated Portainer instances still work. Generate a personal access token from the Account page as above. OAuth login itself is not supported by Portainer-Run.

## Kubernetes

* At least one Kubernetes environment already registered in Portainer and reachable by it.
* A **default StorageClass** configured on any cluster where you plan to use Vibe Deploy or persistent storage in Simple Deploy. Without one, PersistentVolumeClaims will remain unbound and affected applications won't start.
* An **Ingress controller** installed on any cluster where you plan to expose applications via Ingress. Portainer-Run can create Ingress resources but does not install or configure an ingress controller itself.
* **metrics-server** installed on any cluster where you want the Metrics tab (CPU/memory sparklines) to populate.

## Git

* A Git repository to act as the GitOps source of truth: GitHub, GitHub Enterprise Server, GitLab (SaaS or self-hosted), or Gitea. This is where Portainer-Run commits manifests and, for file-upload deployments, source code.
* A personal access token for that repository with read/write access:
  * **GitHub fine-grained PAT:** Contents (read and write) permission on the target repository.
  * **GitHub classic PAT:** `repo` scope.
  * **GitLab / Gitea:** an access token with equivalent read/write repository permissions.
* Portainer itself must already be configured to poll and reconcile this repository via GitOps, since Portainer-Run relies on Portainer's GitOps engine to apply what it commits.

## Runtime environment for Portainer-Run itself

Portainer-Run runs as a single container (or Kubernetes workload) alongside the infrastructure it manages. You'll need either:

* **A Kubernetes cluster**, if you'd rather run Portainer-Run itself as a workload (commonly the same cluster Portainer manages, though it doesn't have to be); or:
* **Docker** (or a Docker-compatible runtime) on a host that can reach both your Portainer instance and your Git provider, for a standalone Docker deployment.
  * with **Docker Compose**, if you prefer a compose-managed deployment.

Whichever route you choose, you'll need:

* Persistent storage of at least a few hundred MB mounted at `/app/data` inside the container, for the SQLite database (encrypted Git target credentials) and the deployment status cache. **Do not mount over `/app`**, only `/app/data`.
* A `PORTAINER_URL` your Portainer-Run instance can reach.
* A 32+ character `ENCRYPTION_KEY`, generated once and kept identical across every redeploy (for example, `openssl rand -hex 32`). If this value changes or is lost, existing Git targets can no longer be decrypted.
* Network egress to your Git provider (GitHub, GitLab, or Gitea) and, if you want AI features, to Anthropic and/or OpenAI.

## Optional: AI Assistant and AI-powered log triage

* An **Anthropic API key** and/or an **OpenAI API key**, if you want the Assistant panel and AI-powered log analysis. Without either key set, the Assistant is not shown at all. If both are set, Anthropic takes priority unless `AI_PROVIDER` is set explicitly.

## Supported application types (Vibe Deploy)

No additional infrastructure is required, but it's worth knowing what Vibe Deploy can and can't run before you plan your rollout:

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
