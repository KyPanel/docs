---
title: Files and logs
description: Where KyPanel keeps its files, the accounts' data and the logs.
---

## KyPanel

| Path | |
|---|---|
| `/usr/local/kypanel/bin/` | `kyd` (panel and API), `kyd-agent` (root executor), `ky` (command line), `kp-jailshell` |
| `/etc/kypanel/` | Configuration: `role`, `update.conf`, `license.conf`, TLS certificate and key of the panel, the master key |
| `/var/lib/kypanel/` | The panel's database (`kypanel.db`, SQLite) and state |
| `/var/log/kypanel/` | Panel logs (apps' deploy logs under `apps/`) |
| `/backup/` | Local backups (configurable) |
| `/etc/yum.repos.d/kypanel-*.repo`, `/etc/apt/sources.list.d/kypanel-*.sources` | The KyPanel package mirror ([updates](/updates/#the-kypanel-package-mirror)) |

`kyd` and `kyd-agent` run as systemd services (`kyd.service`, `kyd-agent.service`); their logs are in the journal: `journalctl -u kyd -u kyd-agent`.

> [!WARNING]
> `/etc/kypanel` holds the server's master key, which encrypts stored secrets (API tokens, backup credentials, webhook secrets). Back it up with the server; a restore without it can't read those secrets.

## Accounts

| Path | |
|---|---|
| `/home/USER/` | The account's home |
| `/home/USER/public_html/` | The main domain's site |
| `/home/USER/DOMAIN/` | An addon domain's or subdomain's site (default) |
| `/home/USER/mail/DOMAIN/MAILBOX/` | Mailboxes (Maildir) |
| `/home/USER/logs/` | The account's PHP error log and archived access logs |
| `/home/USER/apps/NAME/` | [Apps](/user/apps/#layout): `releases/`, `current`, `shared/` |
| `/home/USER/.trash/` | The File Manager's trash |

## Logs

| Log | AlmaLinux / Rocky / CloudLinux | Ubuntu |
|---|---|---|
| Sites (access and errors), per domain | `/var/log/httpd/domains/` | `/var/log/apache2/domains/` |
| Mail (Exim) | `/var/log/exim/main.log` | `/var/log/exim4/mainlog` |
| IMAP/POP3 (Dovecot) | the journal: `journalctl -u dovecot` | same |
| FTP | `/var/log/pure-ftpd/transfer.log` | same |
| Panel | `journalctl -u kyd -u kyd-agent` | same |

Every change made in the panel or through the API is also in the panel's **audit log**, and every background job keeps its own log (Ky Admin shows it with the job).
