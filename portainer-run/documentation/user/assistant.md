# Assistant

The Assistant is a persistent chat panel available on every page in Portainer-Run. It's context-aware: it knows which page you're on, which application (if any) is open, and which environment you're working in, and answers questions about your services in plain English.

## Requirements

The Assistant requires either an Anthropic or OpenAI API be configured in [Settings](admin/settings.md). Without one of these set, the Assistant button isn't shown at all.

## What it can do

* **Answer health and performance questions**, automatically fetching diagnostic data (logs, pod conditions, Kubernetes events) before generating a response, rather than asking you to go check those yourself first.
* **Point you to Deploy.** If you ask it to deploy something, it directs you to [Deploy](deploy.md) rather than generating deployment configuration, Docker Compose files or container image suggestions.
* **Detect scale requests** and open the Edit tab on the relevant application, pre-filled with the requested instance count.

## What it won't do

The Assistant never executes irreversible operations directly. It routes destructive actions (delete, scale down, restart) to the existing UI, so a plain-English request never bypasses the confirmation steps a human would otherwise go through. It's also scoped to container operations only and will decline unrelated questions.

## Session handling

Session history is kept in memory only, and is cleared on disconnect. Nothing from an Assistant conversation persists once the session ends.

## Analyse with AI on the Logs tab

The Assistant's diagnostic-gathering also powers the **Analyse with AI** button on an application's Logs tab. See [Applications](applications.md) for how that works when you're looking at a single application rather than chatting generally.
