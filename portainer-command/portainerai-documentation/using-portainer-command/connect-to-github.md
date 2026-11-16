# Connect your Git host

When your agent proposes a change, Portainer-Command commits it with your own credential for that repository's Git host, so the commit and the change request are authored by you rather than by a bot. Your credential is also used to build the copy of the manifests your agent clones, so the agent reads exactly what you can read.

You need a credential for a repository's Git host to:

* Let your agent read and propose changes there.
* Approve proposals, because approving merges the change request as you.
* Roll back an environment, if you're an administrator.

You can store one credential per Git server. Each is stored encrypted and never shown again.

## Supported Git hosts

| Git host              | Credential                                                                                                                                                     | Change requests are called                         |
| --------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------- |
| GitHub                | A **Personal access token** with write access to contents and pull requests. Works with github.com and GitHub Enterprise Server.                              | Pull requests                                      |
| GitLab                | A **Personal access token** with the `api` scope (`read_api` and `write_repository` aren't enough). Works with gitlab.com and self-managed GitLab.             | Merge requests                                     |
| Azure DevOps          | A **Personal access token** with **Code: Read & write**. Works with dev.azure.com and Azure DevOps Server 2020 or later.                                       | Pull requests                                      |
| Bitbucket Cloud       | Your **Atlassian account email** and an **API token** with the `read:repository`, `write:repository`, `read:pullrequest`, and `write:pullrequest` scopes.     | Pull requests                                      |
| Bitbucket Data Center | A personal **HTTP access token** with repository write permission. A project or repository token can't merge.                                                 | Pull requests                                      |
| Git                   | A username, and a **Password or access token** the server accepts for a push. For any other Git server.                                                       | Change requests, reviewed and merged in Portainer-Command |

Use a personal token. A group, project, or repository token commits as a bot, not as you.

## Add your credential

1. Under **Setup** in the sidebar, click **Connect your Git host**.
2. Choose your **Git host**.
3. Enter your **Git username**. It's optional for most hosts, and required for **Git**.
4. For **Bitbucket Cloud**, enter your **Atlassian account email**. For **Git**, enter the **Commit email** to use as the author of the commits and merges Portainer-Command makes as you.
5. Leave **Host URL** empty for github.com, gitlab.com, or dev.azure.com. Fill it in for GitHub Enterprise Server, self-managed GitLab, Azure DevOps Server, Bitbucket Data Center, or a Git server. Bitbucket Cloud has no Host URL.
6. Enter your token. The field's label and the permissions it needs are shown under it.
7. Click **Save credential**.

Portainer-Command only sends a credential to the server it was saved for, and never to a loopback, link-local, or cloud metadata address.

{% hint style="info" %}
If you use a fine-grained GitHub personal access token, it needs the **Pull requests: write** permission. Portainer-Command can't check this for you.
{% endhint %}

## Test access

Once your credential is saved, click **Test access**. Portainer-Command checks every repository configured on the environments you can reach, and reports on each one. For GitHub, for example:

* **Reachable, and the token can push**: you're ready to propose.
* **Reachable, but the token cannot push**: proposals would fail at commit. Give the token write access.
* **The token was rejected**: check that the token hasn't expired or been revoked.
* **Repository not found, or the token cannot see it**: give the token access to the repository. GitHub gives the same answer for a private repository you can't see and one that doesn't exist.

Each Git host words its results slightly differently. If a repository is on a host you have no credential for, the result tells you which host to add a credential for.

If none of your environments has a repository configured yet, there's nothing to test against. An administrator sets this on the [environment page](admin/environments.md#which-repository).

## Replace or remove your credential

Each stored credential is listed with its host. To change a token, choose the same **Git host** and **Host URL**, enter the new token, and click **Replace credential**. To remove one, click **Remove**, then **Remove credential**. Also revoke the token on the Git host. Your agent can't propose on that host until you add a credential again.
