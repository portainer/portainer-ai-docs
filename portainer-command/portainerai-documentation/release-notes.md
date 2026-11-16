# Release Notes

## Release 1.1.0

November 17, 2026

### Before you upgrade <a href="#id-189aa92a-210c-4d06-adc6-8b2fa07b1572" id="id-189aa92a-210c-4d06-adc6-8b2fa07b1572"></a>

* Back up the database before upgrading. The upgrade applies migrations 16–23; migration 23 pins each environment's configured source URL.
* Rolling back to 1.0.x is not supported: 1.1 refuses a database a newer release used. A failed `helm upgrade --atomic` rolls back to the previous image only.
* With Helm 4, `helm upgrade --atomic` on a release Portainer installed needs `--force-conflicts`.
* Change from 1.0: a repository a non-administrator brings into play on a private or `http://` host is still refused (an agent-named source with no configured source, a re-pointed source, or a stack on an unpinned repository). Ask an administrator to configure the source.

### New in this release <a href="#id-6bc6045f-2ddd-46ca-8e9c-f286ea9edd2c" id="id-6bc6045f-2ddd-46ca-8e9c-f286ea9edd2c"></a>

#### New features

* Proposals can target existing Portainer, Argo CD, Flux, and other Git deployment sources across supported Git hosts, with adoption and safer undo behavior.&#x20;
* AppArmor protection for AI Workspaces is enabled by default, with an administrator setting and clear handling when nodes cannot enforce it.
* Administrators can create the first gateway transport key while adding a connection.
* Workspace connectivity failures now surface as “Can’t reach Command” with troubleshooting guidance.

#### Improvements <a href="#id-0b58fda0-1822-4db3-a10f-2a65e55f4ae9" id="id-0b58fda0-1822-4db3-a10f-2a65e55f4ae9"></a>

* Read-only environment access uses scoped kubeconfigs and works with OAuth or LDAP-authenticated Portainer instances.
* AI Workspace sandboxing now uses tighter network, identity, image, AppArmor, and persistent-storage protections.
* Chat now shows provider errors, recovers after connection loss, and provides clearer subagent progress.
* Workspace cleanup removes runtime objects left without a database record.
* Environment settings provide inline validation, clearer copy, and removable namespace chips.
* Local development authentication now follows Portainer session-cookie behavior.
* The interface now displays accurate build versions and commits.
* Go and React dependencies were upgraded, with stronger coverage and verification safeguards.

#### Security updates <a href="#id-432cd38b-80c4-4e64-b702-bcb89a04dcf3" id="id-432cd38b-80c4-4e64-b702-bcb89a04dcf3"></a>

* Approvals check every environment a merge lands on, including where reconciler build files deploy, refuse merges that would redeploy stacks the approver can't see, and pin each merge to the checked branch.
* With no namespace allowlist, system namespaces (`kube-system`, `kube-public`, `kube-node-lease`, `portainer` and `portainer-addon-*`) stay closed to agents for every user.
* Repository URLs with `.` or `..` path segments, including encoded forms, are refused, and repositories are compared by server.
* Git credentials are sent only to the host and scheme they were saved for; a token saved for `https://` is never sent over `http://`.
* The git endpoint caps pkt-lines, tree walks and Git host answers, and sizes memory from the pod limit, so a crafted push or fetch can't exhaust Command.
* Allow-all workspace egress excludes cloud metadata addresses, including Alibaba Cloud's `100.100.100.200`.
* Kubernetes names that would leave their proxy path are refused, and visibility is checked before an administrator-key read reaches a caller.
* Revoking a grant and halting no longer depend on the administrator API key.
* Built with Go 1.27.4 (GO-2026-6859); the workspace image takes git 2.56.1, `x/net` and musl with CVE fixes.

#### Bug Fixes <a href="#id-4fa19ea3-6559-499c-a8b2-e6354e9cb79d" id="id-4fa19ea3-6559-499c-a8b2-e6354e9cb79d"></a>

* Proposals through a stack target work on Portainer 2.45.x instead of failing with "workflows API 400".
* Private and `http://` Git hosts work again for configured environment sources, pinned stacks, Argo CD and Flux repositories, and administrators.
* Old workspaces are destroyed when their placement moves, and AppArmor follows each proxy's live profile.
* The MCP server reports the release version.
* Failed and unschedulable workspace pods now show accurate states instead of remaining stuck at “Starting.”.
* Timeline, workspace, and chat failures now show actionable errors instead of blank, stale, or endless-loading states.
* Proposal history no longer labels undeployed changes as “Deployed now,” and stale staged proposals are rejected.
* Connection counts, singular labels, revocation messages, navigation titles, unknown routes, and Help & Support behavior are corrected.
* Grant documentation now accurately describes default-namespace access and cluster-scoped read-only permissions.
* Sensitive Git URL credentials are redacted from logs and errors.
* Workspace image vulnerabilities in kubectl, Helm, curl, and undici dependencies were addressed, and compromised certificate authorities are no longer trusted.
* Claude Desktop is no longer offered an incompatible remote MCP configuration.&#x20;
