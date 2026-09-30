# Quick start

Portainer-Run can be installed on a fresh server along with Portainer Business using the terminal installer. The following command downloads the install script and runs it on your server:

{% hint style="warning" %}
Ensure you meet the [requirements](requirements.md) before running the script.
{% endhint %}

```
curl -sfL https://get.portainer.run/get.sh | sudo sh -
```

Follow the steps in the installer to complete the setup. The installer asks you to make several decisions:

{% hint style="info" %}
Portainer-Run can also be installed as an add-on within an existing Portainer Business installation on Kubernetes. You can learn more about this approach [in the Portainer documentation](https://docs.portainer.io/admin/add-ons).
{% endhint %}

{% stepper %}
{% step %}
### Domain

The installer asks for the domain you want to use for applications deployed in Portainer-Run. Deployed applications receive a subdomain of this domain. For example, if your application is called `myapp` and your domain is `apps.mycompany.com`, the application's full URL is `myapp.apps.mycompany.com`.

The installer checks whether the domain and its wildcard subdomain are resolvable. If they aren't resolvable, configure them and run the check again. You can also proceed anyway.
{% endstep %}

{% step %}
### Certificate

Choose the SSL certificate to use for your applications. The options are:

* **Self-signed**: Portainer-Run generates a self-signed certificate for your applications. By default, browsers display a warning when visiting sites with self-signed certificates.
* **Let's Encrypt via DNS-01**: Portainer-Run uses Let's Encrypt to generate trusted certificates automatically for your applications. Your DNS records must be with Cloudflare for this option to work, and you must have a Cloudflare API key.
* **Custom certificate**: Portainer-Run uses a custom wildcard certificate you provide for your applications. You need to supply your own certificate and key.
{% endstep %}

{% step %}
### Ingress controller

An ingress controller is required to route requests to your applications. Here you can choose which to install.

* **Traefik**: The default option, and works out of the box.
* **Pomerium (coming soon)**: The Pomerium ingress controller provides secure-by-default ingress, and requires an OIDC identity provider in order to handle access control. This option is in development and will be available in a future release.
{% endstep %}

{% step %}
### License key

Portainer-Run installs Portainer Business, which requires a license key. If you have a key you can enter it now, otherwise you'll be asked to provide it when accessing Portainer for the first time after the install completes.
{% endstep %}

{% step %}
### Review

The installer shows your choices for review. If you need to make any changes, choose **Back to edit** to return to the start.

If you're ready to proceed, choose **Confirm and install**.
{% endstep %}

{% step %}
### Installing

The installation process begins. Progress appears for each installation step. Expand or collapse items in the list to see each step's details.

When the installation completes, it provides the Portainer-Run URL, an admin username, and a password. Copy the password by pressing `p` to reveal it. The installer doesn't display it again.
{% endstep %}

{% step %}
### Access Portainer-Run

After the install completes, access Portainer-Run at the provided URL. On initial access, enter your Portainer Business license key if you didn't supply it during installation. Then choose whether to enable Edge Compute.

After completing this setup, Portainer Business opens. In the Portainer header, click the switcher icon ![Portainer product switcher icon](../../.gitbook/assets/switcher-icon.png). The product list includes Portainer-Run.

<figure><img src="../../.gitbook/assets/addon-switcher.png" alt="Portainer product switcher showing the Portainer-Run option"><figcaption></figcaption></figure>

Click **Portainer-Run** to open the Portainer-Run interface.
{% endstep %}

{% step %}
### Complete the initial setup

When you first access Portainer-Run as an administrator, set an encryption key. This key encrypts the credentials Portainer-Run saves. Click **Generate** to generate a key, or enter one manually.

<figure><img src="../../.gitbook/assets/portainer-run-setup-encryption-key.png" alt="Portainer-Run encryption key setup"><figcaption></figcaption></figure>

Once you have a key entered, click **Create key and finish setup**.
{% endstep %}

{% step %}
### Installation complete

Portainer-Run is installed.

<figure><img src="../../.gitbook/assets/portainer-run-fresh-install-ui.png" alt="Portainer-Run after initial setup"><figcaption></figcaption></figure>
{% endstep %}
{% endstepper %}

## What's next

* [Requirements](requirements.md): if you skipped ahead, check you have everything in place.
* [Initial configuration](initial-configuration.md): to get Portainer and Portainer-Run configured for optimal usage.
* [Using Portainer-Run](user/): a full tour of the interface once you're up and running.
