# Tokens

**Tokens** shows model token usage and spend across every AI Workspace on the instance, as reported by the model provider. Use it to see which workspaces cost the most, and to spot a runaway pattern before the invoice arrives.

The page is read-only.

## Choose a date range

Use the date picker to choose the period. The default is **Last 30 days**. Days are counted in UTC.

## What the page shows

* **Summary cards**: totals across the whole instance for the period.
* **Tokens by day**: a chart for each kind of token, with one line per workspace. The six busiest workspaces each get their own line, and the rest are combined into **Other**.
* **By workspace**: a table of each workspace and its owner, with the number of replies, **Input**, **Output**, **Cache read**, **Cache write**, and **Reasoning** tokens, and **Spend**. Destroyed workspaces are included and marked **(destroyed)**. Click a row to open that workspace's usage.

Usage is only tracked for AI Workspaces. Agents that users connect themselves, such as Claude Code or Cursor, use their own model account, so their usage doesn't appear here.

If a workspace is spending more than it should, [stop it](workspaces.md#stop-a-workspace). We also recommend using a spend-limited API key for the workspace model provider.
