# Security model

Portainer-Command is built so that an agent is never more privileged than the person who connected it, never holds a credential that can change a cluster, and leaves an attributable record of everything it does. This page covers how that works. For the full threat-by-threat analysis, open the **Threat model** page under **Documentation** in Portainer-Command. You can download it as a PDF for your security team.

## Guarantees the architecture makes

These hold because of how Portainer-Command is built, not because of a setting:

* **An agent can't change a cluster.** No tool changes a cluster, and the kubeconfig a session issues is bound to read-only Kubernetes RBAC. A denied write comes back from the cluster itself.
* **The only write path is a commit.** A proposal is a pushed branch that becomes a pull request. Git provides the diff, the author, the revert, and the replay.
* **Every action is attributed to a person.** An agent token is a handle on its owner's own Portainer credential, every Portainer call an agent causes is made as that person, and every pull request is authored with their own Git credential.
* **Read scope only narrows.** A session reads only the namespaces that both Portainer lets the owner read and an administrator has opened to agents.
* **Rollback is Git.** Undoing a proposal or rolling back an environment opens another proposal.

## Identity

Portainer-Command has no user store or permissions model of its own. Users sign in with their Portainer account, and every access decision comes from Portainer.

* **Agent tokens** are created by a signed-in user on the **Connect your agent** page. The Portainer access token behind each one is checked against the signed-in user and refused if it belongs to somebody else. Only a SHA-256 hash of the agent token is stored.
* **Git credentials** are each user's own GitHub personal access token, stored encrypted.
* **Read-only sessions** are temporary Portainer users with the Read-only role on one environment. They're created with the administrator's [sessions credential](../admin/settings.md#portainer-credential-for-sessions), but only after Portainer-Command checks, as the requesting user, that they can see the environment.

## Admins and non-admins

A user with the **administrator** role in Portainer is an administrator in Portainer-Command. Edge administrators aren't. Administrator pages are enforced on the server, not just hidden in the interface.

<table><thead><tr><th width="411">Capability</th><th width="98">Admin</th><th>Non-admin</th></tr></thead><tbody><tr><td>Connect an agent, connect to GitHub, and use an AI Workspace</td><td>Yes</td><td>Yes</td></tr><tr><td>See and revoke their own sessions and connections</td><td>Yes</td><td>Yes</td></tr><tr><td>See proposals</td><td>All</td><td>For environments they can see in Portainer</td></tr><tr><td>Approve, reject, or reopen a proposal</td><td>If they could deploy it themselves</td><td>If they could deploy it themselves</td></tr><tr><td>See the <strong>Administration</strong> section</td><td>Yes</td><td>No</td></tr><tr><td>Open environments to agents, roll back, and halt</td><td>Yes</td><td>No</td></tr><tr><td>See and revoke every user's sessions and connections</td><td>Yes</td><td>No</td></tr><tr><td>Stop any workspace, and read its conversation</td><td>Yes</td><td>No</td></tr><tr><td>Change <strong>Settings</strong></td><td>Yes</td><td>No</td></tr></tbody></table>

Being an administrator doesn't let you approve a change you couldn't deploy. The rules for deciding a proposal are the same for everyone. See [Who can decide a proposal](../user/proposals.md#who-can-decide-a-proposal).

## Credential lifetimes

| Credential                  | Lifetime                                                           | Stored as                        |
| --------------------------- | ------------------------------------------------------------------ | -------------------------------- |
| Agent token                 | 90 days                                                            | SHA-256 hash                     |
| Read-only session           | 60 minutes by default, 8 hours at most. Swept every minute.       | A temporary Portainer user       |
| Git clone credential        | 8 hours by default, 24 hours at most. Scoped to one environment.   | Hash                             |
| AI Workspace                | 90 days                                                            | An agent token of its own        |
| Git credentials, Portainer tokens behind agent tokens, and model provider keys | Until replaced or removed | Encrypted with AES-256-GCM       |

## What Portainer-Command defends against

The threat model considers three adversaries: a prompt-injected agent, a stolen credential, and a legitimate user overreaching. Some of the main threats, and what stops them:

| Threat                                              | What stops it                                                                                                                                                                 |
| --------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| A compromised agent tries to change the cluster     | It has no write credential. The only write path is an approved pull request.                                                                                                  |
| Instructions smuggled into cluster content          | A hijacked agent can still only read and propose. A person reviewing the diff is the control.                                                                                 |
| Exfiltrating Secrets through the agent              | Secret access is off by default for each environment, and Portainer-Command reports where Secrets are readable anyway.                                                        |
| A stolen agent token                                | It acts only as that user, expires, and can be revoked.                                                                                                                       |
| A stolen session kubeconfig                         | It's read-only, reaches one environment, and expires within hours.                                                                                                            |
| Escalating past the caller's own access             | Portainer's visibility check comes first, and namespace scope only narrows.                                                                                                   |
| Escaping the environment's directory                | Agents clone a repository rooted at the environment's directory, pushes are validated, and pushing to the deploy branch is refused.                                          |
| Sneaking a change past approval                     | Approval rights are checked on the server, and your repository's branch protection still applies.                                                                           |
| A compromised workspace container                   | Workspaces run unprivileged with a read-only root filesystem, and [network modes](../admin/settings.md#workspace-network) limit what they can reach.                          |
| Covering tracks                                     | Records survive revocation, and each session's service account can be matched against the cluster's audit log.                                                                |

Some risks are yours to manage:

* **Data sent to the model provider.** Whatever an agent reads goes to its model. The namespace and Secrets settings are therefore also your data-residency controls.
* **Hostile changes that stay inside policy.** Reviewers, branch protection, and admission control are the defense. Keep **Allow cluster-scoped objects** off unless you need it.
* **Misconfigured Portainer RBAC or branch protection.** Portainer-Command inherits both.
* **Leftover connections.** When someone leaves, revoke their connections or [halt the user](../admin/users.md#emergency-halt). Git clone credentials can outlive a revoked session by a few hours.
* **The model provider key.** Use a spend-limited key for AI Workspaces, and rotate it.

## Reporting a vulnerability

Report security vulnerabilities to your Portainer contact rather than in a public issue.
