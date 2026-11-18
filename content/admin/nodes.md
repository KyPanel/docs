---
title: Nodes
description: Run mail and databases on separate servers managed by the same panel.
---

A **node** is a server that runs only mail or only databases for a KyPanel panel server. The panel creates mailboxes and databases on it, shows its status, services and firewall, and keeps it updated. Nodes don't need a license.

| Node role | Runs | Why |
|---|---|---|
| `mail` | Exim, Dovecot, Rspamd: the mailboxes and all outgoing mail | Mail keeps working while the web server is down or busy; a clean IP reputation for mail. |
| `database` | MariaDB | Database load off the web server; more memory for databases. |

The panel server can run with role `all` (it keeps local mail and databases too, and nodes are optional per account) or `web` (everything goes to nodes).

## Add a node

1. On the new server, install with the node's role:
   ```sh
   curl -fsSL https://updates.kypanel.com/install.sh | bash -s -- --role mail --hostname mail1.example.com
   ```
   At the end it prints a **pairing code** (valid once).
2. Make sure the panel server can reach the node on port **7714/tcp** (open it in any firewall in front of the node).
3. In Ky Admin: **Server Configuration › Link Server Nodes › Add node**, paste the code.

The link between the panel and the node is mutually authenticated TLS. Right after pairing, the node's port 7714 is restricted to the panel's IP.

A new pairing code, if you lost it: on the node, `/usr/local/kypanel/bin/kyd-agent node-init --role mail --host mail1.example.com`.

## Mail nodes

With a mail node, **all mail goes through it**:

- the domains' MX, `mail.` and SPF records point to the node;
- mail clients connect to the node directly (the client settings show its hostname);
- webmail, on the panel server, reaches the mailboxes through the link;
- mail from the sites, cron jobs and the panel is handed to the node, which signs it (DKIM) and sends it. If the node is unreachable, it waits in the panel server's queue and never goes out from there.

Pair the mail node **before** creating accounts, so new zones get the node's records from the start. New accounts choose their **Email Server**; the default is the first mail node.

## Database nodes

Databases of accounts on a database node live there; phpMyAdmin and the sites connect through the node's address. **Remote Database Access** applies on the node. New accounts choose their **Database Server**. On a panel server installed without MariaDB (role `web`), **Databases** in Ky Admin opens the database node.

## Managing nodes

**Link Server Nodes** lists the nodes with their status and version. The machine pages have a server selector to work on a node: Server Status (and restart), Service Manager, Process Manager, Hardware & Disks, root password, DNS resolvers, Firewall, Mail Queue (mail nodes), database processes and root access (database nodes).

- **Updates** follow the panel: same channel, same automatic setting, and nodes install the panel's release when it updates.
- **Operating system updates** run on the nodes too.
- **Brute force protection** reads the nodes' failed logins (SSH, IMAP/POP3, SMTP) and blocks attackers on every server.
- **Firewall:** port 7714 stays locked to the panel. SSH (22) can be disabled or deleted on a node; the panel keeps managing it over the link.

Removing a node from the panel requires that no account uses it. To put everything back on one server, move to a new server without nodes with the [KyPanel Server Transfer](/admin/server-transfer/): each account's mail and databases land on it.
