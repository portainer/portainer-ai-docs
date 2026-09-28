# Initial configuration

If you have completed the [Quick Start](quick-start.md) steps, Portainer-Run is ready to use. However we do recommend a few steps to get yourself set up and working efficiently, especially if you are in a multi-user environment.

## In Portainer

The following steps are completed within Portainer itself as an admin user.

{% stepper %}
{% step %}
### Set up users

If you are in a multi-user environment it's likely you already have users set up. If not, and you're wanting to provide multiple users with access to Portainer-Run, you will first need to set them up as Portainer users. Portainer-Run shares an [authentication system](https://docs.portainer.io/admin/settings/authentication) with Portainer, so a user must exist in Portainer in order for users to log into Portainer-Run.

When using internal authentication, users can be set up under **Users**. When using an external authentication provider, users will usually be pre-provisioned at your upstream auth system (Active Directory, LDAP, or an OAuth provider) and should be provisioned there.
{% endstep %}

{% step %}
### Add users to Teams

Access control to Portainer-Run is done via [Teams](https://docs.portainer.io/admin/user/teams) in Portainer. A `portainer-run` team is set up by the installer script and is configured to provide users within the team with access to Portainer-Run only (ie, not Portainer Business itself), so you can add your Portainer-Run users to this team. Alternatively you can create other teams and give them access to Portainer-Run as well.
{% endstep %}

{% step %}
### Give users/teams access to environments

Portainer-Run inherits the RBAC settings from Portainer. The `portainer-run` team will be configured with access to your environment out of the box. If you add any additional teams (or users to new teams) you will need to [give those teams access to the environment](https://docs.portainer.io/admin/environments/environments#manage-access) as well.
{% endstep %}

{% step %}
### Create namespaces

Deployments in Portainer-Run are created in [namespaces](https://docs.portainer.io/user/kubernetes/namespaces) on your Kubernetes cluster. When creating the deployment, the user will choose the namespace to deploy to. The installer script creates an `applications` namespace for users to deploy into and gives the `portainer-run` team access to this namespace. You can also create additional namespaces in Portainer and [configure access to them](https://docs.portainer.io/user/kubernetes/namespaces/access) for your users and teams.
{% endstep %}
{% endstepper %}

## In Portainer-Run

The following steps are performed in Portainer-Run as an admin user. You can access Portainer-Run from within Portainer using the switcher in the top left.

{% stepper %}
{% step %}
### Set Portainer-Run configuration options

Portainer-Run should work out of the box for you now, but you may want to adjust a few settings. As an administrator, go to **Admin** and click **Settings**.

Here you can adjust the [Portainer-Run configuration](user/admin/settings.md). We'd recommend setting an Anthropic or OpenAI API key here in order to use the [Assistant](user/assistant.md) functionality.
{% endstep %}

{% step %}
### Create a Git Target

Portainer-Run uses Git as the "source of truth" when deploying applications. When an application is created in Portainer-Run, the code the user provides is uploaded to a Git repository, and Portainer then deploys that code from the Git repository into the environment and namespace selected. Configuration data (ie the manifest) is stored in Git alongside the code itself. This provides the ability for users to roll back to previous versions of their code if needed.

These Git repositories are provided via [Git Targets](user/admin/git-targets.md) in Portainer-Run.

To add a GIt Target, access Portainer-Run as an administrator and click **Git Targets** in the left menu.

Here you will see a list of GIt Targets that have been configured. In a fresh install there will be none listed. Click the **Add Git Target** button in the top right to add a new GIt Target.

Fill out the details for your Git Target in the form. Portainer-Run has pre-configured providers for GitHub, GitLab and Gitea, but you can use any Git provider as well by choosing `other.`&#x20;

{% hint style="info" %}
You can find more information on setting up a Git Target under [Git Targets](user/admin/git-targets.md).
{% endhint %}

You can choose to create your Git Target as a **Shared target**, making it accessible to other users and teams with access to Portainer-Run. We recommend doing this to provide teams with a centralized, controlled location to store their application code.

Once you have filled out the form, click **Test connection** to ensure your details are all correct. If there are any issues connecting to your Git repo they will be shown.

When you're ready, ciick **Save** to create your Git Target.
{% endstep %}
{% endstepper %}





