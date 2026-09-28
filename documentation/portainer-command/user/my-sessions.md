# My sessions

A session is a temporary, read-only credential one of your agents has checked out to read an environment's live state. Each session is a Portainer account scoped to a single environment, acting as you, which deletes itself when it expires. **My sessions** lists every session your agents have held.

## How sessions work

When your agent calls `request_environment_access`, it states a reason and gets a kubeconfig that:

* Points at Portainer's Kubernetes proxy, so the agent never holds a cluster credential.
* Only reaches the namespaces that both you can read in Portainer and an administrator has opened to agents.
* Refuses every write. The refusal comes from the cluster itself.
* Expires on its own, after 60 minutes by default and at most 8 hours.

Your agent can also hand a session back early with `revoke_environment_access`.

## View your sessions

The table shows each session's **Environment**, the **Connection** it was issued to, the **Reason** your agent gave, its **State**, and when it was **Issued** and **Expires / ended**. Use **Search sessions…** and the state filter (**Active**, **Expired**, **Revoked**, or **Unrecorded**) to narrow the list.

Click a session to see its full record, including the Kubernetes service account it acts as. That service account is what appears as the user in your cluster's audit log.

## Revoke sessions

To end a session before it expires, click **Revoke** on the session's page. To revoke several at once, select them in the table and click **Revoke** in the bar that appears.

Revoking takes effect immediately, and there's no confirmation. If your agent still needs access, it simply asks for a new session.
