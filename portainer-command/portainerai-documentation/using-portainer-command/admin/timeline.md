# Timeline

**Timeline** shows everything that has happened on the instance, newest first: sessions issued and ended, connections created and revoked, workspaces provisioned and destroyed, proposals opened and decided, and environment configuration changes. It also records workspaces that are running but never connected back, and the redeploys Portainer-Command makes with its administrator credential when a proposal is approved, shown as the approver's action. It's the first page under **Administration**, because it answers the question you usually arrive with: what happened?

Click any event for its full record.

<figure><img src="../../.gitbook/assets/command-timeline-1.png" alt="Timeline activity feed"><figcaption></figcaption></figure>

## Filter the timeline

| Filter          | Overview                                                                                                              |
| --------------- | --------------------------------------------------------------------------------------------------------------------- |
| Search activity | Free-text search across events.                                                                                       |
| Date range      | Limits events to a range of days, in your local time zone. Defaults to **Any time**.                                  |
| User            | Shows events for one user.                                                                                            |
| Kind            | Shows one kind of activity: **Sessions**, **Connections**, **Workspaces**, **Proposals**, or **Environment changes**. |

Click **Show older** at the bottom of the feed to load more events. If events can't be loaded, you'll see **Could not load the activity** or **Could not load older events**, with a retry, rather than an empty feed.

## Export the timeline

Click the download icon (**Download as CSV**) to export every event that matches the current filters. This is useful for incident reviews or for handing activity records to a security team.
