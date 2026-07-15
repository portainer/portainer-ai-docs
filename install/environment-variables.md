# Environment Variables

Portainer-Run is configured entirely through environment variables, and the same set applies regardless of whether you're running it via [Kubernetes](kubernetes.md), [Docker](docker.md), or [Docker Compose](compose.md). This page is the single reference for all of them.

## Required

| Variable         | Description                                                                                                                                                                                                                 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `PORTAINER_URL`  | Full URL of your Portainer instance, for example `https://portainer.example.com:9443`.                                                                                                                                      |
| `ENCRYPTION_KEY` | 32+ character random string used to encrypt Git target credentials at rest. Generate once with `openssl rand -hex 32` and never change it. A different or missing key on redeploy makes existing Git targets undecryptable. |

## AI provider

| Variable            | Default  | Description                                                                                                                                 |
| ------------------- | -------- | ------------------------------------------------------------------------------------------------------------------------------------------- |
| `ANTHROPIC_API_KEY` | None     | Enables the [Assistant](../user/assistant.md) using Claude.                                                                                 |
| `OPENAI_API_KEY`    | None     | Enables the Assistant using GPT-4o. Use one or the other; if both are set, Anthropic takes priority unless `AI_PROVIDER` is set explicitly. |
| `AI_PROVIDER`       | auto     | Force `anthropic` or `openai` explicitly, overriding the auto-detection based on which key is set.                                          |
| `OPENAI_MODEL`      | `gpt-4o` | OpenAI model override. No effect when using Anthropic.                                                                                      |

Without either `ANTHROPIC_API_KEY` or `OPENAI_API_KEY` set, the Assistant panel is not shown at all.

## Deployment behavior

| Variable      | Default | Description                                                                                                                                                                                                                                    |
| ------------- | ------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `BASE_DOMAIN` | None    | Base domain for Ingress exposure. Deployed applications default to `appname.BASE_DOMAIN` as the host when set.                                                                                                                                 |
| `GATEWAY_URL` | None    | URL of the file relay gateway (`https://run-gateway.portainer.ai`). Required for staged file uploads; when set, large [Deploy](../user/deploy.md) uploads route through the gateway instead of transferring inline over [MCP](../user/mcp.md). |

## Networking and TLS

| Variable       | Default | Description                                                                                                         |
| -------------- | ------- | ------------------------------------------------------------------------------------------------------------------- |
| `PORT`         | `443`   | HTTPS listen port.                                                                                                  |
| `HTTP_PORT`    | `80`    | HTTP redirect port.                                                                                                 |
| `SSL_CERT`     | None    | Path to a TLS certificate file. A self-signed certificate is generated on first start if unset.                     |
| `SSL_KEY`      | None    | Path to a TLS private key file. Self-signed if unset.                                                               |
| `SSL_CERT_DIR` | `/app`  | Directory used to store the generated self-signed certificate. Has no effect when `SSL_CERT` and `SSL_KEY` are set. |

## Storage

| Variable    | Default     | Description                                                                                                                                                                                                          |
| ----------- | ----------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `CACHE_DIR` | `/app/data` | Directory used for the SQLite database (encrypted Git target credentials) and the deployment status cache. Change only if you also update your volume or PVC mount to match. Never mount storage over `/app` itself. |

## Where these are set on each platform

* **Kubernetes**: split between the `portainer-run-config` ConfigMap (non-sensitive values) and the `portainer-run-secret` Secret (`ENCRYPTION_KEY` and AI provider keys) in the canonical `kubernetes.yaml`. See [Kubernetes](kubernetes.md).
* **Docker**: passed as `-e` flags on `docker run`. See [Docker](docker.md).
* **Docker Compose**: set in the `environment` block (or an `.env` file) of the canonical `docker-compose.yml`. See [Docker Compose](compose.md).
