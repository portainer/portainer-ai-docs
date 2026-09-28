# Overview

Portainer-Command's sidebar is organized into three groups. What you see depends on your Portainer role and on whether an administrator has enabled AI Workspaces.

**Main**: your own work.

* [**Workspace**](workspace.md): your hosted AI agent, if AI Workspaces are enabled. It has four views: **Manage**, **Chat**, **Clear chat**, and the [**Prompt library**](prompt-library.md).
* [**Proposals**](proposals.md): the changes agents have proposed, and where you approve or reject them.
* [**My sessions**](my-sessions.md): the temporary read-only credentials your agents hold.

**Setup**: the one-time steps that make everything else work.

* [**Connect your agent**](connect-your-agent.md): create a connection for Claude Code, Cursor, or any other MCP client.
* [**Connect to GitHub**](connect-to-github.md): store the Git credential your proposals are authored with.

**Manage**

* [**Administration**](../admin/): visible to Portainer administrators only. Covers the timeline, environments, sessions, connections, workspaces, token spend, users, and settings.
* **Documentation**: the in-app guide, including the **Threat model** page, which documents each threat considered and what stops it.

## Home

The **Home** page is where you land. For most users, it shows:

* **Waiting on you**: proposals you're able to decide.
* **My open proposals**: proposals your agents have opened.
* **My live sessions**: read-only credentials your agents hold right now.
* **My recent connections**: the agent connections you've used recently.

Administrators see the whole instance instead: environments, open proposals, live sessions, recently used connections, and AI workspaces.

Until you've completed both setup steps, **Home** also shows a **Before you start** panel, and the steps are tagged **Set up** in the sidebar.

## Two ways to use an agent

* **Bring your own agent.** Connect the agent you already use, such as Claude Code, Claude Desktop, or Cursor, to Portainer-Command's MCP server. The agent runs wherever it already runs. Portainer-Command gives it the tools and holds the credentials. See [Connect your agent](connect-your-agent.md).
* **Use an AI Workspace.** Let Portainer-Command run a hosted agent for you, with a chat page to talk to it. There's nothing to install. See [AI Workspace](workspace.md).

Both go through the same [MCP tools](../architecture/mcp.md) and the same rules. Either way, the agent acts as you and can only reach what you can reach in Portainer.
