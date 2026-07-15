# Docker Compose

Portainer-Run ships a canonical Compose file, [`docker-compose.yml`](https://github.com/portainer/portainer-run/blob/develop/deploy/docker-compose.yml). Use that file as the source of truth. This page walks through what's in it and what to change before starting.

```bash
docker compose up -d
```

Or, with a separate environment file:

```bash
cp .env.example .env && docker compose --env-file .env up -d
```

## What you must change before starting

The file ships with two placeholders that need real values:

1. **`PORTAINER_URL`**: set this to your Portainer instance.
2.  **`ENCRYPTION_KEY`**: replace `change-me-to-a-rand-32-string` with a real generated value:

    ```bash
    openssl rand -hex 32
    ```

    This must stay identical across every future `docker compose up`. Changing or losing it makes existing Git target credentials undecryptable.

If you want the Assistant, also set `ANTHROPIC_API_KEY` (or comment that out and uncomment `OPENAI_API_KEY` instead, further down in the same block).

## What's in the file

The file is heavily commented and organized into clearly labeled sections inside the single `portainer-run` service:

| Section             | What it covers                                                                                            |
| ------------------- | --------------------------------------------------------------------------------------------------------- |
| Required            | `PORTAINER_URL`, `ENCRYPTION_KEY`                                                                         |
| AI provider         | `ANTHROPIC_API_KEY`, `OPENAI_API_KEY`, `OPENAI_MODEL`, `AI_PROVIDER`. Pick one provider.                  |
| Template catalogue  | `TEMPLATE_URL`, for pointing the Catalogue at a custom template file                                      |
| Ingress base domain | `BASE_DOMAIN`, for defaulting Ingress-exposed apps to `appname.BASE_DOMAIN`                               |
| File relay gateway  | `GATEWAY_URL`. See below.                                                                                 |
| Custom ports        | `PORT`, `HTTP_PORT`                                                                                       |
| Cache directory     | `CACHE_DIR`, if you need to relocate where the SQLite database and status cache live inside the container |
| TLS certificates    | `SSL_CERT`, `SSL_KEY`, `SSL_CERT_DIR`, paired with a cert volume mount                                    |

Most of these are commented out by default and only need uncommenting if you want that behavior. For a full description of every variable, defaults included, see [Environment Variables](environment-variables.md).

## The file relay gateway

`GATEWAY_URL` is required if you want staged file uploads for large [Vibe Deploy](../user/deploy.md) source drops via [MCP](../user/mcp.md). Leave it unset for standard browser-based uploads. See [Environment Variables](environment-variables.md) for the full description.

## Persisting data

The `portainer-run-data` named volume, mounted at `/app/data`, holds the SQLite database (encrypted Git target credentials) and the deployment status cache. It's enabled by default in the canonical file. Don't remove it unless you're fine losing Git targets and cached state on every restart.

## Real TLS certificates

Uncomment the certificate volume mount:

```yaml
# - /etc/letsencrypt/live/portainer-run.example.com:/certs:ro
```

and set `SSL_CERT` / `SSL_KEY` in the environment section to the paths inside that mount.

## DNS resolution

If the container can't resolve your Portainer hostname, uncomment the `dns:` block near the bottom of the service definition.

## Health check

The file defines a Docker healthcheck that calls `GET /config` over HTTPS on the container itself every 30 seconds, after an initial 15-second grace period. This is the same endpoint the Kubernetes manifest's liveness and readiness probes use (see [Kubernetes](kubernetes.md)).

## Next step

Once running, continue with [Quick Start](../quick-start.md) step 3 onward to connect Portainer and deploy your first app.
