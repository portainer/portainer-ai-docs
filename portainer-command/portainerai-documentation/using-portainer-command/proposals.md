# Proposals

A proposal is a change an agent wants to make to an environment. It's a change request on your Git host (a pull request on GitHub, Azure DevOps, and Bitbucket, a merge request on GitLab), authored by the person whose agent made it, and nothing reaches the cluster until a person approves it. A plain Git server has no change requests of its own, so Portainer-Command keeps the change request itself, marked **Reviewed in Command**, and its comments appear under **Comments** on the proposal. The **Proposals** page is where proposals are reviewed and decided.

## How a proposal is made

1. The agent clones the environment's manifests through Portainer-Command, edits files, and pushes a feature branch. The push is held inside Portainer-Command, and nothing reaches the Git host yet.
2. The agent opens a proposal. Portainer-Command re-checks the environment's policy (proposals allowed, namespace allowlist, file types, cluster-scoped objects) and refuses the proposal with a reason if it breaks one.
3. Portainer-Command turns the branch into one commit on the real repository, made with the proposer's own [Git host credential](connect-to-github.md), and opens a change request.
4. The proposal appears on the **Proposals** page with its full diff and the agent's summary of what it changed and why.

In the environment's own repository, each manifest file maps to one Portainer stack. Adding a file creates a workload, changing a file updates it, and removing a file deletes what it deployed.

### Proposing into what's already deployed

An agent can also propose into the repository behind something already running. It names a live object, Portainer-Command works out what deploys it, and the agent gets a clone of only the files that deploy it, at their real paths, on the branch they deploy from:

| What deploys it                                       | What the agent can change                                                                          | What approving does                                                        |
| ----------------------------------------------------- | -------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------- |
| A Portainer Git stack or edge stack                   | Edit its existing files, or delete all of them to remove the app.                                  | Merges, then redeploys the stack on every environment the change reaches. |
| A Portainer GitOps workflow deployment                | Edit its existing files only.                                                                      | Merges. Portainer applies it on the deployment's interval.                 |
| An Argo CD Application or Flux Kustomization or HelmRelease | Add or edit files, and delete one only where the reconciler prunes. An administrator has to [allow this](admin/environments.md#which-repository) per environment. | Merges. Argo CD or Flux applies it on its own schedule.                   |

A Portainer stack that isn't in Git, or objects nothing deploys (for example, applied with `kubectl apply`), are first **adopted**: Portainer-Command opens an adoption proposal that writes them to Git exactly as they run, so approving it changes nothing in the cluster. The agent then proposes its change on top. Existing Secrets are never adopted. Anything managed elsewhere, such as a plain Helm release or an operator, is refused, naming its owner.

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
* **Changes**: each file changed, with a link to its diff on the Git host. For a change request kept in Portainer-Command, the files are listed in place.

The change request's description lists, per file, the kinds of object it creates and the namespaces they land in. It calls out anything a reviewer shouldn't skim past, such as cluster-scoped objects, RBAC, admission webhooks, CRDs, Secrets, and custom resources Portainer-Command doesn't recognize. Where a kind or namespace is chosen by a Helm template, it's listed as **not verifiable** rather than guessed at.

Warnings also appear on the proposal when approving it would do more than merge, for example **Approving removes running workloads** or **Approving installs a Helm release**.

A proposal into something already deployed also shows:

* **Environments that will be updated via** Portainer, Argo CD, or Flux, listing every environment the change reaches. A shared file can update production from a staging proposal, so read it before approving. When it's more than one, you're warned **Approving updates N environments**.
* **Reconciled by**: Portainer, Argo CD, or Flux.
* **App managed by**: Portainer-Run or Portainer-IDP, when one of them keeps the app's definition in these files and rewrites them when the app is edited there.
* For an adoption, **Adopts workloads that are already running**, or **Approving deletes workloads that existed before Command** when a change deletes an adopted file.

{% hint style="warning" %}
Trust the diff, not the summary. The rationale is written by the agent. Read what the files actually change, and prefer images pinned by digest.
{% endhint %}

## Approve or reject a proposal

To approve, click **Approve and merge**. Portainer-Command:

1. Merges the change request.
2. Delivers it. Portainer stacks are created, updated, or deleted with **your** credential. Edge stacks are redeployed by Portainer-Command with its administrator credential, on your behalf. Argo CD, Flux, and Portainer GitOps workflow deployments apply the merge themselves, and the approval shows **Waiting for** them **to apply**. Portainer-Command never asks them to sync.
3. Confirms the cluster is running it.

If adopting a stack stops part way, the proposal shows **Adoption did not finish**, and anyone who may approve sees **Retry adoption of** _stack_, which carries on from the step it reached.

To reject, click **Reject**, optionally enter a reason, and click **Reject proposal**. The change request is closed without merging, and your reason is posted as a comment on it (or, on a plain Git server, kept under **Comments**), so the proposer can see why. A rejected proposal can be put back in the queue with **Reopen**, except on Bitbucket Cloud, which can't reopen a closed pull request.

### Who can decide a proposal

You approve with your own rights, so you can only decide a proposal you could have deployed yourself. You need:

* Access to the environment in Portainer, with the rights to deploy to it.
* The same rights on every environment the merge updates, not just the one it was proposed for.
* Write access to every namespace the change lands in.
* Environment administrator rights, if the change creates a namespace.
* A [credential for the repository's Git host](connect-to-github.md) stored in Portainer-Command.

Approval is also refused if merging would redeploy stacks in Portainer that you can't see.

If you can't decide a proposal, the page tells you why. You can use **Escalate to…** to ask someone else to decide it. Escalating grants them nothing new.

{% hint style="info" %}
Agents have no way to approve proposals. Approval only happens in the Portainer-Command interface. There is currently no rule stopping you from approving a proposal your own agent opened, so use branch protection on your Git host if you need a second reviewer.
{% endhint %}

## Undo a proposal

To undo an approved proposal, click **Undo**, then **Open revert proposal**. Portainer-Command opens a new proposal that restores every file the original changed to what it held before. Nothing deploys until that proposal is approved too.

If a later change touched the same files, you're warned that **A later change is in the way**. Click **Undo anyway** to continue.

An adoption can't be undone, because undoing it would delete what it adopted: it shows **Adopted, so it cannot be undone**. Undoing a later change goes back to the adopted state.

To roll a whole environment back to an earlier state, administrators can use [State history](admin/environments.md#state-history). Rollback covers the environment's own repository only.

## Helm charts

Agents can adopt an upstream Helm chart by vendoring it into the environment's directory. When a proposal adds a new chart, the agent has to declare the namespace the release is installed into.

{% hint style="warning" %}
What removing a Helm chart does depends on your Portainer version. Portainer 2.45 and later uninstall the release along with the chart's stack, and the proposal warns **Approving uninstalls the Helm release**. Older versions delete the stack but leave the release running, including its CRDs and webhooks: the proposal warns **Approving does not uninstall the Helm release**, and the change request gives you the `helm uninstall` command to finish the job.
{% endhint %}
