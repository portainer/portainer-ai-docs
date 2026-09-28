# Portainer-Command

Portainer-Command is the safe way to put AI agents to work on your Kubernetes fleet. It sits between AI agents and your clusters: agents can read cluster state, but every change they want to make is forced through a Git pull request that a person approves, and Portainer's GitOps engine delivers it.

<a href="architecture/overview.md" class="button secondary" data-icon="buildings">Architecture</a><a href="requirements.md" class="button secondary" data-icon="clipboard-list-check">Requirements</a><a href="installation.md" class="button primary" data-icon="rocket-launch">Install</a>

{% hint style="info" %}
The Portainer-Command add-on is currently in **beta**. The core feature set is complete and installs from the Portainer add-ons catalog. See [Known limitations](known-limitations.md) for what isn't supported yet.
{% endhint %}

***

## Why Portainer-Command exists

Anyone can point an AI agent at `kubectl` today. What most organizations lack is permission: no security team signs off on an agent holding cluster credentials, and no operations lead wants to be on call when it goes wrong. Portainer-Command is the set of guardrails that makes "yes" possible.

It helps two kinds of team:

* **Teams that haven't started.** The built-in [AI Workspace](user/workspace.md) is the on-ramp: an agent with nothing to install, a [prompt library](user/prompt-library.md) of real tasks (security audits, image pinning, network policy baselines, RBAC review), and undo on everything. The governance isn't a tax on getting started. It's the reason you're allowed to.
* **Teams whose engineers already point agents at clusters, officially or not.** Start from the administration side: a fleet-wide [timeline](admin/timeline.md) of all agent activity, per-environment allow and deny, namespace restrictions, per-user token spend, an [emergency halt](admin/environments.md#emergency-halt) for an environment or a user, and one-click revert of any agent change. Don't fight the behavior. Govern it.

## What Portainer-Command does

* **Agents can't change a cluster directly.** There is no tool that writes to a cluster. The only write path is a commit: an agent pushes a branch, Portainer-Command turns it into a pull request, and a person who could deploy the change themselves approves it.
* **Reads are scoped and expire.** An agent asks for a read-only kubeconfig for one environment. It only reaches the namespaces that both Portainer lets the user read and an administrator has opened to agents, and it expires on its own.
* **Every action has a person behind it.** An agent acts with its owner's own Portainer credential, and its pull requests are authored with its owner's own Git credential. Portainer-Command never widens anyone's access.
* **Everything can be undone.** Any proposal can be reverted in one click, and an environment can be rolled back to any past state. Both happen as pull requests, too.
* **Spend is visible.** Model token usage and cost are tracked per user and per workspace.
* **The threat model is published.** The in-app **Threat model** page documents each threat considered and what stops it. You can hand it to your security team.

## How a change happens

{% stepper %}
{% step %}
### A person sets a goal

A user gives a goal to their [AI Workspace](user/workspace.md), or [connects their own agent](user/connect-your-agent.md), such as Claude Code or Cursor.
{% endstep %}

{% step %}
### The agent reads the environment

The agent reads live state through a scoped, read-only kubeconfig, and desired state by cloning the environment's manifests from Git.
{% endstep %}

{% step %}
### The agent proposes a change

The agent pushes a branch and opens a proposal. Portainer-Command opens a pull request, authored by the person whose agent made it.
{% endstep %}

{% step %}
### A person approves it

On the [Proposals](user/proposals.md) page, someone who holds the rights to deploy the change reviews the diff and approves or rejects it.
{% endstep %}

{% step %}
### Portainer delivers it

Approving merges the pull request, and Portainer GitOps delivers the change to the environment.
{% endstep %}
{% endstepper %}

Anything can then be undone from Portainer-Command, or from Git history.

{% hint style="warning" %}
Portainer-Command assumes GitOps is the source of truth for the environments it governs. If you aren't there yet, Portainer-Command is a good reason to get there.
{% endhint %}

## Where to go next

* New to Portainer-Command? Start with [Requirements](requirements.md), then [Install](installation.md) and [Initial configuration](initial-configuration.md).
* Setting up as a user? Go to [Connect your agent](user/connect-your-agent.md) and [Connect to GitHub](user/connect-to-github.md).
* Want to understand the guarantees? Read [Architecture](architecture/overview.md) and [Security model](architecture/security.md).
