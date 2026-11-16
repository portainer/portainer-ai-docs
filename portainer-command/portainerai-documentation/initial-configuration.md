# Initial configuration

After you [install](installation.md) Portainer-Command, an administrator needs to complete a few settings before anybody can connect an agent. Then each user completes two one-time steps of their own.

## In Portainer

{% stepper %}
{% step %}
### Check your GitOps sources

Portainer-Command proposes changes into the Git repositories your environments already deploy from: your Portainer stacks' and edge stacks' repositories, and, if you allow it, the ones Argo CD and Flux deploy from. To give agents a home for new manifests on an environment, add that repository as a GitOps source in Portainer. It can be on GitHub, GitLab, Azure DevOps, Bitbucket, or another Git server. View the [Portainer Sources documentation](https://docs.portainer.io/user/app-delivery/sources) for details.
{% endstep %}

{% step %}
### Check user access

Portainer-Command has no permissions model of its own. An agent sees exactly what its owner sees in Portainer, and only a person with the rights to deploy a change can approve it. Make sure your users and teams have the environment and namespace access in Portainer that you want their agents to inherit. See [Security model](architecture/security.md).
{% endstep %}

{% step %}
### Optional: restrict the default namespace

On Portainer older than 2.45.2, Portainer lets every user read an unrestricted default namespace, including Secrets, and a read-only session inherits that even if you haven't opened `default` to agents. If agents shouldn't be able to read it, turn on **Restrict access to the default namespace** in each environment's Kubernetes settings. See [Secrets](using-portainer-command/admin/environments.md#secrets).
{% endstep %}
{% endstepper %}

## In Portainer-Command, as an administrator

Open Portainer-Command from the switcher in the top left of Portainer, then go to **Administration**.

{% stepper %}
{% step %}
### Set the credential for sessions

Go to **Settings** and find the administrator key card. It's called **Service account** on Portainer 2.45.2 or 3.0, and **Portainer credential for sessions** on older Portainer. Click **Create the API key for me**, or paste an administrator's [Portainer access token](https://docs.portainer.io/user/account-settings#access-tokens) and click **Save**.

On Portainer 2.45.2 or 3.0, Portainer-Command uses it for background work, such as delivering approved changes and redeploying an Edge stack an approved change touched. On older Portainer it also lets agents request temporary read-only sessions: without it, agents can still read manifests and propose changes, but can't read live cluster state. See [Settings](using-portainer-command/admin/settings.md#portainer-credential-for-sessions).
{% endstep %}

{% step %}
### Set the transport key

In **Agent gateway transport key**, click **Create the user and key for me**. Portainer-Command creates a Portainer user with access to Portainer-Command only, and stores its key.

Until this is set, only an administrator can add a connection: on internal authentication, an administrator's first **Add connection** creates the key. On an OAuth or LDAP instance, **Settings** lists the steps to create the user in your identity provider and paste its token instead. See [Settings](using-portainer-command/admin/settings.md#agent-gateway-transport-key).
{% endstep %}

{% step %}
### Optional: enable AI Workspaces

To give users a hosted agent with nothing to install:

1. In **Public Portainer address**, click the suggested address (or type your own) and click **Save**.
2. In **AI Workspace Provisioning**, choose the environment and a dedicated namespace for workspace pods, then click **Create the user and key for me** and **Save**. Leave **Run workspaces under AppArmor** on unless your nodes have no AppArmor.
3. In **AI Workspaces**, turn on **Enable AI Workspaces**.
4. In **AI Model**, choose a provider, enter its details, click **Test connection**, and **Save**.
5. Review the **Workspace Network** mode. The default, **Allow some**, is a sensible starting point.

See [Settings](using-portainer-command/admin/settings.md#ai-workspaces) for every option.
{% endstep %}

{% step %}
### Open environments to agents

Every environment is closed to agents until you open it. Go to **Environments**, click an environment, and on the **Agent configuration** tab:

1. Choose what agents may do: read live state, read GitOps state, and propose changes.
2. Under **Which repository**, choose the repository, branch, and directory the environment deploys from, or **None** to let agents work only in the repositories behind what's already deployed. Turn on **Agents may propose into the repositories Argo CD and Flux deploy from** if you use them.
3. Under **Which namespaces**, restrict agents to specific namespaces if you want to narrow what they can reach.
4. Click **Save configuration**.

We recommend starting with a development environment, with read access only, and adding proposals once you're comfortable. See [Environments](using-portainer-command/admin/environments.md).
{% endstep %}
{% endstepper %}

## In Portainer-Command, for each user

Every user, administrators included, completes two one-time steps under **Setup** in the sidebar. Until they're done, the **Home** page shows them under **Before you start**.

{% stepper %}
{% step %}
### Connect your Git host

Store your own credential for each Git host your environments use, so the changes your agent proposes are authored by you. Without it, your agent can read but can't propose anything, and you can't approve proposals. See [Connect your Git host](using-portainer-command/connect-to-github.md).
{% endstep %}

{% step %}
### Connect your agent, or provision a workspace

Create a connection for the agent you already use, such as Claude Code or Cursor. See [Connect your agent](using-portainer-command/connect-your-agent.md).

If AI Workspaces are enabled, you can instead go to **Workspace** and click **Provision my workspace**. See [AI Workspace](using-portainer-command/workspace/workspace.md).
{% endstep %}
{% endstepper %}

You're ready to go. Try a task from the [Prompt library](using-portainer-command/workspace/prompt-library.md), or ask your agent to list the environments it can reach.
