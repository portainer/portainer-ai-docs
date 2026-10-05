# Connect your agent

A connection lets one MCP-capable agent, such as Claude Code, Claude Desktop, Cursor, or Windsurf, act as you through Portainer-Command. It uses your own Portainer credential, so the agent sees exactly what you see and nothing more. Agents can read; the only way one can change anything is a pull request that a person approves.

{% hint style="info" %}
If **Add connection** is disabled and you see **Agent connections are not set up yet**, an administrator needs to set the [transport key](admin/settings.md#agent-gateway-transport-key) first.
{% endhint %}

## Create a connection

{% stepper %}
{% step %}
### Add a connection

Under **Setup** in the sidebar, click **Connect your agent**, then click **Add connection**.
{% endstep %}

{% step %}
### Name it and create it

Enter a name in **Name this connection**, such as `Ops laptop`. The name is also given to the Portainer access token Portainer-Command creates for you, so you can tell your tokens apart under **My account** in Portainer.

If Portainer needs your password to issue the token, enter it in **Your Portainer password**. The password goes straight from your browser to Portainer and is never stored. LDAP and OAuth users aren't asked for one.

Click **Create connection**.
{% endstep %}

{% step %}
### Copy the configuration

Portainer-Command shows the configuration for your agent, with your token filled in.

{% hint style="warning" %}
This is the only time the token is shown. Portainer-Command stores only a hash of it and can't display it again. Copy it now.
{% endhint %}

For **Claude Code**, run the command shown in the repository you want the agent to work in. It looks like this:

```bash
claude mcp add --transport http portainer-command https://your-portainer/addons/portainer-command/mcp \
  --header "X-API-Key: ptr_…" \
  --header "X-Command-Token: cmd_…"
```

For any client that reads a JSON configuration file, such as **Cursor**, **Windsurf**, or **Claude Desktop**, add the `mcp.json` snippet shown:

```json
{
  "mcpServers": {
    "portainer-command": {
      "type": "http",
      "url": "https://your-portainer/addons/portainer-command/mcp",
      "headers": {
        "X-API-Key": "ptr_…",
        "X-Command-Token": "cmd_…"
      }
    }
  }
}
```

Click **Done**.
{% endstep %}
{% endstepper %}

Each client needs both headers. `X-API-Key` is the instance-wide transport key that gets requests past Portainer's add-on gateway. It's the same for everyone and identifies nobody. `X-Command-Token` is your connection's own token, and it's what identifies you.

To try it out, ask your agent to list the environments it can reach.

{% hint style="info" %}
To propose changes, you also need to [connect to GitHub](connect-to-github.md). Without a Git credential, your agent can read but has nothing to open a pull request with.
{% endhint %}

## Manage your connections

The **Connect your agent** page lists your active connections, who each one acts as, when it was **Last used** and by which client (for example `claude-code`), and when it **Expires**. Connections expire after 90 days.

To revoke a connection, click **Revoke** and then **Revoke connection**. This immediately:

* Stops the agent from authenticating. It can't be reconnected; you add a new connection instead.
* Ends any sessions the connection holds.
* Deletes the Portainer access token Portainer-Command created for it.
* Destroys a workspace running on it. A stopped workspace is left in place, but can't start again.

Revoke a connection whenever a machine is lost, a token might have leaked, or you stop using an agent.

{% hint style="info" %}
Deleting a Portainer user doesn't revoke their connections. Revoke the connections too, or have an administrator use [Emergency halt](admin/users.md#emergency-halt) on the user.
{% endhint %}
