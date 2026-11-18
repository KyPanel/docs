---
title: KyPanel documentation
nav: Introduction
description: Install, run and automate a KyPanel hosting server.
---

KyPanel is a hosting control panel for Linux servers. One install gives you web hosting (nginx in front of Apache, PHP-FPM per account), email (Exim, Dovecot, Rspamd, webmail), DNS (PowerDNS), databases (MariaDB), FTP, free certificates, backups and an app deployment system, all managed from three web interfaces:

| Interface | Who uses it | Address |
|---|---|---|
| **Ky Admin** | The server administrator (root) and resellers | `https://SERVER:7711/admin/` |
| **Customer panel** | Each hosting account | `https://SERVER:7711/user/` or `https://panel.THEIR-DOMAIN/` |
| **Webmail** | Each mailbox | `https://SERVER:7712/` or `https://webmail.THEIR-DOMAIN/` |

`SERVER` is the server's hostname, chosen at install time. Every domain hosted on the server also answers on `panel.` and `webmail.` over HTTPS, so customers never need a port number.

## Where to start

- **New server:** check the [requirements](/requirements/), run the [one-line installer](/install/) and follow [first steps](/first-steps/).
- **Moving from another panel:** [import tools](/admin/import/) restore cPanel backups and copy accounts from a running cPanel server over SSH.
- **Moving to a new server:** the [KyPanel Server Transfer](/admin/server-transfer/) copies a whole server, settings and accounts included, to another one.
- **Hosting customers:** the [customer panel guide](/user/overview/) covers email, files, databases, domains, apps and security.
- **Automation:** the [API](/developers/api/), the [`ky` command line](/developers/cli/), [webhooks](/developers/webhooks/) and [single sign-on](/developers/sso/) connect KyPanel to billing systems and scripts. The [API reference](/api/) lists all operations.

## How KyPanel is built

- **One daemon, one agent.** `kyd` serves the panels and the API and runs background jobs; `kyd-agent` is the only part that runs as root and applies changes to the system. They talk over a local socket.
- **Each account is isolated.** Its own Linux user, PHP-FPM pool and cgroup limits for CPU, memory, I/O and processes; SELinux stays in enforcing mode. See [isolation](/reference/isolation/).
- **Signed updates.** Releases are signed and checked before install, applied with automatic rollback, and the operating system gets daily security updates. See [updates](/updates/).
- **Nodes.** Mail and databases can run on separate servers ("nodes") managed from the same panel. See [nodes](/admin/nodes/).

## Getting help

Open a ticket in the [client area](https://my.kypanel.com/tickets/new) or write to support@kypanel.com. Include the server's hostname and, for errors, the time it happened: every change is in the panel's audit log, and every background job keeps its log.
