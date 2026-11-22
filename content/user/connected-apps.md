---
title: Connected apps (ChatGPT, Claude)
description: Manage the server, an account or a mailbox by talking to ChatGPT, Claude or another app that supports MCP.
---

Apps that support the **Model Context Protocol (MCP)**, such as ChatGPT and Claude, can work on the panel for you, in a normal conversation, on the computer or the phone: "create the account shop.com", "add the mailbox sales@shop.com", "switch the site to PHP 8.3", "why is the certificate of blog.shop.com about to expire?".

The app never gets your password: you sign in on the panel's own page and allow it. It acts with your permissions, nothing more.

## Connect an app

The address below is KyPanel's gateway: it sends you to your own server's sign-in and then relays each request and
answer between the app and your server, without storing them. Your password never passes through it.

Add a **custom connector** with the address:

```
https://mcp.kypanel.com/mcp
```

- **Claude:** **Settings › Connectors › Add custom connector**.
- **ChatGPT:** **Plugins › + › Add custom MCP server** (on some plans, turn on **Developer mode** under **Settings › Apps & Connectors › Advanced** first). Custom connectors depend on the ChatGPT plan, and some plans only allow reading.

The connector asks for your panel's address (the one you open the panel at), then sends you to it.

On the panel's sign-in page, choose how you sign in:

| Sign in as | The app can… |
|---|---|
| **Server** | an administrator: the whole server and every hosting account (a reseller: its own accounts and packages) |
| **Account** | one hosting account: domains, email accounts, files, databases, backups, SSL, DNS, cron jobs |
| **Email** | one mailbox: search, read, move, delete and send its messages; its autoresponder, filters and mail app settings |

Messages are only reachable with the mailbox's own sign-in: a **Server** or **Account** connection manages mailboxes (create, quota, password, forwarders) but never reads them.

Two-factor authentication is asked as on the panel. Tick **Read only** for an app that may look but not change anything.

Once added on the computer, the connector also works in the app on the phone.

## What it does, and what it doesn't

- It works through the same API as the panel: package limits, features, the reseller's privileges and the audit log apply as they do on the screens. The audit log shows the app's name.
- Every change needs two steps: sending an email, deleting or moving messages, suspending an account, writing a file, changing a database… The app tells you exactly what will happen, and only does it after you agree. The ChatGPT app on Android may refuse deletions on its own; use it on the computer for them.
- Emails it sends follow the mailbox's and the account's sending limits, and a copy goes to **Sent**.
- It never sees passwords or keys, and can't download or upload whole files or backups: those stay on the panel.
- New passwords it sets (a new mailbox, a database user) are shown once in the conversation.

## Remove an app

Connected apps are listed with the API tokens, marked **Connected app**: **Security › Manage API Tokens** in the customer panel (with the ones connected to the account's mailboxes and, for a reseller, to its Ky Admin), and **Ky Admin › Security › Manage API Tokens** for administrators. Revoke one and the app stops at once; connect it again to use it.

## Other MCP clients

Any MCP client with OAuth works with the same address, or with your panel's own: `https://SERVER-HOSTNAME/mcp`. A client that only sends a fixed header can use an API token instead: `Authorization: Bearer kpt_…`.
