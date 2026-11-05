# Runtime updates

Runtime updates lets an administrator redeploy applications on the latest image of their runtime, for example after a runtime security release. It's in the **Admin** section of the navigation, and only administrators can use it.

Portainer-Run runs applications on stock public runtime images, so a running pod keeps using the image its node already pulled until the pod is recreated. A redeploy makes the application's pods pull the image again.

## Redeploying apps

The page lists the applications Portainer-Run has deployed across every environment you can reach. Each row shows:

| Column             | What it shows                                                  |
| ------------------ | -------------------------------------------------------------- |
| **Name**           | The application's name.                                        |
| **Environment**    | The Kubernetes environment the application runs in.            |
| **Project space**  | The Kubernetes namespace the application is deployed into.     |
| **Runtime**        | The application's runtime, for example Node.js 22.             |
| **Image**          | The runtime image the application uses.                        |
| **Running digest** | The digest of the image each pod is running, one line per pod. |
| **Pods started**   | When each pod started.                                         |
| **Status**         | The outcome of a redeploy you've started from this page.       |

To redeploy:

1. Use the runtime filter, the search box, or the sort menu to find the applications you want. Select **Refresh** to reload the list.
2. Select the applications. Applications without a manifest in Git can't be redeployed and can't be selected.
3. Select **Redeploy** in the bar that appears.

Each redeploy is a commit to the application's manifest in Git. Portainer then applies the change to the cluster, and the new pods pull the image again.

If an environment can't be reached, a warning names it and the rest of the list still loads.

To redeploy a single application, open it and use **Redeploy** instead. See [Applications](../applications.md#redeploy).
