# Initial configuration

After you [install](installation.md) Portainer-Command, an administrator needs to complete a few settings before anybody can connect an agent. Then each user completes two one-time steps of their own.

## In Portainer

{% stepper %}
{% step %}
### Check your GitOps sources

Portainer-Command proposes changes into the Git repositories your environments deploy from. Make sure each repository is added as a GitOps source in Portainer, and that it's hosted on GitHub.
{% endstep %}

{% step %}
### Check user access

Portainer-Command has no permissions model of its own. An agent sees exactly what its owner sees in Portainer, and only a person with the rights to deploy a change can approve it. Make sure your users and teams have the environment and namespace access in Portainer that you want their agents to inherit. See [Security model](architecture/security.md).
{% endstep %}

{% step %}
### Optional: restrict the default namespace

Portainer grants every user read access to Secrets in an unrestricted default namespace, and Portainer-Command can't take that back. If agents shouldn't be able to read Secrets there, turn on **Restrict access to the default namespace** in each environment's Kubernetes settings. See [Secrets](admin/environments.md#secrets).
{% endstep %}
{% endstepper %}

## In Portainer-Command, as an administrator

Open Portainer-Command from the switcher in the top left of Portainer, then go to **Administration**.

{% stepper %}
{% step %}
### Set the credential for sessions

Go to **Settings** and find **Portainer credential for sessions**. Click **Create the API key for me**, or paste an administrator's access token and click **Save**.

This lets agents request temporary read-only sessions. Without it, agents can still read manifests and propose changes, but can't read live cluster state. See [Settings](admin/settings.md#portainer-credential-for-sessions).
{% endstep %}

{% step %}
### Set the transport key

In **Agent gateway transport key**, click **Create the user and key for me**. Portainer-Command creates a Portainer user with access to Portainer-Command only, and stores its key.

Nobody can connect an agent until this is set. On an OAuth or LDAP instance, create the user in your identity provider and paste its token instead. See [Settings](admin/settings.md#agent-gateway-transport-key).
{% endstep %}

{% step %}
### Optional: enable AI Workspaces

To give users a hosted agent with nothing to install:

1. In **Public Portainer address**, click the suggested address (or type your own) and click **Save**.
2. In **AI Workspaces**, turn on **Enable AI Workspaces**.
3. In **AI Workspace Provisioning**, choose the environment and a dedicated namespace for workspace pods, then click **Create the user and key for me** and **Save**.
4. In **AI Model**, choose a provider, enter its details, click **Test connection**, and **Save**.
5. Review the **Workspace Network** mode. The default, **Allow some**, is a sensible starting point.

See [Settings](admin/settings.md#ai-workspaces) for every option.
{% endstep %}

{% step %}
### Open environments to agents

Every environment is closed to agents until you open it. Go to **Environments**, click an environment, and on the **Agent configuration** tab:

1. Choose what agents may do: read live state, read GitOps state, and propose changes.
2. Under **Which repository**, choose the repository, branch, and directory the environment deploys from.
3. Under **Which namespaces**, restrict agents to specific namespaces if you want to narrow what they can reach.
4. Click **Save configuration**.

We recommend starting with a development environment, with read access only, and adding proposals once you're comfortable. See [Environments](admin/environments.md).
{% endstep %}
{% endstepper %}

## In Portainer-Command, for each user

Every user, administrators included, completes two one-time steps under **Setup** in the sidebar. Until they're done, the **Home** page shows them under **Before you start**.

{% stepper %}
{% step %}
### Connect to GitHub

Store your own GitHub credential, so the pull requests your agent opens are authored by you. Without it, your agent can read but can't propose anything, and you can't approve proposals. See [Connect to GitHub](user/connect-to-github.md).
{% endstep %}

{% step %}
### Connect your agent, or provision a workspace

Create a connection for the agent you already use, such as Claude Code or Cursor. See [Connect your agent](user/connect-your-agent.md).

If AI Workspaces are enabled, you can instead go to **Workspace** and click **Provision my workspace**. See [AI Workspace](user/workspace.md).
{% endstep %}
{% endstepper %}

You're ready to go. Try a task from the [Prompt library](user/prompt-library.md), or ask your agent to list the environments it can reach.
