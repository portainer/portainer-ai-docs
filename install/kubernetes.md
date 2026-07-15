# Kubernetes

Portainer-Run ships a canonical Kubernetes manifest, [`kubernetes.yaml`](https://github.com/portainer/portainer-run/blob/develop/deploy/kubernetes.yaml), that deploys it as a single pod in its own `portainer-run` namespace. Use that file as the source of truth. This page walks through what's in it and what to change before applying.

```bash
kubectl apply -f kubernetes.yaml
```

To remove everything it creates:

```bash
kubectl delete -f kubernetes.yaml
```

## What you must change before applying

Two things in the file are placeholders and won't work as shipped:

1. **`PORTAINER_URL`** in the `portainer-run-config` ConfigMap: set this to your Portainer instance. If Portainer-Run is running in the same cluster as Portainer, the file includes a commented-out in-cluster alternative (`https://portainer.portainer.svc.cluster.local:9443`). Uncomment it and adjust the namespace and service name to match your Portainer install.
2.  **`ENCRYPTION_KEY`** in the `portainer-run-secret` Secret: this ships empty and the container will not start without it. Generate one and base64-encode it before applying:

    ```bash
    openssl rand -hex 32 | base64
    ```

    Paste the output as the value of `ENCRYPTION_KEY` in the Secret. As with every other installation route, this value must stay identical across every future redeploy. Losing or changing it makes existing Git target credentials undecryptable.

If you want the Assistant, also base64-encode an `ANTHROPIC_API_KEY` (or switch to `OPENAI_API_KEY`, see the commented block in the `env` section of the container spec) into the same Secret.

For a full description of every variable used across the ConfigMap and Secret, defaults included, see [Environment Variables](environment-variables.md).

## What's in the file

| Resource                                     | Purpose                                                                                                                                                                                                        |
| -------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Namespace: portainer-run`                   | Isolates everything Portainer-Run creates.                                                                                                                                                                     |
| `ConfigMap: portainer-run-config`            | Non-sensitive configuration: `PORTAINER_URL` and feature flags.                                                                                                                                                |
| `Secret: portainer-run-secret`               | `ENCRYPTION_KEY` and AI provider key(s).                                                                                                                                                                       |
| `PersistentVolumeClaim: portainer-run-cache` | 1Gi volume mounted at `/app/data`, holding the SQLite Git target database and the deployment status cache.                                                                                                     |
| `Deployment: portainer-run`                  | The pod itself: single replica, `imagePullPolicy: Always`, liveness and readiness probes against `/config`, and modest default resource requests (50m CPU / 64Mi memory) and limits (500m CPU / 256Mi memory). |
| `Service: portainer-run`                     | `ClusterIP` by default, exposing ports 443 and 80.                                                                                                                                                             |
| `Ingress` (commented out)                    | A ready-to-uncomment example for exposing Portainer-Run through an nginx ingress controller.                                                                                                                   |

## Exposing Portainer-Run externally

The Service ships as `ClusterIP`, meaning it's only reachable inside the cluster by default. To expose it, either:

* **Uncomment the `Ingress` block** at the bottom of the file (requires an ingress controller such as nginx or Traefik already installed), and set `host` to your desired hostname. The example includes `ssl-passthrough` since Portainer-Run terminates its own TLS.
* **Change the Service `type`** to `NodePort` or `LoadBalancer` instead, per the comments directly above the `type:` field.

## Real TLS certificates instead of self-signed

By default Portainer-Run generates its own self-signed certificate on first start. To use a real one instead:

1.  Create a TLS Secret from your certificate files:

    ```bash
    kubectl create secret tls portainer-run-tls \
      --cert=/path/to/fullchain.pem \
      --key=/path/to/privkey.pem \
      -n portainer-run
    ```
2. Uncomment the `tls-certs` volume and `volumeMount` in the Deployment, and the `SSL_CERT`, `SSL_KEY`, and `SSL_CERT_DIR` entries in the container's `env` block.

## Health checks

The Deployment's liveness and readiness probes both call `GET /config` over HTTPS on port 443, the same endpoint the Docker Compose healthcheck uses (see [Docker Compose](compose.md)). This is a lightweight way for Kubernetes to confirm the proxy is actually serving traffic, not just that the process is alive.

## DNS resolution

If the pod can't resolve your Portainer hostname, uncomment the `dnsConfig` block near the bottom of the pod spec and point it at a resolver that can (`8.8.8.8` in the example).

## Next step

Once the pod is running and reachable, continue with [Quick Start](../quick-start.md) step 3 onward to connect Portainer and deploy your first app.
