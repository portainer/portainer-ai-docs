# Welcome to Portainer-Run

Portainer-Run is a governed self-service layer that lets non-developer business teams deploy the apps they build with AI tools onto your organization's own Kubernetes, without ever needing to know anything about Kubernetes, containers, or infrastructure.

<a href="../architecture/overview.md" class="button secondary" data-icon="buildings">Architecture</a><a href="../requirements.md" class="button secondary" data-icon="clipboard-list-check">Requirements</a><a href="../quick-start.md" class="button primary" data-icon="rocket-launch">Quick Start</a>

***

## Why Portainer-Run exists

AI has made everyone a developer. Not a software engineer, not a full-stack engineer, but a developer: someone who can describe a business problem to an AI coding tool and get a working application out the other side. The barrier to creation has effectively gone.

The best AI-assisted development tools know this, which is why they push hosting onto their own SaaS or PaaS. It's the only way to keep the experience seamless end to end. That works right up until the app needs to talk to something inside your network: an internal database, an on-prem API, a system that lives behind the firewall and isn't going anywhere. At that point the experience collapses, and the only path forward is a ticket to the platform team.

That platform team is already stretched. The influx of deployment requests from people who have never touched infrastructure, app owners, business developers, support staff, people who vibe-coded their first container last Tuesday, is a real and growing problem with no clean answer today. Buying an Internal Developer Platform that takes a year to configure before anyone can use it isn't the answer either.

Portainer-Run sits in that gap. A container-ready application is already an artifact AI coding tools can produce. Portainer-Run is the "now run it, inside your environment" layer, with the platform team's guardrails baked in via Portainer's existing RBAC, Git-based governance, and policy controls. The platform team's role shifts from processing every deployment ticket to setting the rules once.

It is intentionally narrow in scope. Portainer-Run does not replace Portainer. It does not try to serve the engineer who already has full cluster access and wants a powerful agent with deep API reach; that's a different product for a different persona. Portainer-Run surfaces one workflow, deploy, run, and operate a containerized workload, in the simplest interface possible, for the people who have no idea what a Pod is and shouldn't need to.

## What Portainer-Run does

Portainer-Run connects to your Portainer instance using a personal access token. Access is governed entirely by your Portainer RBAC role, so there is no separate user or permissions model to manage. Once connected, it provides a unified, self-service view across every Kubernetes environment your account can reach.

From Portainer-Run, a business builder can:

* **Deploy an AI-built application straight from source**: no Dockerfile, no container registry, no platform engineering ticket. Sane resource requests and limits are applied automatically, so a first deployment doesn't need an administrator's involvement to be safe by default.
* **Monitor, scale, restart, and roll back** everything they've deployed from a single operational view.
* **Update a running application** by uploading revised files, which commits the change to Git and redeploys it.
* **Ask the built-in Assistant** questions about their services in plain English, and get an AI-powered answer grounded in live logs and events.

Underneath all of it, every deployment is committed to a sanctioned Git repository and reconciled onto Kubernetes by Portainer's GitOps engine. Nothing is deployed out of band, nothing bypasses your governance, and administrators retain full visibility over what's running and who deployed it.

## Where to go next

* New to Portainer-Run? Start with [Requirements](../requirements.md) and then [Quick Start](../quick-start.md).
* Already running it? Jump to [Using Portainer-Run](../user/overview.md) to explore the interface, or [Architecture](../architecture/overview.md) to understand how it fits together with Portainer.
