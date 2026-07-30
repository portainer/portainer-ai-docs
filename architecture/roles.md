# Roles and RBAC

Portainer-Run has no permissions model of its own. Every access decision it makes is derived from Portainer, at the moment a user authenticates, and re-derived on every request. This page covers exactly how that works.

## Authentication

Users log into Portainer-Run on the same URL as Portainer, with their Portainer credentials. There's no separate Portainer-Run account to create, no duplicate user store, and no independent permissions model to keep in sync. The token _is_ the identity, and it carries the user's Portainer role with it.

## User access determines cluster visibility

What a user can see is governed by their configured access in Portainer:

* **Environments** are only visible when that user (or a team the user belongs to) has been explicitly given access.
* **Namespaces** work the same way - a user or team needs to be given access to a namespace in order for it to appear as an option under Project Spaces when deploying.

A user only ever sees the environments and namespaces their Portainer account can already reach. Portainer-Run doesn't expand access beyond what Portainer already grants. It's a narrower, friendlier surface onto the same permission boundary.

## Admin vs. non-admin

Portainer-Run derives its own roles from Portainer's: **a user with Role 1 (admin) in Portainer is an admin in Portainer-Run.** There's nothing to configure separately.

<table><thead><tr><th width="411">Capability</th><th width="98">Admin</th><th>Non-admin</th></tr></thead><tbody><tr><td>See the <strong>Admin</strong> navigation section</td><td>Yes</td><td>No</td></tr><tr><td>View or manage <strong>Cluster Readiness</strong></td><td>Yes</td><td>No. Sees a notice on Applications if environments have been hidden</td></tr><tr><td>Enable or disable environments</td><td>Yes</td><td>No</td></tr><tr><td>Create personal <strong>Git targets</strong></td><td>Yes</td><td>Yes</td></tr><tr><td>Mark a Git target as <strong>shared</strong></td><td>Yes</td><td>No</td></tr><tr><td>Edit or delete <strong>any</strong> Git target, including shared ones</td><td>Yes</td><td>Only their own</td></tr><tr><td>Use a shared Git target in a deploy flow</td><td>Yes</td><td>Yes, read-only: can Test and use it, can't edit or delete</td></tr><tr><td>See "admin" badge next to their username</td><td>Yes</td><td>No</td></tr></tbody></table>

Everything else, including [Applications](../user/applications.md) and [Deploy](../user/deploy.md), behaves the same for admins and non-admins alike, scoped only by what their token can reach in the underlying cluster.

## How this plays out through MCP

The [MCP endpoint](../user/mcp.md) enforces exactly the same boundary as the browser UI. A token authenticated over MCP sees the same environments (`list_environments` excludes anything disabled via Cluster Readiness), the same namespaces, and the same Git targets (`list_git_targets` returns the caller's own targets plus any shared ones) that the same user would see logging into the UI directly. There's no separate, looser permission surface for AI coding tools; governance travels with the token, not the client.

## Why this matters

This is the core of Portainer-Run's governance model: it doesn't introduce a new place to define who can do what. Every RBAC decision your Portainer administrators have already made, including who can reach which environments, namespaces, and clusters, is inherited automatically and enforced identically, whether the user is clicking through the Applications page, filling in the Vibe Deploy form, or deploying from an AI coding tool over MCP. There's exactly one place to manage access, and it's the place your platform team already manages it today.
