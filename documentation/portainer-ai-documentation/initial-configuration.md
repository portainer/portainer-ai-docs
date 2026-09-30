# Initial configuration

If you have completed the [Quick start](quick-start.md) steps, Portainer-Run is ready to use. However, we recommend a few steps to get yourself set up and working efficiently, especially if you are in a multi-user environment.

## In Portainer

The following steps are completed within Portainer itself as an admin user.

{% stepper %}
{% step %}
### Set up users

If you are in a multi-user environment, you likely already have users set up. If not, and you want to provide multiple users with access to Portainer-Run, first set them up as Portainer users. Portainer-Run shares an [authentication system](https://docs.portainer.io/admin/settings/authentication) with Portainer, so a user must exist in Portainer to log in to Portainer-Run.

When using internal authentication, create users under **Users**. When using an external authentication provider, users are usually pre-provisioned in your upstream authentication system (Active Directory, LDAP, or an OAuth provider). Provision them there.
{% endstep %}

{% step %}
### Add users to Teams

Access control to Portainer-Run is done through [Teams](https://docs.portainer.io/admin/user/teams) in Portainer. The installer script configures a `portainer-run` team. It provides members access to Portainer-Run only, not Portainer Business itself. Add your Portainer-Run users to this team. Alternatively, create other teams and give them access to Portainer-Run.
{% endstep %}

{% step %}
### Give users/teams access to environments

Portainer-Run inherits the RBAC settings from Portainer. The `portainer-run` team has access to your environment by default. If you add teams or users to new teams, [give those teams access to the environment](https://docs.portainer.io/admin/environments/environments#manage-access).
{% endstep %}

{% step %}
### Create namespaces

Deployments in Portainer-Run are created in [namespaces](https://docs.portainer.io/user/kubernetes/namespaces) on your Kubernetes cluster. When creating the deployment, the user chooses the namespace to deploy to. The installer script creates an `applications` namespace and gives the `portainer-run` team access to it. You can also create additional namespaces in Portainer and [configure access to them](https://docs.portainer.io/user/kubernetes/namespaces/access) for your users and teams.
{% endstep %}
{% endstepper %}

## In Portainer-Run

The following steps are performed in Portainer-Run as an admin user. You can access Portainer-Run from within Portainer using the switcher in the top left.

{% stepper %}
{% step %}
### Set Portainer-Run configuration options

Portainer-Run works out of the box, but you may want to adjust a few settings. As an administrator, go to **Admin** and click **Settings**.

Here you can adjust the [Portainer-Run configuration](user/admin/settings.md). We recommend setting an Anthropic or OpenAI API key to use the [Assistant](user/assistant.md).
{% endstep %}

{% step %}
### Create a Git target

Portainer-Run uses Git as the "source of truth" when deploying applications. When an application is created in Portainer-Run, the code the user provides is uploaded to a Git repository, and Portainer then deploys that code from the Git repository into the environment and namespace selected. Configuration data (ie the manifest) is stored in Git alongside the code itself. This provides the ability for users to roll back to previous versions of their code if needed.

These Git repositories are provided via [Git Targets](user/admin/git-targets.md) in Portainer-Run.

To add a Git target, in Portainer-Run, access **Git Targets** as an administrator.

The page lists configured Git targets. A fresh installation has none. Click **Add Git Target** to add a new Git target.

Fill out the Git target details in the form. Portainer-Run has pre-configured providers for GitHub, GitLab, and Gitea. You can also use any Git provider by choosing `other.`

{% hint style="info" %}
You can find more information on setting up a Git Target under [Git Targets](user/admin/git-targets.md).
{% endhint %}

You can create your Git target as a **Shared target**, making it accessible to other users and teams with access to Portainer-Run. We recommend this to provide teams with a centralized, controlled location to store their application code.

After you fill out the form, click **Test connection** to verify the details. The page shows any issues connecting to your Git repository.

Click **Save** to create your Git target.
{% endstep %}
{% endstepper %}
