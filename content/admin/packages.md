---
title: Packages and features
description: Plans with limits, resource isolation and the tools each customer can use.
---

A **package** is a plan: an account always has one, and changing a package updates every account on it. A **feature list** decides which tools of the customer panel an account can use; each package points to one.

## Packages

**Packages › Add a Package** or **Edit a Package**.

### Resources

| Limit | |
|---|---|
| Disk Space Quota (MB) | Files, mail and databases. Enforced by the filesystem quota. |
| Monthly Bandwidth Limit (MB) | Web, mail and FTP traffic. Going over it notifies the customer and the admins; with **Suspend the websites** on (Account Functions › Account Settings) the sites show a bandwidth page (HTTP 509) until next month or a higher limit. |
| Max Addon Domains, Max Sub Domains, Max Parked Domains | Domains the customer can add. |
| Max Email Accounts, Max Quota per Email Address (MB) | Mailboxes. |
| Maximum Hourly Email by Domain Relayed | Outgoing mail per domain per hour. |
| Max SQL Databases, Max FTP Accounts | |

Leave a field empty for unlimited.

### Isolation limits

Enforced per account with cgroups v2 (see [isolation](/reference/isolation/)):

| Limit | |
|---|---|
| CPU | Percent of one core (100 = one full core, 200 = two). |
| Memory | MB of RAM for all of the account's processes. |
| I/O | Disk throughput (MB/s) and operations per second. |
| Processes | Maximum processes and threads. |
| Entry processes | Concurrent web requests to PHP. |
| Inodes | Maximum number of files. |

### Settings

- **PHP workers per account:** the PHP-FPM pool size. Raise it for busy sites; each worker uses memory.
- **Shell access:** no shell, a **jailed shell** (the account sees only its own files and a minimal system) or a normal shell.
- **Feature list**, **Language**, **Theme**.
- **Dedicated IP:** new accounts on the package get a free dedicated IP.

Deleting a package asks where its accounts go.

## Feature lists

**Packages › Feature Manager.** Every tool of the customer panel is a feature: Email Accounts, Forwarders, Autoresponders, File Manager, FTP Accounts, phpMyAdmin, Zone Editor, Cloudflare, SSL/TLS, SSH Access, Terminal, Cron Jobs, Apps, API Tokens and so on.

Create a list, switch off what the accounts shouldn't have, and choose the list in the packages. A tool that's off disappears from the customer panel and its API answers `403 feature-disabled`. Admins are never limited.

Examples: a "Mail only" list without web tools; a "No shell" list without SSH Access and Terminal.

## Resellers and packages

Resellers create their own packages, limited by their privileges: they may be allowed unlimited packages or not, and may see root's packages or only their own. See [resellers](/admin/resellers/).
