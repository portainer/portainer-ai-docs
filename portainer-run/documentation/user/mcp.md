# MCP

Portainer-Run exposes a Model Context Protocol (MCP) endpoint at `POST /mcp`, so AI coding tools can deploy applications directly, with no browser and no human clicking through the UI at all. This is the same governed pipeline as [Deploy](deploy.md), just driven by an MCP-capable client instead of the web interface.

## Connecting Claude Desktop

The following procedure connects Claude Desktop to your Portainer-Run instance so you can work with it from a Claude chat.&#x20;

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
6. Add the configuration for your system to the file.&#x20;
   1. If the file is empty or contains only `{}`, select all the text in the file, delete it, and paste the full configuration for your system.&#x20;
   2.  If the file already contains other settings, don’t delete them.&#x20;

       1. If the file already has an `"mcpServers": {` section, add a comma after the last entry in that section, then paste the `"portainer-run": { ... }` block on a new line below it.&#x20;
       2. If the file doesn’t have an `"mcpServers": {` section, add a comma after the last setting in the file, then paste the whole `"mcpServers": { ... }` section on a new line below it.&#x20;

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
      "https://your-portainer-run/mcp",
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
        "https://your-portainer-run/mcp",
        "--header",
        "X-API-Key: YOUR_PORTAINER_TOKEN"
      ]
    }
  }
}
```

7. Replace `https://your-portainer-run` with your Portainer-Run URL (keep `/mcp` at the end of the URL), and `YOUR_PORTAINER_TOKEN` with your [API token](https://app.gitbook.com/s/MmwXfSb4bP3JyB8BLrAf/user/account-settings#access-tokens). Keep the quotation marks.
8. Save the file and close the editor.&#x20;
9. Restart Claude Desktop. Claude Desktop only reads the configuration when it starts, and closing the window doesn’t quit the app. Select the arrow at the right of the taskbar to show hidden icons, right-click the **Claude** icon, and select **Quit**.
{% endtab %}

{% tab title="MacOS" %}
1. Open Claude Desktop.
2. Select your name at the bottom left, then select **Settings**.
3. In the left menu, select **Developer**.
4. Select **Edit Config**. A file window opens and highlights `claude_desktop_config.json`.
5. Right-click the file, select **Open With**, then select **TextEdit**. If the file appears with formatting options, select **Format** > **Make Plain Text**.
6. Add the configuration for your system to the file.&#x20;
   1. If the file is empty or contains only `{}`, select all the text in the file, delete it, and paste the full configuration for your system.&#x20;
   2.  If the file already contains other settings, don’t delete them.&#x20;

       1. If the file already has an `"mcpServers": {` section, add a comma after the last entry in that section, then paste the `"portainer-run": { ... }` block on a new line below it.&#x20;
       2. If the file doesn’t have an `"mcpServers": {` section, add a comma after the last setting in the file, then paste the whole `"mcpServers": { ... }` section on a new line below it.&#x20;

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
        "https://your-portainer-run/mcp",
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
        "https://your-portainer-run/mcp",
        "--header",
        "X-API-Key: YOUR_PORTAINER_TOKEN"
      ]
    }
  }
}
```

7. Replace `https://your-portainer-run` with your Portainer-Run URL (keep `/mcp` at the end of the URL), and `YOUR_PORTAINER_TOKEN` with your [API token](https://app.gitbook.com/s/MmwXfSb4bP3JyB8BLrAf/user/account-settings#access-tokens). Keep the quotation marks.
8. Save the file and close the editor.&#x20;
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
6. Add the configuration for your system to the file.&#x20;
   1. If the file is empty or contains only `{}`, select all the text in the file, delete it, and paste the full configuration for your system.&#x20;
   2.  If the file already contains other settings, don’t delete them.&#x20;

       1. If the file already has an `"mcpServers": {` section, add a comma after the last entry in that section, then paste the `"portainer-run": { ... }` block on a new line below it.&#x20;
       2. If the file doesn’t have an `"mcpServers": {` section, add a comma after the last setting in the file, then paste the whole `"mcpServers": { ... }` section on a new line below it.&#x20;

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
        "https://your-portainer-run/mcp",
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
        "https://your-portainer-run/mcp",
        "--header",
        "X-API-Key: YOUR_PORTAINER_TOKEN"
      ]
    }
  }
}
```

7. Replace `https://your-portainer-run` with your Portainer-Run URL (keep `/mcp` at the end of the URL), and `YOUR_PORTAINER_TOKEN` with your [API token](https://app.gitbook.com/s/MmwXfSb4bP3JyB8BLrAf/user/account-settings#access-tokens). Keep the quotation marks.
8. Save the file and close the editor.&#x20;
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

<table><thead><tr><th width="209">Tool</th><th>What it does</th></tr></thead><tbody><tr><td><code>list_environments</code></td><td>Returns Kubernetes environments accessible with the provided token, excluding any an admin has disabled from deploy flows via Cluster Readiness.</td></tr><tr><td><code>list_namespaces</code></td><td>Returns namespaces in a given environment, filtered to exclude system namespaces.</td></tr><tr><td><code>list_git_targets</code></td><td>Returns Git targets accessible to the caller (their own targets plus shared targets). If none exist, also returns a message explaining that a Git target is required and must be created in the Portainer-Run UI. <strong>Git targets can't be created via MCP.</strong></td></tr><tr><td><code>list_ingress_classes</code></td><td>Returns the IngressClasses defined in an environment (including which one is the cluster default), plus <code>baseDomain</code> and <code>ingressHostRequired</code>. Use it to pick an ingress class when deploying with <code>exposeType: "Ingress"</code>. When <code>ingressHostRequired</code> is <code>true</code>, no base domain is configured, so a full <code>ingress.host</code> must be supplied.</td></tr><tr><td><code>request_upload_session</code></td><td><p>Starts a staged upload through the file relay gateway. Returns an <code>uploadUrl</code> and a <code>sessionId</code>. The client POSTs the complete source files to <code>uploadUrl</code> as a JSON array of <code>{ path, content }</code>, then passes <code>sessionId</code> to <code>deploy_app</code>.</p><p>Each session can be used once and expires after 5 minutes.</p></td></tr><tr><td><code>deploy_app</code></td><td><p>Deploys source files via the full Deploy pipeline.</p><p>Accepts <code>appName</code>, <code>envId</code>, <code>namespace</code>, <code>gitTargetId</code>, and <code>stagedSessionId</code> (from <code>request_upload_session</code>, the only way to supply files), plus optional <code>envVars</code>, <code>ingress</code> (<code>{ host, path, ingressClass }</code>), <code>runtime</code>, and <code>branch</code>. Runtime is auto-detected by default; pass <code>runtime</code> (<code>node</code>, <code>python</code>, <code>php</code>, <code>ruby</code>, or <code>nginx</code>) to override, for example <code>nginx</code> to serve a static site.</p><p>Parses <code>.env.example</code> for environment variables if <code>envVars</code> isn't supplied.</p><p>Applications deployed over MCP are always exposed through the cluster's ingress controller. The host defaults to <code>&#x3C;appName>.&#x3C;BASE_DOMAIN></code> if <code>BASE_DOMAIN</code> is set, and <code>ingressClass</code> defaults to the cluster's default IngressClass if not supplied. There is no port parameter: the service port is the detected runtime's default, so the application should listen on that port.</p><p>Returns a <code>url</code> for accessing the app (<code>null</code> if the address is still being assigned, in which case <code>get_app_status</code> returns it once ready).</p><p>Requires a Gateway URL to be configured.</p></td></tr><tr><td><code>get_app_status</code></td><td>Returns the running status of a deployed application, plus a live access <code>url</code> resolved from the Service or Ingress.</td></tr></tbody></table>

## The file relay gateway

Every MCP deploy stages its source files through a file relay gateway: the client uploads the files to the relay with `request_upload_session`, and Portainer-Run fetches them from there when `deploy_app` runs. There's no inline transfer path, so **MCP deploys are disabled until an administrator sets a** **Gateway URL**. Until then, `request_upload_session` and `deploy_app` return an error that says so.

Portainer hosts a relay at `https://run-gateway.portainer.ai`. Application source passes through whichever relay you configure, so egress-restricted or air-gapped installs should host their own. Uploads from the browser go straight to Portainer-Run and don't use the relay.
