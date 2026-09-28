# Administration

**Administration** is where Portainer administrators look after the whole Portainer-Command instance: what agents may do, what they've done, and how the deployment is configured. It's one item in the sidebar that opens into the following pages:

* [**Timeline**](timeline.md): everything that has happened on the instance, newest first.
* [**Environments**](environments.md): open environments to agents, choose their repository and namespaces, roll back, and halt.
* [**Sessions**](sessions.md): every temporary read-only credential issued, for every user.
* [**Connections**](connections.md): every agent connection, across all users.
* [**Workspaces**](workspaces.md): every AI Workspace, across all users.
* [**Tokens**](tokens.md): model token usage and spend across all AI Workspaces.
* [**Users**](users.md): what Portainer-Command attributes to each Portainer user, and the per-user emergency halt.
* [**Settings**](settings.md): the deployment's credentials, public address, and AI Workspace configuration.

Only users with the **administrator** role in Portainer see **Administration**. Portainer's Edge administrator role doesn't count. The pages are also enforced on the server, so a non-admin who opens one directly is refused. See [Security model](../architecture/security.md#admins-and-non-admins).

## Stopping agents in an emergency

Portainer-Command gives you two ways to stop agents immediately. Neither deletes any records, and both are reversible:

* **Halt an environment**: closes the environment to every agent and ends every live session on it. Use this when something is wrong in one place. See [Emergency halt](environments.md#emergency-halt).
* **Halt a user**: revokes all of one person's connections and sessions, and stops their workspace, everywhere. Use this when a person's credentials or agent may be compromised. See [Emergency halt](users.md#emergency-halt).

If you need to go further, you can act in Portainer directly: shut down the Portainer-Command add-on, or delete the temporary `command-agent-…` users that back live sessions.
