# Users

**Users** lists every Portainer user on the instance, with what Portainer-Command attributes to each: connections, sessions, workspaces, and proposals. Portainer-Command doesn't manage users itself. They come from Portainer.

Use the search box and the role filter (**Administrators** or **Standard users**) to narrow the list, and click a user to see their full record.

## User details

A user's page has four tabs:

* **Connections**: their agent connections. Select connections and click **Revoke** to revoke them.
* **Workspaces**: their AI Workspaces, including destroyed ones.
* **Sessions**: every read-only session their agents have held. Select sessions and click **Revoke** to end them.
* **Proposals**: every proposal their agents have opened, and who decided each one.

## Emergency halt

**Emergency halt** on a user's page stops one person's agents everywhere, in one step. Use it when a person's machine, credentials, or agent may be compromised, or when someone leaves the organization.

Click **Emergency halt**, then **Halt&#x20;**_**user**_**'s agents**. This immediately:

* Revokes all of their live connections. Their agents can no longer authenticate and can't be reconnected.
* Ends all of their live sessions. The credentials stop working inside the clusters.
* Stops their AI Workspace. Its conversation and history are kept, but its connection is revoked with the rest, so they'd have to destroy it and provision a new one.

Nothing is deleted from the record. The user can create new connections afterwards, unless you also restrict or remove them in Portainer.

If anything couldn't be finished, you'll see **Halted, with loose ends**, listing what's left. Portainer-Command retries sessions it couldn't revoke. Any Portainer access tokens it couldn't delete can be removed by their owner under **My account** in Portainer.

To close an environment to every agent instead, use [Emergency halt](environments.md#emergency-halt) on the environment.
