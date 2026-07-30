# Assistant

The Assistant is a persistent chat panel available on every page in Portainer-Run. It's context-aware: it knows which page you're on, which application (if any) is open, and which environment you're working in, and answers questions about your services in plain English.

## Requirements

The Assistant requires either `ANTHROPIC_API_KEY` or `OPENAI_API_KEY` to be configured on the server. Without one of these set, the Assistant button isn't shown at all. See [Requirements](../requirements.md) for how to configure these.

## What it can do

* **Answer health and performance questions**, automatically fetching diagnostic data (logs, pod conditions, Kubernetes events) before generating a response, rather than asking you to go check those yourself first.
* **Translate a Docker Compose file into a Portainer-Run deployment.** Paste a compose file directly into the Assistant input and it translates it into Portainer-Run's deployment model, with every service becoming a container in a single pod sharing localhost, shows a preview, and populates the deploy form. Build directives and network aliases are flagged as unmappable, since they don't have a direct equivalent in that model.
* **Pre-populate the deploy form** from a natural-language description of what you want to deploy.
* **Detect scale requests** and open the Edit tab on the relevant application, pre-filled with the requested instance count.

## What it won't do

The Assistant never executes irreversible operations directly. It routes destructive actions (delete, scale down, restart) to the existing UI, so a plain-English request never bypasses the confirmation steps a human would otherwise go through. It's also scoped to container operations only and will decline unrelated questions.

## Session handling

Session history is kept in memory only, and is cleared on disconnect. Nothing from an Assistant conversation persists once the session ends.

## AI Analyse on the Logs tab

The Assistant's diagnostic-gathering also powers the **AI Analyse** button on an application's Logs tab. See [Applications](applications.md) for how that works when you're looking at a single application rather than chatting generally.
