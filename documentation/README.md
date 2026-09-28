# Welcome to Portainer AI

Portainer AI is a family of products for putting AI to work on the Kubernetes platforms you already run, under the controls your security team already requires. Each product runs inside your own environment, uses your existing Portainer RBAC, and treats Git as the source of truth for every change it makes.

This documentation covers two of the products: **Portainer-Run** and **Portainer-Command**.

<a href="portainer-run/README.md" class="button primary" data-icon="rocket-launch">Portainer-Run</a><a href="portainer-command/README.md" class="button primary" data-icon="terminal">Portainer-Command</a>

***

## The products

<table data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><strong>Portainer-Run</strong></td><td>A governed place to run the AI-built apps your people are already building. Business users deploy straight from source, or from an AI coding tool over MCP, without needing to know anything about Kubernetes.</td><td><a href="portainer-run/README.md">portainer-run/README.md</a></td></tr><tr><td><strong>Portainer-Command</strong></td><td>The safe way to put AI agents to work on your Kubernetes fleet. Agents read cluster state through expiring, read-only sessions, and every change they want to make becomes a Git pull request that a person approves.</td><td><a href="portainer-command/README.md">portainer-command/README.md</a></td></tr></tbody></table>

The family also includes [Portainer-AiGrid](https://portainer.ai/products/portainer-aigrid), a self-hosted retrieval platform that turns your document corpus into a private, citable index any AI agent can query. It isn't covered in this documentation.

## Which product do I need?

Each product stands alone, so start with the problem in front of you.

| If you need to...                                                                                                   | Use                                        |
| ------------------------------------------------------------------------------------------------------------------- | ------------------------------------------ |
| Give business teams a safe, self-service way to deploy the apps they build with AI tools, inside your own network   | [Portainer-Run](portainer-run/README.md)         |
| Let engineers use AI agents (Claude Code, Cursor, or a built-in workspace) to investigate and change your clusters  | [Portainer-Command](portainer-command/README.md) |
| Govern AI agents your teams are already pointing at your clusters, with a full audit trail and an emergency halt    | [Portainer-Command](portainer-command/README.md) |

The two products serve different people. Portainer-Run is for people who have no idea what a Pod is and shouldn't need to. Portainer-Command is for the people who do, and for the platform and security teams responsible for what their agents are allowed to touch.

## What the products have in common

* **They run on your platform.** Every product runs on your own Kubernetes, wherever it lives: cloud, on-premises, edge, or air-gapped. Workloads and agent traffic stay inside your boundary.
* **Portainer Business is the engine.** Both products are delivered as Portainer Business add-ons and use Portainer's GitOps engine to deliver changes to your clusters.
* **Access comes from Portainer.** There is no separate user store or permissions model to maintain. What a user can see and do is decided by the Portainer RBAC you already manage, and neither product widens anyone's access.
* **Every change goes through Git.** Deployments from Portainer-Run and changes proposed through Portainer-Command are committed to a Git repository before Portainer applies them, so every change has an author, a diff, and a way back.
* **AI clients connect over MCP.** Each product exposes a Model Context Protocol (MCP) endpoint, so AI tools such as Claude Code, Claude Desktop, and Cursor can work with it directly, under the same rules as the web interface.
