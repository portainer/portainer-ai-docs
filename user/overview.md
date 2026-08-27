# Overview

Once connected, Portainer-Run's navigation is organized into four areas: **Workloads**, **Deploy**, **Admin** (visible to admins only), and the always-available **Assistant** panel (if an Anthropic or OpenAI key has been configured). This section walks through each in turn, plus the **MCP** endpoint for deploying from AI coding tools without touching the UI at all.

* [**Applications**](applications.md): the main operational view and the page you land on after login. Shows status, logs, metrics, and edit controls for everything Portainer-Run has deployed.
* [**Deploy**](deploy.md): deploy an application straight from AI-generated source files.
* [**Admin**](admin/): visible only to Portainer admins.
  * [Git Targets](admin/git-targets.md): manage personal and shared repository connections.
  * [Cluster Readiness](admin/cluster-readiness.md): environment health checks and enable/disable controls.
  * [Settings](admin/settings.md): for Portainer-Run's configuration.
* [**Assistant**](assistant.md): a persistent, context-aware chat panel available on every page.
* [**MCP**](mcp.md): deploy applications directly from Claude Desktop, Claude Code, or any MCP-capable client.

A quick note on what determines what you see: everything in Portainer-Run is governed by the Portainer RBAC role of the account behind your personal access token. Non-admins won't see the Admin section at all. See [Roles and RBAC](../architecture/roles.md) for the full picture.
