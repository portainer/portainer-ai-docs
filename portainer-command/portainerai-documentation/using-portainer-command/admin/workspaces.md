# Workspaces

**Workspaces** lists every [AI Workspace](../workspace/workspace.md) on the instance, across all users. Each workspace runs as the person who provisioned it and acts with their own Portainer credential.

## View workspaces

The summary cards show the number of **Running workspaces**, how many are **Not connected**, and the **Idle stop** time. Click **Idle stop** to change it in [Settings](settings.md#ai-workspace-details).

The table shows each workspace's **Owner**, **State**, **Where** it runs (namespace and environment), its **Usage** (tokens and cost), when it was **Last used**, and when it **Expires**. Use the search box, the state filter, and the sort menu to narrow the list.

| State             | Meaning                                                                                                  |
| ----------------- | -------------------------------------------------------------------------------------------------------- |
| Starting          | The pod is being created, or OpenCode is starting inside it.                                             |
| Connected         | The workspace is running and connected to Portainer-Command.                                             |
| Stopped           | The pod is stopped, usually after the idle timeout. Its owner can start it again.                        |
| Crashing          | The pod keeps failing. Check the pod in Portainer.                                                       |
| Image pull failed | The workspace image couldn't be pulled. Check the image in [Settings](settings.md#ai-workspace-details). |
| Can't reach Command | The pod has been running for at least three minutes but has never connected back to Portainer-Command. Check that the cluster can reach the [Public Portainer address](settings.md#public-portainer-address), then restart the workspace. |
| Failed            | The pod failed, or its container can't be created. Restarting the workspace tries again. If it fails again, check the pod's events and the node it ran on. |
| Can't be scheduled | No node can run the workspace pod. Free capacity or change where workspaces run. It starts on its own once a node fits. |
| Stopped: AppArmor | Portainer-Command stopped the workspace because its node can't enforce AppArmor. Enable AppArmor on the nodes, or turn off **Run workspaces under AppArmor** in [Settings](settings.md#ai-workspace-provisioning). Its owner can then start it again. |
| Expired           | The workspace reached the end of its 90-day lifetime.                                                    |
| Destroyed         | The workspace was destroyed. Its record and transcript remain.                                           |

<figure><img src="../../.gitbook/assets/Workspace-1.png" alt="Workspaces list"><figcaption></figcaption></figure>

Click a workspace to open it.

## Stop a workspace

Click **Stop** on a workspace, then **Stop workspace**. The pod stops and no longer consumes resources. The workspace keeps its identity, credential, and chat history, and its owner can start it again from their **Workspace** page.

Starting a workspace is left to its owner. To destroy one, open it and click **Destroy**, then **Destroy workspace**. To cut a person's workspace off completely, revoke its connection on [Connections](connections.md), or use [Emergency halt](users.md#emergency-halt) on the user.

## Workspaces left behind by a move

When you change the environment or namespace AI Workspaces run in, Portainer-Command destroys the existing workspaces and keeps retrying any it can't reach. Until an old workspace is gone, its owner can't provision a new one. If the old environment is gone for good, open the workspace and click **Forget this workspace**, then **Forget workspace**. It's recorded as destroyed, but its pod, storage, and Secret may still exist in the old cluster. The Secret holds the model provider key, so remove those objects yourself.
