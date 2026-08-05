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

Access control to Portainer-Run is done via [Teams](https://docs.portainer.io/admin/user/teams) in Portainer. If you don't already have teams set up, you can add them under **Teams** in Portainer.
{% endstep %}

{% step %}
### Give teams access to Portainer-Run

Once you have users and teams set up, you can define which teams have access to Portainer-Run. This lets you limit who has access to use Portainer-Run as well as whether they are restricted to Portainer-Run only or allowed access to both Portainer and Portainer-Run.

In Portainer, under **Add-ons**, click on the **Portainer-Run** addon. You'll be taken to the configuration page for the add-on.

Select the **Team access** tab. Here you can select the teams that will have access to Portainer-Run. Administrator users will always have access to Portainer-Run regardless of the teams selected here.

If you want to prevent the selected teams from accessing Portainer itself, and therefore limit them to Portainer-Run access only, turn the **Deny access to Portainer for selected teams** toggle on.

If you've made any changes here, remember to click **Save** to keep your changes.
{% endstep %}

{% step %}
### Give users/teams access to environments

Portainer-Run inherits the RBAC settings from Portainer. By default, non-administrators do not have access to any environments so you will need to [specify which users and teams can have access to which environments](https://docs.portainer.io/admin/environments/environments#manage-access) in order for them to show up in Portainer-Run.

In Portainer, under **Environment-related** you'll see a list of environments added to Portainer. Click the **Manage access** link next to the environment you want to provide your users or teams access to within Portainer-Run.

In the **Create access** section, choose your users and/or teams from the dropdown, and choose the **Role** you want these users and tams to have on the environment. Click the **Create access** button to apply your selections.

{% hint style="info" %}
For more on environment access management, refer to the [Portainer documentation](https://docs.portainer.io/admin/environments/environments#manage-access).
{% endhint %}
{% endstep %}

{% step %}
### Create namespaces

Deployments in Portainer-Run are created in [namespaces](https://docs.portainer.io/user/kubernetes/namespaces) on your Kubernetes cluster. When creating the deployment, the user will choose the namespace to deploy to. We recommend creating namespaces for your Portainer-Run users and teams to deploy to.&#x20;

To create a namespace, in Portainer enter the environment that you gave your users or teams access to in the previous step. Then click on **Namespaces** in the left menu.

From the list of namespaces, click **Add with form** to [add a new namespace](https://docs.portainer.io/user/kubernetes/namespaces/add).

Give your namespace a name and optionally set quotas on the namespace.

{% hint style="info" %}
Portainer-Run deployments have [CPU and memory requests and limits](user/applications.md#default-resource-allocation) set by default. Consider these when setting up quotas on your namespaces.
{% endhint %}

When you're ready, click **Create namespace**.
{% endstep %}

{% step %}
### Give users/teams access to namespaces

Once you have created a namespace, you need to [give your users and teams access to that namespace](https://docs.portainer.io/user/kubernetes/namespaces/access), just as you did for the environment itself in step 4. This way, you can give specific users and teams access to specific namespaces within the environment as needed (for example, for separate business units).

In Portainer, in the **Namespaces** list for your environment, click the **Manage access** link next to the namespace you want to provide your users or teams access to within Portainer-Run.

In the **Create access** section, choose your users and/or teams from the dropdown. Click the **Create access** button to apply your selections.
{% endstep %}

{% step %}
### Add an ingress controller

Portainer-Run uses ingresses to publish the applications your users upload. If you don't already have an ingress controller installed, you will need to do so in order for your users to be able to access the applications they deploy.

Installing an ingress controller is outside the scope of this documentation. We would recommend a secure ingress controller such as [Pomerium](https://github.com/pomerium/ingress-controller).
{% endstep %}

{% step %}
### Add ingresses to your namespaces

Once you have the ingress controller installed, you should [add ingresses to your namespaces](https://docs.portainer.io/user/kubernetes/networking/ingresses/add). This lets you configure the ingresses on a per-namespace level rather than an overarching configuration for all namespaces.

You can add an ingress to a namespace in Portainer by selecting your environment then expanding **Networking** in the left hand menu and selecting **Ingresses**. Click the **Add with form** button to add a new ingress.

From the **Namespace** dropdown select the namespace you crated in step 5. A **Name** will automatically be generated for your ingress but you can change this if you prefer something different. From the **Ingress class** dropdown, choose the ingress you installed in the previous step.

The exact annotations and rules you need to apply for your ingress will differ depending on the ingress controller, and are outside the scope of this documentation. Refer to the docs for your ingress controller for details. We generally recommend setting a hostname that makes sense for your setup and has the correct DNS records set to talk to your Kubernetes cluster here, however.

When you're ready, click **Create**.
{% endstep %}
{% endstepper %}

## In Portainer-Run

The following steps are performed in Portainer-Run as an admin user. You can access Portainer-Run from within Portainer using the switcher in the top left.

{% stepper %}
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





