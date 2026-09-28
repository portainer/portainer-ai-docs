# Proposals

A proposal is a change an agent wants to make to an environment. It's a GitHub pull request authored by the person whose agent made it, and nothing reaches the cluster until a person approves it. The **Proposals** page is where proposals are reviewed and decided.

## How a proposal is made

1. The agent clones the environment's manifests through Portainer-Command, edits files, and pushes a feature branch. The push is held inside Portainer-Command, and nothing reaches GitHub yet.
2. The agent opens a proposal. Portainer-Command re-checks the environment's policy (proposals allowed, namespace allowlist, file types, cluster-scoped objects) and refuses the proposal with a reason if it breaks one.
3. Portainer-Command turns the branch into one commit on the real repository, made with the proposer's own [GitHub credential](connect-to-github.md), and opens a pull request.
4. The proposal appears on the **Proposals** page with its full diff and the agent's summary of what it changed and why.

Each manifest file maps to one Portainer stack. Adding a file creates a workload, changing a file updates it, and removing a file deletes what it deployed.

## Find a proposal

The list shows proposals for the environments you can see in Portainer.

* Use the **All**, **Open**, **Approved**, and **Rejected** tabs to filter by state.
* Use the filter menu to show open proposals that are **Yours to decide** or **Not yours to decide**. A lock icon marks proposals you can't decide.
* Use **Search proposals** to search by title, environment, or proposer.

| Status          | Meaning                                                                                   |
| --------------- | ----------------------------------------------------------------------------------------- |
| Waiting         | Open, and waiting for a decision.                                                         |
| Approved        | Merged and delivered.                                                                     |
| Rejected        | Closed without merging.                                                                   |
| Needs attention | Approved, but something after the merge didn't finish. The proposal says what went wrong. |

## Review a proposal

Click a proposal to see its details:

* **Rationale**: the agent's explanation of what it changed and why.
* **Changes**: each file changed, with a link to its diff on GitHub.

The pull request lists, per file, the kinds of object it creates and the namespaces they land in. It calls out anything a reviewer shouldn't skim past, such as cluster-scoped objects, RBAC, admission webhooks, CRDs, Secrets, and custom resources Portainer-Command doesn't recognize. Where a kind or namespace is chosen by a Helm template, it's listed as **not verifiable** rather than guessed at.

Warnings also appear on the proposal when approving it would do more than merge, for example **Approving removes running workloads** or **Approving installs a Helm release**.

{% hint style="warning" %}
Trust the diff, not the summary. The rationale is written by the agent. Read what the files actually change, and prefer images pinned by digest.
{% endhint %}

## Approve or reject a proposal

To approve, click **Approve and merge**. Portainer-Command:

1. Merges the pull request.
2. Applies the change to the cluster, creating or updating the Portainer stack with **your** credential.
3. Confirms the cluster is running it.

To reject, click **Reject**, optionally enter a reason, and click **Reject proposal**. The pull request is closed without merging, and your reason is posted as a comment on it, so the proposer can see why. A rejected proposal can be put back in the queue with **Reopen**.

### Who can decide a proposal

You approve with your own rights, so you can only decide a proposal you could have deployed yourself. You need:

* Access to the environment in Portainer, with the rights to deploy to it.
* Write access to every namespace the change lands in.
* Environment administrator rights, if the change creates a namespace.
* A [GitHub credential](connect-to-github.md) stored in Portainer-Command.

If you can't decide a proposal, the page tells you why. You can use **Escalate to…** to ask someone else to decide it. Escalating grants them nothing new.

{% hint style="info" %}
Agents have no way to approve proposals. Approval only happens in the Portainer-Command interface. There is currently no rule stopping you from approving a proposal your own agent opened, so use GitHub branch protection if you need a second reviewer.
{% endhint %}

## Undo a proposal

To undo an approved proposal, click **Undo**, then **Open revert proposal**. Portainer-Command opens a new proposal that restores every file the original changed to what it held before. Nothing deploys until that proposal is approved too.

If a later change touched the same files, you're warned that **A later change is in the way**. Click **Undo anyway** to continue.

To roll a whole environment back to an earlier state, administrators can use [State history](../admin/environments.md#state-history).

## Helm charts

Agents can adopt an upstream Helm chart by vendoring it into the environment's directory. When a proposal adds a new chart, the agent has to declare the namespace the release is installed into.

{% hint style="warning" %}
Removing a Helm chart doesn't uninstall it. Portainer deletes the chart's stack but leaves the release running, including its CRDs and webhooks. The pull request and the approval both say so, and give you the `helm uninstall` command to finish the job.
{% endhint %}
