# Release Notes

## Release 1.5.0

November 6, 2026

### Known Issues <a href="#id-1e9b55d1-128e-49da-85f3-e7a9ae7c0948" id="id-1e9b55d1-128e-49da-85f3-e7a9ae7c0948"></a>

* On Portainer 2.44, stacks created through the API don't check Git for changes on their own. An Edit-tab change saved by someone other than the app's owner waits until the owner saves a change.
* Reinstalling Run removes its saved Git targets.
* Run accepts some app names that Kubernetes later rejects, such as names starting with a hyphen or longer than 63 characters.

### Before you upgrade <a href="#id-6c1f4ffb-ce0a-4793-9eaf-c47ce7882581" id="id-6c1f4ffb-ce0a-4793-9eaf-c47ce7882581"></a>

**Self-signed Git servers need Skip TLS verification ticked.**\
Apps now clone their source with the Git target's own TLS setting. Before, certificate checks were always skipped. If a Git target points at a server with a self-signed or private-CA certificate, open it under [Git targets](user/admin/git-targets.md) and tick **Skip TLS verification** before you deploy or update apps with it.

**Operator roles can no longer deploy or edit apps.**\
Run now checks, with your own [Portainer access](architecture/roles.md), that you can work in the target project space before every deploy, edit, delete and settings read. Users with the Operator or Namespace Operator role can now only restart and scale apps; they can't deploy or edit them. Give each user who deploys apps the Standard user role on their own project space.

**MCP deploys need a Gateway URL.**\
`deploy_app` sends source files through the file gateway, so set **Gateway URL** in Run's [settings](user/admin/settings.md) before agents deploy. Without it, `deploy_app` returns a clear error asking for it.

**Runtime images are pulled on every pod start.**\
New and redeployed apps pull their runtime image (`node:22`, `php:8.4-apache` and so on) each time a pod starts, so runtime security updates reach them. On an air-gapped cluster, make sure the runtime images are available from a registry mirror the cluster can reach, or new pods fail with `ImagePullBackOff`.

**Environment variable values move out of Git.**\
Every value now lives in a Kubernetes Secret named `<app>-app-secrets`, and the committed manifest holds none. An app deployed by an earlier version moves its values the first time you save its settings. Values committed by earlier versions stay in your repository's history, so rotate any secrets that were stored that way.

**A changed encryption key keeps unreadable Git targets aside.**\
When you continue with a new encryption key, Git targets that the new key can't decrypt are copied into a `connections_orphaned` table before they're removed. To get them back, [restore the key that encrypted them and copy the rows back](user/admin/git-targets.md#recovering-targets-after-a-key-change).

### New in this release

#### New features <a href="#fcef0c09-97db-402f-8e2f-bd9a8c7d080b" id="fcef0c09-97db-402f-8e2f-bd9a8c7d080b"></a>

* **App sizes.** Deploy an app at Small, Medium (twice Small) or Large (four times Small), and change its size later on the Edit tab. Sizes above Small need a short reason, which is recorded with the app.
* **Redeploy and Runtime updates.** A Redeploy action restarts an app on the latest release of its runtime image. Administrators get a Runtime updates page listing every app by runtime, from which they can redeploy apps across environments.
* **Connect your agent.** A new page sets up MCP clients such as Claude Desktop and Claude Code for your account. It creates a Portainer access token for the agent, shows it once, and lists and revokes the ones you've made. The token has your full Portainer rights and doesn't expire, so revoke it when it's no longer needed.
* **Claude Desktop extension.** Connect your agent offers a one-click `.mcpb` extension for Claude Desktop, so you don't need Node.js installed to connect it.
* **Business-friendly MCP.** MCP no longer asks for a cluster, namespace or hostname. It fills them in from what you can access, says what it picked, asks in plain language when there's a real choice, and reports results in plain language. A new `get_deploy_options` tool shows the choices.
* **Detail page header.** The app detail page has a sticky header that stays in view as you scroll.

#### Security updates <a href="#id-88a30779-7d6d-4df7-bebf-fa209ae0cf5e" id="id-88a30779-7d6d-4df7-bebf-fa209ae0cf5e"></a>

* Run checks that you can work in an app's project space before every deploy, edit, delete and settings read, and refuses paths outside the app's folder. One user can no longer overwrite another user's app in a shared Git target.
* Environment variable values, secret or not, are kept in a Kubernetes Secret and never committed to Git.
* Apps deployed before 1.4.0 get the web server rule that hides `.env` and other dotfiles the first time their settings are saved, and when they are redeployed with Run's Redeploy.
* An Edit-tab change applies at once only when the person saving it is the one the app's stack runs as. Anyone else's change, an administrator's included, is saved in Git and applies at the stack's next Git check, so a stack never starts running with another person's rights.
* Every request to Run is checked against your Portainer session, request bodies have a size limit, and slow Git hosts and Portainer calls time out instead of hanging the server.
* The source clone quotes every value, keeps the Git target's scheme, and skips certificate checks only when the target asks for it.

#### Bug Fixes <a href="#eb20de61-8412-40ee-8762-b16e2a667830" id="eb20de61-8412-40ee-8762-b16e2a667830"></a>

* Edit-tab changes to an app's address or size are applied to the running app, not only saved in Git. When Portainer can't apply a change, the tab says it is saved and when it applies.
* Secret environment variables are no longer dropped when you edit an app.
* Saving on the Edit tab can no longer overwrite an app's environment variables or port, or remove its Ingress. An invalid hostname or port is refused with a clear message.&#x20;
* Updating a single-file HTML app now serves the new page instead of the old one.&#x20;
* A failed request no longer crashes or hangs the Run server, and errors are shown in plain language.
* The source clone no longer breaks on branch names with special characters.
* MCP's `deploy_app` reports deploy errors clearly.
