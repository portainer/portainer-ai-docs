# Requirements

The Portainer-Run installation script installs most of what is needed, but it does expect you to have a few things in place.

## A server

You will need a server to install on. We've tested extensively on Ubuntu Server 26.04 LTS but most Linux distributions should suffice. You will need:

* Root / `sudo` access on the server. This level of access is needed to install KubeSolo, Portainer-Run and the dependencies.
* Internet access from the server. This is to access the install script as well as the upstream binaries.
* `iptables` and `curl` installed. These are required for KubeSolo and for downloading the installation script.

In terms of specs, this will depend on the amount of applications you intend to run. KubeSolo (the Kubernetes distribution that Portainer-Run installs) is designed to consume as few resources as possible, as is Portainer-Run itself, so the majority of your resource will go to your applications.

Every deployed application receives a sane default resource request and limit automatically: **0.1 CPU / 1 GiB memory requested, 1 CPU / 4 GiB memory as the limit.**

## A Portainer Business license key

Portainer-Run relies on Portainer Business as the engine that powers it. Portainer Business is installed by the Portainer-Run installation script, and requires a license key. You can enter this during the installation or on initial login.

## Optional: An Anthropic or OpenAI API key

Portainer-Run's Assistant feature lets you talk to either Anthropic or OpenAI to help deploy applications and diagnose issues with your deployments. Once Portainer-Run is installed, you can set your API key under Settings. If no API key for either Anthropic or OpenAI is provided, then the Assistant feature will be unavailable.

## Next step

Once these are in place, continue to [Quick Start](quick-start.md) to get Portainer-Run running.
