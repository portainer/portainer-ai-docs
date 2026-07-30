# MCP

Portainer-Run exposes a Model Context Protocol (MCP) endpoint at `POST /mcp`, so AI coding tools can deploy applications directly, with no browser and no human clicking through the UI at all. This is the same governed pipeline as [Deploy](deploy.md), just driven by an MCP-capable client instead of the web interface.

## Authentication

Authenticate with either:

* `X-API-Key: <portainer-token>`
* `Authorization: Bearer <portainer-token>`

The token is a Portainer personal access token, validated against Portainer's `/users/me` endpoint on first use and cached for five minutes. Whatever that token can see and do in Portainer is exactly what it can see and do through MCP; the same RBAC boundary applies. See [Roles and RBAC](../architecture/roles.md).

## Workflow guidance

The server returns workflow guidance in the MCP `initialize` response (`instructions`). Compliant clients surface this to the model automatically, so it knows to gather the required deployment details (environment, namespace, Git target, exposure type, ingress host, port behavior) and confirm them with the user before deploying, without the user needing to prompt for that explicitly.

## Available tools

<table><thead><tr><th width="209">Tool</th><th>What it does</th></tr></thead><tbody><tr><td><code>list_environments</code></td><td>Returns Kubernetes environments accessible with the provided token, excluding any an admin has disabled from deploy flows via <a href="admin/cluster-readiness.md">Cluster Readiness</a>.</td></tr><tr><td><code>list_namespaces</code></td><td>Returns namespaces in a given environment, filtered to exclude system namespaces.</td></tr><tr><td><code>list_git_targets</code></td><td>Returns Git targets accessible to the caller (their own targets plus shared targets). If none exist, also returns a message explaining that a Git target is required and must be created in the Portainer-Run UI. <strong>Git targets can't be created via MCP.</strong></td></tr><tr><td><code>list_ingress_classes</code></td><td>Returns the IngressClasses defined in an environment (including which one is the cluster default), plus <code>baseDomain</code> and <code>ingressHostRequired</code>. Use it to pick an ingress class when deploying with <code>exposeType: "Ingress"</code>. When <code>ingressHostRequired</code> is <code>true</code>, no base domain is configured, so a full <code>ingress.host</code> must be supplied.</td></tr><tr><td><code>deploy_app</code></td><td><p>Deploys source files via the full <a href="deploy.md">Deploy</a> pipeline. </p><p></p><p>Accepts <code>appName</code>, <code>envId</code>, <code>namespace</code>, <code>gitTargetId</code>, <code>files</code> (an array of <code>{ path, content }</code>), and optional <code>envVars</code>, <code>exposeType</code>, <code>ingress</code> (<code>{ host, path, ingressClass }</code>), <code>runtime</code>, and <code>branch</code>. Runtime is auto-detected by default; pass <code>runtime</code> (<code>node</code>, <code>python</code>, <code>php</code>, <code>ruby</code>, or <code>nginx</code>) to override, for example <code>nginx</code> to serve a static site. </p><p></p><p>Parses <code>.env.example</code> for environment variables if <code>envVars</code> isn't supplied. </p><p></p><p>Returns a <code>url</code> for accessing the app: immediately for Ingress, or after a short poll for NodePort/LoadBalancer (<code>null</code> if the address is still being assigned, in which case <code>get_app_status</code> returns it once ready). </p><p></p><p>When <code>exposeType</code> is <code>Ingress</code>, the host defaults to <code>&#x3C;appName>.&#x3C;BASE_DOMAIN></code> if <code>BASE_DOMAIN</code> is set, and <code>ingressClass</code> defaults to the cluster's default IngressClass if not supplied. </p><p></p><p>Only available when <code>FEATURE_VIBE_DEPLOY</code> is enabled.</p></td></tr><tr><td><code>get_app_status</code></td><td>Returns the running status of a deployed application, plus a live access <code>url</code> resolved from the Service or Ingress.</td></tr></tbody></table>

## Connecting Claude Desktop

Add the following to `claude_desktop_config.json` (requires Node.js, for `mcp-remote`):

{% tabs %}
{% tab title="Windows" %}
To avoid pathing issues on Windows, you may need to wrap the call to `npx` in `cmd`:

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
    ]
  }
}
```
{% endtab %}

{% tab title="MacOS" %}
```json
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
```
{% endtab %}
{% endtabs %}

Config file location:

* **Windows:** `%APPDATA%\Claude\claude_desktop_config.json`  or `%LOCALAPPDATA%\Packages\Claude_pzs8sxrjxfjjc\LocalCache\Roaming\Claude` depending on how it was installed
* **macOS:** `~/Library/Application Support/Claude/claude_desktop_config.json`

## Verifying the endpoint

Before connecting a client, confirm the endpoint is responding correctly:

```bash
curl -k -X POST https://your-portainer-run/mcp \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -d '{"jsonrpc":"2.0","id":1,"method":"tools/list","params":{}}'
```

A working endpoint returns a JSON-RPC response listing the tools above.

## The file relay gateway

MCP transfers files inline as part of the `deploy_app` call by default, which works fine for typical AI-generated source drops. For larger applications, uploads are routed through `https://run-gateway.portainer.ai`, Portainer's hosted relay. Large [Deploy](deploy.md) uploads route through the gateway instead of being transferred inline over the MCP connection, avoiding payload-size limits some MCP clients or transports impose. This is a server-side setting; there's nothing for the calling MCP client to configure differently.
