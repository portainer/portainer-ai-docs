# Overview

Portainer-Run ships as a single container image (`portainer/portainer-run:latest`), a React/Vite frontend served by a Node.js proxy. How you run that container is up to you; pick the page that matches your platform for full detail.

<table data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><h3>Kubernetes</h3></td><td>Run Portainer-Run itself as a workload on a Kubernetes cluster, using the canonical <code>kubernetes.yaml</code> manifest.</td><td><a href="kubernetes.md">kubernetes.md</a></td></tr><tr><td><h3>Docker</h3></td><td>Run Portainer-Run as a standalone container with <code>docker run</code>.</td><td><a href="docker.md">docker.md</a></td></tr><tr><td><h3>Docker Compose</h3></td><td>Run Portainer-Run via the canonical <code>docker-compose.yml</code> file</td><td><a href="compose.md">compose.md</a></td></tr></tbody></table>

All three routes converge on the same two required settings, so it's worth having these ready before you start:

<table><thead><tr><th width="190">Variable</th><th width="116">Required</th><th>Notes</th></tr></thead><tbody><tr><td><code>PORTAINER_URL</code></td><td>Yes</td><td>Full URL of your Portainer instance, for example <code>https://portainer.example.com:9443</code></td></tr><tr><td><code>ENCRYPTION_KEY</code></td><td>Yes</td><td>32+ character random string used to encrypt Git target credentials at rest. Generate once with <code>openssl rand -hex 32</code> and never change it. A different or missing key on redeploy makes existing Git targets undecryptable.</td></tr></tbody></table>

Every installation route also needs persistent storage mounted at `/app/data` inside the container. That's where Portainer-Run keeps its SQLite database (encrypted Git target credentials) and its deployment status cache. Without it, both are lost on every restart. Never mount storage over `/app` itself, only `/app/data`.

For the full list of optional environment variables (AI provider keys, feature flags, TLS, ports, storage), see [Environment Variables](environment-variables.md).
