# MCP

Portainer-Run exposes a Model Context Protocol (MCP) endpoint at `POST /mcp`, so AI coding tools can deploy applications directly, with no browser and no human clicking through the UI at all. This is the same governed pipeline as [Deploy](deploy.md), just driven by an MCP-capable client instead of the web interface.

## Connect your agent

Every user has a **Connect your agent** page, under **AI agents** in the navigation. It creates the Portainer API key your AI assistant uses and walks you through adding Portainer-Run to Claude Desktop, so you don't need to create a token in Portainer yourself.

1. Select **New key**, name it (for example after the computer it's on), and enter your Portainer password if asked. Portainer only asks for it when you sign in with Portainer's own user database, not with LDAP or OAuth.
2. Copy the key. It's shown once.
3. Choose how to connect Claude Desktop:
   * **Extension (recommended)**: download the `portainer-run.mcpb` extension, open it to install it in Claude Desktop, and paste the key when asked. The address is filled in for you, and Claude Desktop keeps the key in your system's keychain.
   * **Config file**: for macOS, Linux, or Windows, paste the generated configuration into `claude_desktop_config.json`, then quit and reopen Claude Desktop. This route needs Node.js installed, as described below.

The assistant acts as you, with all of your Portainer rights. Keys never expire, so treat a key like a password. The page lists your keys, and you can revoke any of them; an assistant using a revoked key stops working straight away. If Portainer uses a self-signed or internal certificate, give the extension that certificate (or its CA) in its optional certificate setting.

## Connecting Claude Desktop manually

The following procedure connects Claude Desktop to your Portainer-Run instance so you can work with it from a Claude chat.

### Before you begin

You will need:

* [Claude Desktop](https://claude.com/download) installed and signed in.
* [Node.js version 18 or later](https://nodejs.org/en/download). Node.js includes `npx`, which Claude Desktop uses to run the connector.
* Your Portainer-Run URL and [Portainer API token](https://app.gitbook.com/s/MmwXfSb4bP3JyB8BLrAf/user/account-settings#access-tokens).

### Add Portainer-Run configuration to Claude desktop

These details differ by operating system, select Windows, MacOS or Linux below.

{% tabs %}
{% tab title="Windows" %}
1. Open Claude Desktop.
2. Select your name at the bottom left, then select **Settings**.
3. In the left menu, select **Developer**.
4. Select **Edit Config**. A file window opens and highlights `claude_desktop_config.json`.
5. Right-click the file, select **Open with**, then select **Notepad**.
6. Add the configuration for your system to the file.
   1. If the file is empty or contains only `{}`, select all the text in the file, delete it, and paste the full configuration for your system.
   2.  If the file already contains other settings, don’t delete them.

       1. If the file already has an `"mcpServers": {` section, add a comma after the last entry in that section, then paste the `"portainer-run": { ... }` block on a new line below it.
       2. If the file doesn’t have an `"mcpServers": {` section, add a comma after the last setting in the file, then paste the whole `"mcpServers": { ... }` section on a new line below it.

       In both cases, don’t copy the outer `{` and `}` from the configuration.

{% hint style="info" %}
If you are using a self-signed certificate, you will need the following environment variable set:

```json
"env": { "NODE_TLS_REJECT_UNAUTHORIZED": "0" }
```

For example:

```json
"mcpServers": {
  "portainer-run": {
    "command": "cmd",
    "args": [
      "/c",
      "npx",
      "mcp-remote@latest",
      "https://<your-portainer-host>/addons/portainer-run/mcp",
      "--header",
      "X-API-Key: YOUR_PORTAINER_TOKEN"
    ],
    "env": { "NODE_TLS_REJECT_UNAUTHORIZED": "0" }
  }
}
```
{% endhint %}

```json
{
  "mcpServers": {
    "portainer-run": {
      "command": "cmd",
      "args": [
        "/c",
        "npx",
        "mcp-remote@latest",
        "https://<your-portainer-host>/addons/portainer-run/mcp",
        "--header",
        "X-API-Key: YOUR_PORTAINER_TOKEN"
      ]
    }
  }
}
```

7. Replace `<your-portainer-host>` with your Portainer address (for example `portainer.example.com`) and keep `/addons/portainer-run/mcp` at the end. Replace `YOUR_PORTAINER_TOKEN` with your [API token](https://app.gitbook.com/s/MmwXfSb4bP3JyB8BLrAf/user/account-settings#access-tokens). Keep the quotation marks.
8. Save the file and close the editor.
9. Restart Claude Desktop. Claude Desktop only reads the configuration when it starts, and closing the window doesn’t quit the app. Select the arrow at the right of the taskbar to show hidden icons, right-click the **Claude** icon, and select **Quit**.
{% endtab %}

{% tab title="MacOS" %}
1. Open Claude Desktop.
2. Select your name at the bottom left, then select **Settings**.
3. In the left menu, select **Developer**.
4. Select **Edit Config**. A file window opens and highlights `claude_desktop_config.json`.
5. Right-click the file, select **Open With**, then select **TextEdit**. If the file appears with formatting options, select **Format** > **Make Plain Text**.
6. Add the configuration for your system to the file.
   1. If the file is empty or contains only `{}`, select all the text in the file, delete it, and paste the full configuration for your system.
   2.  If the file already contains other settings, don’t delete them.

       1. If the file already has an `"mcpServers": {` section, add a comma after the last entry in that section, then paste the `"portainer-run": { ... }` block on a new line below it.
       2. If the file doesn’t have an `"mcpServers": {` section, add a comma after the last setting in the file, then paste the whole `"mcpServers": { ... }` section on a new line below it.

       In both cases, don’t copy the outer `{` and `}` from the configuration.

{% hint style="info" %}
If you are using a self-signed certificate, you will need the following environment variable set:

```json
"env": { "NODE_TLS_REJECT_UNAUTHORIZED": "0" }
```

For example:

```json
"mcpServers": {
  "portainer-run": {
    "command": "npx",
    "args": [
        "mcp-remote@latest",
        "https://<your-portainer-host>/addons/portainer-run/mcp",
        "--header",
        "X-API-Key: YOUR_PORTAINER_TOKEN"
    ],
    "env": { "NODE_TLS_REJECT_UNAUTHORIZED": "0" }
  }
}
```
{% endhint %}

```json
{
  "mcpServers": {
    "portainer-run": {
      "command": "npx",
      "args": [
        "mcp-remote@latest",
        "https://<your-portainer-host>/addons/portainer-run/mcp",
        "--header",
        "X-API-Key: YOUR_PORTAINER_TOKEN"
      ]
    }
  }
}
```

7. Replace `<your-portainer-host>` with your Portainer address (for example `portainer.example.com`) and keep `/addons/portainer-run/mcp` at the end. Replace `YOUR_PORTAINER_TOKEN` with your [API token](https://app.gitbook.com/s/MmwXfSb4bP3JyB8BLrAf/user/account-settings#access-tokens). Keep the quotation marks.
8. Save the file and close the editor.
9. Restart Claude Desktop. Claude Desktop only reads the configuration when it starts, and closing the window doesn’t quit the app. Select **Claude** in the menu bar, then select **Quit Claude**, or press **Command+Q**.
{% endtab %}

{% tab title="Linux" %}
{% hint style="info" %}
Claude Linux support is in beta.
{% endhint %}

1. Open Claude Desktop.
2. Select **Claude** in the menu bar, then select **Settings**.
3. In the left menu, select **Developer**.
4. Select **Edit Config**. A file window opens and highlights `claude_desktop_config.json`.
5. Open `claude_desktop_config.json` in your text editor, such as **Text Editor** (GNOME) or **Kate** (KDE).
6. Add the configuration for your system to the file.
   1. If the file is empty or contains only `{}`, select all the text in the file, delete it, and paste the full configuration for your system.
   2.  If the file already contains other settings, don’t delete them.

       1. If the file already has an `"mcpServers": {` section, add a comma after the last entry in that section, then paste the `"portainer-run": { ... }` block on a new line below it.
       2. If the file doesn’t have an `"mcpServers": {` section, add a comma after the last setting in the file, then paste the whole `"mcpServers": { ... }` section on a new line below it.

       In both cases, don’t copy the outer `{` and `}` from the configuration.

{% hint style="info" %}
If you are using a self-signed certificate, you will need the following environment variable set:

```json
"env": { "NODE_TLS_REJECT_UNAUTHORIZED": "0" }
```

For example:

```json
"mcpServers": {
  "portainer-run": {
    "command": "npx",
    "args": [
        "mcp-remote@latest",
        "https://<your-portainer-host>/addons/portainer-run/mcp",
        "--header",
        "X-API-Key: YOUR_PORTAINER_TOKEN"
    ],
    "env": { "NODE_TLS_REJECT_UNAUTHORIZED": "0" }
  }
}
```
{% endhint %}

```json
{
  "mcpServers": {
    "portainer-run": {
      "command": "npx",
      "args": [
        "mcp-remote@latest",
        "https://<your-portainer-host>/addons/portainer-run/mcp",
        "--header",
        "X-API-Key: YOUR_PORTAINER_TOKEN"
      ]
    }
  }
}
```

7. Replace `<your-portainer-host>` with your Portainer address (for example `portainer.example.com`) and keep `/addons/portainer-run/mcp` at the end. Replace `YOUR_PORTAINER_TOKEN` with your [API token](https://app.gitbook.com/s/MmwXfSb4bP3JyB8BLrAf/user/account-settings#access-tokens). Keep the quotation marks.
8. Save the file and close the editor.
9. Restart Claude Desktop. Close the window, then quit from the app’s menu or tray icon. If Claude Desktop still seems to be running, enter `pkill -f claude-desktop` in Terminal. Then reopen it from your applications menu.
{% endtab %}
{% endtabs %}

### Verify your connection

1. In Claude desktop, go to **Settings** > **Developer**.
2. Confirm that **portainer-run** is listed with a **Running** status. The first start can take a few seconds while the connector downloads.
3. Start a new chat and try out some of the [available tools](mcp.md#available-tools).

## Available tools

{% hint style="info" %}
Whatever you see and do in Portainer is exactly what it can see and do through MCP; the same RBAC boundary applies. See [Roles and RBAC](../architecture/roles.md).
{% endhint %}

<table><thead><tr><th width="209">Tool</th><th>What it does</th></tr></thead><tbody><tr><td><code>get_deploy_options</code></td><td>Call this before deploying. Returns what Portainer-Run would use for this user, the way the Deploy page would: where the app would run (environment and project space), its web address, and where it's stored. It also returns <code>questions</code>, listing only the choices Portainer-Run can't make itself (often none). Accepts optional <code>appName</code>, <code>envId</code>, <code>namespace</code>, <code>gitTargetId</code> and <code>ingress</code>. If the user can't deploy yet, it returns <code>blocked</code> with a message.</td></tr><tr><td><code>list_environments</code></td><td>For technical users. Returns Kubernetes environments accessible with the provided token, excluding any an admin has disabled from deploy flows via <a href="admin/cluster-readiness.md">Cluster Readiness</a>.</td></tr><tr><td><code>list_namespaces</code></td><td>For technical users. Returns namespaces in a given environment, filtered to exclude system namespaces.</td></tr><tr><td><code>list_git_targets</code></td><td>For technical users. Returns Git targets accessible to the caller (their own targets plus shared targets). If none exist, also returns a message explaining that a Git target is required and must be created in the Portainer-Run UI. <strong>Git targets can't be created via MCP.</strong></td></tr><tr><td><code>list_ingress_classes</code></td><td>For technical users. Returns the IngressClasses defined in an environment (including which one is the cluster default), plus <code>baseDomain</code> and <code>ingressHostRequired</code>. Use it to pick an ingress class when deploying with <code>exposeType: "Ingress"</code>. When <code>ingressHostRequired</code> is <code>true</code>, no base domain is configured, so a full <code>ingress.host</code> must be supplied.</td></tr><tr><td><code>request_upload_session</code></td><td>Starts a file upload for a deployment. Takes no parameters and returns a <code>sessionId</code>, an <code>uploadUrl</code> and an <code>expiresAt</code> time. Upload the complete set of files to <code>uploadUrl</code>, then pass the <code>sessionId</code> to <code>deploy_app</code> as <code>stagedSessionId</code>. A session can be used once and expires after 5 minutes.</td></tr><tr><td><code>deploy_app</code></td><td><p>Deploys previously uploaded source files via the full <a href="deploy.md">Deploy</a> pipeline.</p><p>Accepts <code>appName</code>, <code>envId</code>, <code>namespace</code>, <code>gitTargetId</code> and <code>stagedSessionId</code> (from <code>request_upload_session</code>), and optional <code>envVars</code> (an array of <code>{ key, value }</code>), <code>ingress</code> (<code>{ host, path, ingressClass }</code>), <code>runtime</code>, <code>branch</code> (defaults to <code>main</code>), <code>size</code> (<code>S</code>, <code>M</code> or <code>L</code>; defaults to <code>S</code>) and <code>sizeReason</code>, which is required with <code>M</code> or <code>L</code>. See <a href="deploy.md#size">Size</a>. Runtime is auto-detected by default; pass <code>runtime</code> (<code>node</code>, <code>python</code>, <code>php</code>, <code>ruby</code>, or <code>nginx</code>) to override, for example <code>nginx</code> to serve a static site.</p><p>Parses <code>.env.example</code> for environment variables if <code>envVars</code> isn't supplied.</p><p>Applications deployed over MCP are always exposed through an Ingress. The host defaults to <code>&#x3C;appName>.&#x3C;base domain></code> if a base domain is set in <a href="admin/settings.md">Settings</a>, and <code>ingressClass</code> defaults to the cluster's default IngressClass if not supplied.</p><p>Returns a <code>url</code> for accessing the app, or <code>null</code> if the address is still being assigned, in which case <code>get_app_status</code> returns it once ready.</p></td></tr><tr><td><code>get_app_status</code></td><td>Returns the running status of a deployed application, with a plain summary (Running, Starting up, Stopped, or Having trouble) and a live access <code>url</code> resolved from the Service or Ingress. Takes <code>appName</code>, and <code>envId</code> and <code>namespace</code> when needed. If the app could be in more than one place, it returns a question to ask instead.</td></tr><tr><td><code>redeploy_app</code></td><td>Redeploys an app so its pods pull the latest image of its runtime, for example after a Node.js security release. It commits the change to the app's manifest in Git; Portainer applies it straight away if the caller deployed the app, and otherwise the next time it applies the stack from Git. Needs edit access to the app. Takes <code>appName</code>, and <code>envId</code> and <code>namespace</code> when needed. Call <code>get_app_status</code> afterwards to see it running again.</td></tr></tbody></table>

## The file relay gateway

MCP deployments never send files inline in the `deploy_app` call. The files are uploaded to `uploadUrl` (from `request_upload_session`) through Portainer's hosted relay, `https://run-gateway.portainer.ai`, which avoids the payload-size limits some MCP clients or transports impose. This means the **Gateway URL** must be set in [Settings](admin/settings.md); without it `deploy_app` fails with "GATEWAY\_URL is not configured". There's nothing for the calling MCP client to configure differently.
