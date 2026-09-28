# Overview

This section covers how Portainer-Run is built, and specifically how it relates to Portainer itself, the boundary that makes the whole governance model work.

## The high-level flow

```
Builder (browser or MCP client)
  → Portainer-Run (self-service layer)
    → Git repository (sanctioned, scanned, source of truth)
      → Portainer (GitOps reconciliation, control plane)
        → Kubernetes (your clusters, any environment)
```

Portainer-Run never talks to Kubernetes directly, and never holds cluster credentials of its own. Every deployment produced by [Deploy](../user/deploy.md) is committed to a Git repository first. Portainer, which is already configured to poll and reconcile that repository via GitOps, applies the change onto the cluster from there. Running state always tracks the repository. There's no out-of-band drift, because there's no path to the cluster that skips Git.

This has two direct consequences worth internalizing:

1. **Builders never receive cluster credentials.** They authenticate to Portainer-Run with a Portainer personal access token, and everything they can see or do is bound by that token's Portainer RBAC role. See [Roles and RBAC](roles.md) for the full detail.
2. **Git is the actual source of truth**, not Portainer-Run's own database. If Portainer-Run went away entirely, every application it deployed would keep running as a standard Kubernetes GitOps stack, because nothing proprietary sits between the repository and the cluster.

## Component architecture

```
Browser (React/Vite frontend)
  → Node.js proxy (server.js)
    → Portainer API        (Kubernetes operations, proxied to bypass browser CORS)
    → Anthropic API        (if ANTHROPIC_API_KEY configured)
    → OpenAI API           (if OPENAI_API_KEY configured)
    → GitHub / GitLab / Gitea  (Git target operations)
```

Portainer-Run is a React/Vite frontend served by a Node.js proxy. That proxy does more than serve static assets. It's the component responsible for:

* **Forwarding Kubernetes API calls to Portainer**, bypassing the CORS restrictions a browser would otherwise hit calling Portainer directly.
* **Relaying AI requests** to whichever provider is configured, keeping the API key server-side so it never reaches the browser.
* **Serving the aggregated `/env-status/:envId` endpoint** (see [Performance](overview.md#performance-aggregated-status-polling) below).
* **Exposing the `/mcp` endpoint** described in [MCP](../user/mcp.md).
* **Maintaining a file-backed session cache** and the SQLite database backing [Git Targets](../user/admin/git-targets.md).

User credentials never appear in server logs, and AI API keys never reach the browser. Both stay entirely server-side.

## Performance: aggregated status polling

At scale, with dozens of clusters and hundreds of workloads, firing individual pod, service, ingress, and node API calls per deployment would saturate both the browser and the Portainer proxy. Portainer-Run avoids this with an aggregated approach:

* The server exposes `/env-status/:envId`, which fans out to Kubernetes in parallel for a single environment (one call each for pods, services, ingresses, and nodes), then aggregates the results into a per-deployment status map (status reason and access URL), cached for 20 seconds keyed by a hash of the token and environment ID.
* The browser fires one request per environment rather than one per deployment, with a client-side concurrency limit of 5 simultaneous environment fetches.
* A resourceVersion fingerprint per environment means that if nothing has changed since the last render, the cached result is applied immediately with no network call at all.

## Session cache and data persistence

The server maintains two things under `/app/data`:

* **`portainer-run.db`**: a SQLite database storing encrypted [Git target](../user/admin/git-targets.md) credentials.
* **`cache.json`**: a file-backed cache of deployment status. On reconnect, the last known state is shown immediately while live data loads in the background, so the UI never shows a blank slate after a refresh.

Both are keyed appropriately and persist only if `/app/data` is mounted as durable storage. `ENCRYPTION_KEY` must stay identical across every restart and redeploy. It's what makes the Git target credentials in that database readable, and losing or changing it makes them permanently undecryptable.

## Next: Roles and RBAC

The piece that ties all of this together, what a given user can actually see and do inside Portainer-Run, is covered in [Roles and RBAC](roles.md).
