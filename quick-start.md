# Quick Start

This gets Portainer-Run running as fast as possible using Docker. If you're deploying to Kubernetes or using Docker Compose, see [Installing Portainer-Run](https://app.gitbook.com/s/wg4JrAPgL0W0wygwwbaI/install) for platform-specific detail; the steps below still apply conceptually.

{% stepper %}
{% step %}
### Generate an encryption key

Portainer-Run needs a stable, 32+ character key to encrypt stored Git target credentials. Generate one and keep it somewhere safe. You'll need the exact same value on every future redeploy.

```bash
openssl rand -hex 32
```
{% endstep %}

{% step %}
### Run the container

```bash
docker run -d \
  -p 443:443 \
  -p 80:80 \
  -v portainer-run-data:/app/data \
  -e PORTAINER_URL=https://portainer.example.com:9443 \
  -e ENCRYPTION_KEY=<the-key-you-generated> \
  -e ANTHROPIC_API_KEY=sk-ant-... \
  --name portainer-run \
  portainer/portainer-run:latest
```

* `PORTAINER_URL` and `ENCRYPTION_KEY` are the only two required variables.
* `ANTHROPIC_API_KEY` (or `OPENAI_API_KEY`) is optional. Include it if you want the Assistant panel and AI log triage from the start.
* The named volume `portainer-run-data` mounted at `/app/data` persists Git target credentials and deployment status across restarts. Skip it and you'll need to reconfigure Git targets every time the container restarts.

On first start, the container generates a self-signed TLS certificate valid for 3 years. Your browser will warn about it; accept the exception to proceed, or provide real certificates later (see [Installing Portainer-Run → Docker](install/docker.md)).
{% endstep %}

{% step %}
### Generate a Portainer personal access token

In Portainer, go to **Account → Access Tokens** and create a new token for the account you want Portainer-Run to act as. Portainer-Run inherits this account's RBAC role completely.
{% endstep %}

{% step %}
### Log in

Navigate to `https://<your-host>` and paste in the personal access token. You'll land on the **Applications** page, Portainer-Run's main operational view.
{% endstep %}

{% step %}
### Connect a Git Target

Every deployment path in Portainer-Run commits to Git, so before you can deploy anything you need at least one Git Target configured. Go to the Git Targets area (**Admin → Git Targets** if you're an admin, or your own Git Targets page otherwise) and add a repository, GitHub, GitHub Enterprise Server, GitLab, or Gitea, along with a personal access token that has read/write access to it.
{% endstep %}

{% step %}
### Deploy your first application

From the Applications page, click **+ Deploy**. Drop in the files an AI coding tool produced (a `package.json`-based Node app, a Python app, a static site, and so on) and Portainer-Run detects the runtime, installs dependencies, commits to your Git target, and deploys it. No Dockerfile needed. A sensible default resource request and limit is applied automatically, so the deployment is safe by default without any extra configuration.

Once deployed, the app shows up on the Applications page with a live status indicator. Click into it for logs, metrics, revision history, and the ability to edit or roll back.
{% endstep %}
{% endstepper %}

## What's next

* [Requirements](requirements.md): if you skipped ahead, check you have everything in place.
* [Installing Portainer-Run](https://app.gitbook.com/s/wg4JrAPgL0W0wygwwbaI/install): detailed, platform-specific installation (Kubernetes, Docker, Docker Compose).
* [Using Portainer-Run](https://app.gitbook.com/s/wg4JrAPgL0W0wygwwbaI/user): a full tour of the interface once you're up and running.
