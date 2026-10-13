# Applications

Applications is the primary operational view in Portainer-Run, and the page you land on after logging in. There is no separate Dashboard; Applications serves that role.

## What it shows

Applications lists every deployment tagged `managed-by=portainer-run` across all Kubernetes environments your token can reach. Workloads deployed through Portainer's own UI or `kubectl` directly won't appear here. See [Notes on scope](applications.md#notes-on-scope) below.

Each row shows:

| Column            | What it shows                                                                                                                                                                                                                                                                                                                                 |
| ----------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Name**          | The application's name.                                                                                                                                                                                                                                                                                                                       |
| **Environment**   | Which Kubernetes environment (cluster) the application runs in.                                                                                                                                                                                                                                                                               |
| **Project Space** | The Kubernetes namespace the application is deployed into.                                                                                                                                                                                                                                                                                    |
| **Health**        | A plain-English status: for example "Your app is live and running," or a reason when something's wrong, such as "App keeps crashing (4 restarts)," "Can't download the image," or "No node has enough resources." Transient pod recycling (OOMKilled) is suppressed until three or more restarts, so a single blip doesn't read as a problem. |
| **Access**        | A clickable address for reaching the application: node IP:nodePort for NodePort, IP:port for LoadBalancer, or the FQDN for Ingress.                                                                                                                                                                                                           |
| **Deployed**      | When the application was deployed.                                                                                                                                                                                                                                                                                                            |
| **Deployed by**   | Which user deployed it.                                                                                                                                                                                                                                                                                                                       |
| **Logs**          | A button opening the application's logs directly.                                                                                                                                                                                                                                                                                             |
| **Restart**       | A button triggering a rolling restart.                                                                                                                                                                                                                                                                                                        |

## Deploying from here

A **+ Deploy** button in the page header takes you straight to [Deploy](deploy.md): drop in the files an AI coding tool produced and Portainer-Run handles the rest.

## Application detail panel

Clicking any application opens a detail panel. By default, it's kept simple, showing only the tabs most people need day to day:

| Tab          | What it shows                                                                                                                                                                                                                                                                                                                                                                                                                       |
| ------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Overview** | The application's status, its address, and its environment variables. See [Overview](applications.md#overview) below.                                                                                                                                                                                                                                                                                                               |
| **Metrics**  | CPU and memory sparklines, polled periodically via `metrics.k8s.io`. Requires metrics-server on the cluster.                                                                                                                                                                                                                                                                                                                        |
| **Logs**     | Streamed or fetched pod logs with severity filtering and text search. The **AI Analyse** button gathers logs, pod conditions, and Kubernetes events and sends them to the configured AI provider for triage. This covers failure modes where no application logs exist yet, such as scheduling failures, image pull errors, or resource constraints, because it reads from events rather than relying solely on application output. |
| **Edit**     | Update the application's deployment, exposure, and environment variables, or upload revised code. See [Edit](applications.md#edit) below.                                                                                                                                                                                                                                                                                           |

A **Show technical details** button reveals two further tabs for anyone who needs a deeper level of control:

| Tab               | What it shows                                                                                                  |
| ----------------- | -------------------------------------------------------------------------------------------------------------- |
| **App internals** | Per-container configuration: image, ports, pull policy, resource limits, environment variables, volume mounts. |
| **Revisions**     | ReplicaSet history, most recent first, each with a **Rollback** button.                                        |

### Overview

The Overview tab is deliberately simple:

* **Status**: a plain-English summary, for example "Your app is live and running."
* **Address**: an **Open your app** button (the same action as the Access link on the Applications list) alongside a text link to the application.
* **Environment variables**: every configured variable and its value. Anything that looks like a password, key, or other secret is masked by default, with a **Reveal** button to show it.

### Edit

The Edit tab covers everything needed to change a running application, organized into sections:

* **Current Deployment**: read-only details of what's currently running, including the runtime image, the start command, the source path, and the branch it was deployed from.
* **Exposure**: an **Expose Service As** dropdown (NodePort, LoadBalancer, or Ingress) and a **Port** field where relevant. Changes are applied with a **Save Exposure** button that only appears once something has changed.
* **App Settings**: add, update, or remove environment variables.
* **Upload updated files**: select a folder or individual files to replace the deployed code with an updated version. A **Commit & Restart** button commits the selected files to the repository and redeploys and restarts the application.

## Default resource allocation

Every application deployed through Portainer-Run receives a default resource request and limit automatically: **0.1 CPU / 1 GiB memory requested, 1 CPU / 4 GiB memory as the limit.** This is applied without any input needed at deploy time. An administrator can adjust it afterward from within Portainer if a workload needs more or less.

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

## Notes on scope

Portainer-Run only surfaces deployments it created itself, filtered by the `managed-by=portainer-run` label. Anything deployed through Portainer's own UI or `kubectl` won't show up on this page.

Persistent storage volumes can't be modified after deployment. PVCs are created at deploy time and are untouched by the Edit tab. If you need to resize or reconfigure storage, that has to happen outside Portainer-Run.
