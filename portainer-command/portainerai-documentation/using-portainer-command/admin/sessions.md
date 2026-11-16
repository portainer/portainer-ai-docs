# Sessions

**Sessions** lists every temporary read-only credential Portainer-Command has issued, for every user. Each one is scoped to a single environment and ends on its own when it expires. On Portainer 2.45.2 or 3.0 it's a scoped kubeconfig Portainer issues as the requesting user; on older Portainer it's a temporary Portainer account. Users see their own sessions under [My sessions](../my-sessions.md).

## View sessions

The summary cards show the number of **Active sessions** and how many have ever been issued. Click a card to filter the table.

The table shows each session's **Environment**, **Owner**, the **Reason** the agent gave, its **State**, and when it was **Issued** and **Expires / ended**. Use **Search sessions…**, the state filter, and the sort menu to narrow the list.

| State      | Meaning                                                                            |
| ---------- | ---------------------------------------------------------------------------------- |
| Active     | The credential is live.                                                            |
| Revoke pending | A revoke was asked for, but Portainer didn't complete it, so the credential still works. Portainer-Command retries every minute. |
| Expired    | The credential reached its expiry and was removed.                                 |
| Revoked    | The credential was ended early, by its agent, its owner, or an administrator.      |
| Unrecorded | Portainer holds the credential, but Portainer-Command has no record of issuing it. |

{% hint style="info" %}
**Unrecorded** credentials usually mean Portainer-Command's database was lost or restored. Portainer still holds them, so they still expire on schedule and can still be revoked. Losing the database loses the audit trail, not control of the credentials.

On Portainer 2.45.2 or 3.0, sessions are scoped kubeconfigs, which Portainer can't list. If the database is lost, they don't appear here and can't be revoked early, but Portainer still ends each one at its expiry, at most eight hours out.
{% endhint %}

## Session details

Click a session to see its full record:

* **Identity**: the access ID, the Kubernetes **Service account** the session acts as, and, on Portainer older than 2.45.2, the ID of the temporary Portainer user behind it. The service account is what appears as `user.username` in Kubernetes audit events, so use it to match a session against your cluster's audit log.
* **Attribution**: the owner, environment, connection, and reason.
* **Lifetime**: when it was issued, when it expires or expired, and how it ended.

## Revoke sessions

Click **Revoke** on a session's page, or select several sessions in the table and click **Revoke** in the bar that appears. Revoking takes effect immediately and has no confirmation. It uses your own administrator credential, so it works even if the key in **Settings** is missing or refused. The credential stops working and its role bindings are removed from the cluster. If Portainer doesn't complete it, the session shows **Revoke pending** and is retried every minute.

To end every session on one environment at once, use [Emergency halt](environments.md#emergency-halt).
