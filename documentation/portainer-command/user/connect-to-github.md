# Connect to GitHub

When your agent proposes a change, Portainer-Command commits it with your own GitHub credential, so the commit and the pull request are authored by you rather than by a bot. Your credential is also used to build the copy of an environment's manifests your agent clones, so the agent reads exactly what you can read.

You need a GitHub credential to:

* Let your agent propose changes.
* Approve proposals, because approving merges the pull request as you.
* Roll back an environment, if you're an administrator.

The credential is stored encrypted and never shown again.

## Add your credential

1. Under **Setup** in the sidebar, click **Connect to GitHub**.
2. Enter your **Git username**.
3. Leave **Host URL** empty for github.com. Fill it in only for GitHub Enterprise Server.
4. Enter a **Personal access token** with write access to the repositories behind your environments.
5. Click **Save credential**.

{% hint style="info" %}
If you use a fine-grained personal access token, it needs the **Pull requests: write** permission. Portainer-Command can't check this for you.
{% endhint %}

## Test your access

Once your credential is saved, click **Test access**. Portainer-Command checks every repository configured on the environments you can reach, and reports on each one:

* **Reachable, and the token can push**: you're ready to propose.
* **Reachable, but the token cannot push**: proposals would fail at commit. Give the token write access.
* **The token was rejected**: check that the token hasn't expired or been revoked.
* **Repository not found, or the token cannot see it**: give the token access to the repository. GitHub gives the same answer for a private repository you can't see and one that doesn't exist.

If none of your environments has a repository configured yet, there's nothing to test against. An administrator sets this on the [environment page](../admin/environments.md#which-repository).

## Replace or remove your credential

To change your token, enter a new one and click **Replace credential**. To remove it, click **Remove**. There's no confirmation, and your agent can't propose changes until you add a credential again.
