# Docker

This is the most direct way to run Portainer-Run: a single container on any host that can reach your Portainer instance and your Git provider.

## Build the image

If you're building from source rather than pulling `portainer/portainer-run:latest`:

```bash
DOCKER_BUILDKIT=0 docker build -t portainer-run .
```

## Minimal run (self-signed certificate, persistent data)

```bash
docker run -d \
  -p 443:443 \
  -p 80:80 \
  -v portainer-run-data:/app/data \
  -e PORTAINER_URL=https://portainer.example.com:9443 \
  -e ENCRYPTION_KEY=change-me-to-a-random-32-char-string \
  -e ANTHROPIC_API_KEY=sk-ant-... \
  --name portainer-run \
  portainer/portainer-run:latest
```

This is enough to get a fully working instance with the Assistant enabled, using a self-signed certificate valid for 3 years. Your browser will show a certificate warning on first access; accept the exception to proceed.

## Run with OpenAI instead of Anthropic

```bash
docker run -d \
  -p 443:443 \
  -p 80:80 \
  -v portainer-run-data:/app/data \
  -e PORTAINER_URL=https://portainer.example.com:9443 \
  -e ENCRYPTION_KEY=change-me-to-a-random-32-char-string \
  -e OPENAI_API_KEY=sk-... \
  --name portainer-run \
  portainer/portainer-run:latest
```

Set one key or the other, not both. If both are present, Anthropic takes priority unless you set `AI_PROVIDER` explicitly.

## Run with real TLS certificates

```bash
docker run -d \
  -p 443:443 \
  -p 80:80 \
  -v portainer-run-data:/app/data \
  -v /etc/letsencrypt/live/portainer-run.example.com:/certs:ro \
  -e PORTAINER_URL=https://portainer.example.com:9443 \
  -e ENCRYPTION_KEY=change-me-to-a-random-32-char-string \
  -e ANTHROPIC_API_KEY=sk-ant-... \
  -e SSL_CERT=/certs/fullchain.pem \
  -e SSL_KEY=/certs/privkey.pem \
  --name portainer-run \
  portainer/portainer-run:latest
```

## Run on custom ports

```bash
docker run -d \
  -p 8443:8443 \
  -p 8080:8080 \
  -v portainer-run-data:/app/data \
  -e PORTAINER_URL=https://portainer.example.com:9443 \
  -e ENCRYPTION_KEY=change-me-to-a-random-32-char-string \
  -e PORT=8443 \
  -e HTTP_PORT=8080 \
  --name portainer-run \
  portainer/portainer-run:latest
```

## Persistent data

Mount a named volume at `/app/data`. This holds:

* `portainer-run.db`: a SQLite database storing encrypted Git target credentials.
* `cache.json`: the deployment status cache, used to show last-known state immediately on reconnect.

Without this volume, both are lost every time the container restarts and Git targets will need to be recreated. Never mount a volume over `/app` itself, only `/app/data`. Doing so overrides the application.

## Health check

Portainer-Run exposes a lightweight health endpoint at `GET /config`. The canonical [Docker Compose file](https://github.com/portainer/portainer-run/blob/develop/deploy/docker-compose.yml) and [Kubernetes manifest](https://github.com/portainer/portainer-run/blob/develop/deploy/kubernetes.yaml) both use this endpoint for their respective healthcheck or probe configuration. It's worth wiring into your own monitoring if you're running via plain `docker run` without either of those.

## Troubleshooting DNS resolution

If the container can't resolve your Portainer hostname (`EAI_AGAIN`), add `--dns 8.8.8.8` to the run command.

## Environment variables

`PORTAINER_URL` and `ENCRYPTION_KEY` are the only two required variables, both shown in the examples above. For the full list, including AI provider settings, TLS, ports, and storage options, see [Environment Variables](environment-variables.md).

## Next step

Once the container is running, jump to [Quick Start](../quick-start.md) step 3 onward to connect Portainer and deploy your first app.
