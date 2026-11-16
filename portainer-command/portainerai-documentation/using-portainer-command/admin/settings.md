# Settings

**Settings** holds the configuration for the whole Portainer-Command deployment. It's under **Administration** and only administrators can change it. Non-admin users who open the page see a notice pointing them to [Connect your Git host](../connect-to-github.md) for their own Git credential.

The page has four cards:

1. [Agent gateway transport key](settings.md#agent-gateway-transport-key)
2. [Portainer credential for sessions](settings.md#portainer-credential-for-sessions), called **Service account** on Portainer 2.45.2 or 3.0
3. [Public Portainer address](settings.md#public-portainer-address)
4. [AI Workspaces](settings.md#ai-workspaces)

Each credential card shows a **Configured** or **Not set** badge. Stored credentials are never shown again after saving. You can only replace or rotate them.

{% hint style="info" %}
If a warning reading **Portainer refused this add-on's own credential** appears above the cards, the credential Portainer issued to the add-on no longer matches the one Portainer-Command holds. Portainer issues a new one on every install, upgrade, or repair. Repairing the add-on in Portainer reissues it.
{% endhint %}

## Portainer credential for sessions

An administrator's Portainer API key that Portainer-Command uses for work Portainer keeps to administrators. What the card is called, and what the key is for, depends on your Portainer version:

* **Portainer 2.45.2 or 3.0:** the card is called **Service account**. The key is used for delivering and tracking approved changes, redeploying or removing an Edge stack an approved change touched, reading stacks, workflows and reconcilers, and listing users. Agents' [read-only sessions](../../architecture/overview.md#reading-live-state) don't use it. Until it's set, the card shows **Background tasks cannot run yet**.
* **Older Portainer:** the card is called **Portainer credential for sessions**. The key also creates and deletes the temporary, read-only Portainer users behind agents' read-only sessions. Until it's set, `request_environment_access` fails for everybody. Agents can still read environment files and propose changes.

To set it, do one of the following:

* Paste an administrator's Portainer access token and click **Save**. Portainer-Command checks that the token belongs to an administrator before storing it.
* Click **Create the API key for me**. Portainer-Command creates an access token for your own administrator account, which appears under **My account** in Portainer, where you can revoke it. If Portainer needs your password to issue the token, you're asked for it. The password goes straight from your browser to Portainer and is never stored.

Once configured, the buttons become **Create a new key** and **Replace**.

{% hint style="warning" %}
On an OAuth or LDAP instance running Portainer older than 2.45.2, read-only sessions aren't available whatever key you store. The card says **Agents cannot get cluster access on this OAuth instance** (or LDAP), and **Create the API key for me** is hidden. Upgrade Portainer to 2.45.2 or 3.0 to enable them. See [Known limitations](../../known-limitations.md#oauth-and-ldap-authentication).
{% endhint %}

## Agent gateway transport key

Agent traffic reaches Portainer-Command through Portainer's add-on gateway, which only lets through requests carrying a Portainer credential. The transport key is that credential: one shared key that every agent connection and AI Workspace carries to get past the gateway. It identifies nobody. Each agent is identified by its own [agent token](../connect-your-agent.md).

Because the key ends up in every agent's configuration file, treat it as published. It must belong to a Portainer user with **no environment access**. Portainer-Command refuses an administrator's key.

Until it's set, agents are turned away at the gateway, and only an administrator can add a connection. On internal authentication, an administrator's first **Add connection** creates the key, just as **Create the user and key for me** does.

To set it, do one of the following:

* Click **Create the user and key for me**. Portainer-Command creates a Portainer user with access to Portainer-Command only, and stores its key.
* Paste a key belonging to a standard user with no environment access, and click **Save**.

On an OAuth or LDAP instance, or if Portainer-Command can't tell how Portainer authenticates, it can't create the user: the create button is hidden and the card lists the steps instead. Create a user in your identity provider, sign in to Portainer as it once, create an access token under **My account**, and paste it here. Saving checks that it's a standard user with no environment access, and gives it access to Portainer-Command.

Once the key is set, you can:

* **Check safety**: asks Portainer what the stored key can reach now, and warns you if it can reach an environment. It changes nothing.
* **Rotate credential**: mints a new key and revokes the old one.

{% hint style="danger" %}
Rotating or replacing the transport key **disconnects every agent immediately**. Every agent connection is revoked (including its live sessions), and every running AI Workspace is destroyed. Users then add a new connection or reprovision their workspace with the new key. This can't be undone.
{% endhint %}

## Public Portainer address

The Portainer URL handed to agents and AI Workspaces that run outside this cluster. Every kubeconfig issued after you save it carries this address. It's **required for AI Workspaces**.

The page suggests an address, either Portainer's own Edge agent URL or the address you're viewing the page on. Click the suggestion to use it, or type your own, then click **Save**.

If every agent runs inside the cluster, you can leave this empty.

## AI Workspaces

An [AI Workspace](../workspace/workspace.md) is a private, hosted agent (an OpenCode instance) that Portainer-Command runs as a pod for each user who wants one. Workspaces keep AI agents off people's own machines and away from the rest of your infrastructure.

Before you can enable workspaces, you need a [Public Portainer address](settings.md#public-portainer-address), an [Agent gateway transport key](settings.md#agent-gateway-transport-key), and a saved environment and namespace under [AI Workspace Provisioning](settings.md#ai-workspace-provisioning). Then turn on **Enable AI Workspaces**. The setting saves as soon as you flip it. Portainer-Command checks that it can actually launch a workspace pod, and turns the switch back off if it can't.

When workspaces are enabled, **Workspace** appears at the top of every user's sidebar.

The section has four cards, each saved separately.

### AI Workspace Provisioning

Where workspace pods run, and the credential that creates them.

| Field/Option                 | Overview                                                                                                                                                                                                                                                                       |
| ---------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Environment                  | The environment workspace pods run in. It must be the same cluster Portainer-Command runs on. See [Known limitations](../../known-limitations.md#ai-workspaces-share-portainer-commands-cluster).                                                                              |
| Namespace for workspace pods | The namespace workspace pods run in. Use a namespace that holds only AI Workspaces: Portainer-Command removes any workspace in it that it doesn't recognize, so never point two Portainer-Command installations at the same namespace. Click **None of these? Create a new namespace…** to create one, for example `ai-workspaces`.                                                                                                          |
| Provisioning credential      | The Portainer credential used to create and delete workspace pods. This should be a dedicated Portainer user, never the sessions credential and never a person's own. It only starts and stops pods. The agent inside a workspace still acts as the person who provisioned it. |

Click **Create the user and key for me** to have Portainer-Command create a user (`portainer-command-provisioner`) with access to the chosen namespace only, and store its key. On an OAuth or LDAP instance, create a service account in your identity provider, give it access to the namespace, and paste its token instead.

Click **Test access** to dry-run a workspace create with the stored credential without saving anything. When you **Save**, Portainer-Command grants its provisioning user access to the namespace and checks that it can create workspaces there.

{% hint style="warning" %}
If workspaces already exist, changing the environment or namespace asks you to confirm **Move AI Workspaces?**. Moving destroys every existing workspace, running or stopped, with its storage, and their owners provision new ones in the new location. Transcripts are kept. This can't be undone. See [Workspaces left behind by a move](workspaces.md#workspaces-left-behind-by-a-move).
{% endhint %}

#### Run workspaces under AppArmor

**Run workspaces under AppArmor** is on by default. Workspace pods and the egress proxy then ask for AppArmor's `RuntimeDefault` profile. The switch saves as soon as you flip it, and is applied to the egress proxy straight away. Running workspaces take the change the next time they start or restart.

A node without AppArmor rejects such a pod. Portainer-Command then stops the workspace or proxy rather than run it unconfined, and this card shows **Stopped: the nodes can't enforce AppArmor**. Enable AppArmor on the nodes, or turn this setting off. Only turn it off where the nodes have no AppArmor by design. The profile only takes effect on Kubernetes 1.30 or later.

### AI Workspace Details

What each workspace runs, and when an idle one is stopped.

| Field/Option                           | Overview                                                                                                                                                                                                                                                                    |
| -------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Workspace image                        | By default, workspaces run the image that ships with this version of the add-on, and follow it across upgrades. Turn on **Use a specific image** to pin a registry, repository, and tag instead. A pinned image stays put across upgrades. If you pin by tag only, you're warned **This image is pulled by tag**: add the digest you checked (`repository:tag@sha256:…`) so every workspace runs that image. |
| CPU limit                              | The most CPU one workspace pod may use. Defaults to `2`. Every workspace requests only `100m`, so this is a ceiling rather than a reservation. It can't be lower than `100m`.                                                                                                                              |
| Memory limit                           | The most memory one workspace pod may use. Defaults to `2Gi`. Every workspace requests only `256Mi`, and the limit can't be lower than that. An agent that only reads and proposes is fine with the defaults; one that builds code or runs tests needs more.                                                        |
| Stop an idle workspace after (minutes) | Defaults to `120`. An idle workspace is stopped, not destroyed: its identity, credential, and chat history are kept, and its owner can start it again. Sending a prompt or opening the chat resets the clock. Set `0` to let workspaces run until they expire. |

Existing workspaces keep their image and size until they are recreated. The idle timeout applies to all of them.

### AI Model

The model every workspace talks to. Choose one provider:

| Provider                   | What you need                                                                                                                                                                                                        |
| -------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| None                       | No model. Workspaces still provision, but prompts fail. Choose a provider before inviting users.                                                                                                                     |
| Anthropic (direct)         | An Anthropic API key. The model is optional; leave it blank to use OpenCode's default.                                                                                                                               |
| OpenAI (direct)            | An OpenAI API key and a model ID, such as `gpt-5`. Use this rather than the OpenAI-compatible option for `api.openai.com`.                                                                                           |
| OpenAI-compatible endpoint | A **Base URL** and a model ID, plus an API key only if the endpoint asks for one. This covers LLM gateways, vLLM, or an Ollama you run yourself at its `/v1` URL. Workspace pods must be able to reach the endpoint. |

Once a key or base URL is entered, you can pick a model from the list the provider offers, or click **Not listed? Type a model id instead…**. Click **Test connection** to send one short prompt with these settings without saving them.

A workspace reads the provider when it starts, so stopping and starting a workspace, or restarting it, picks up a change.

{% hint style="warning" %}
Every workspace user can read the API key. Use a key made just for Portainer-Command, with a spend limit at the provider.
{% endhint %}

### Workspace Network

What a workspace may reach on the network. Choose a **Network mode**:

| Mode                     | Overview                                                                                                                                                                                                                             |
| ------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Allow all                | Any IPv4 address except link-local addresses and Alibaba Cloud's metadata address `100.100.100.200`, so cloud instance metadata stays out of reach. A single prompt injection could still send credentials anywhere else. IPv6 addresses outside the cluster aren't reachable. |
| Allow some (the default) | Only the domains in **Allowed domains**, plus Portainer-Command, Portainer, and the model endpoint. Allowed services that accept uploads, such as GitHub and container registries, can still receive data sent with another account. |
| Block all                | Only DNS, Portainer-Command, Portainer, and the model endpoint. The agent can't fetch code, charts, or images from the internet. The model endpoint still receives what the agent reads, and DNS lookups can still carry data out. |

Every mode keeps DNS, Portainer-Command, Portainer, and the model endpoint reachable. No mode reaches link-local addresses such as cloud instance metadata, except DNS on port 53 to a node-local resolver such as `169.254.20.10`. This only holds where your cluster's network plugin enforces NetworkPolicy. The mode is enforced by a NetworkPolicy on each workspace and an egress proxy in the workspace namespace.

With **Allow some**, enter one domain per line in **Allowed domains**:

* A host name, such as `github.com`.
* A wildcard, such as `*.github.io`. This matches names under it, not `github.io` itself.
* Either of these with a port, such as `registry.example.com:5000`. Without a port, ports 443 and 80 are allowed.

The shipped list covers OpenCode and npm, GitHub, the common container registries, and common Helm chart repositories. It follows the add-on across upgrades until you edit it, and **Reset to defaults** restores it. Clients must support an HTTP proxy: `git`, `curl`, `kubectl`, `helm`, `npm`, and OpenCode all do. SSH and other raw connections are blocked.

Network changes apply to running workspaces as well as new ones, within about a minute.
