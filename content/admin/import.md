---
title: Import tools
description: Move accounts from cPanel or Hestia backups, straight from a cPanel server, or from another KyPanel.
---

KyPanel imports accounts from cPanel, Hestia and other KyPanel servers: the account, its files, email, databases and DNS. A cPanel or Hestia backup is analyzed and nothing changes until you **apply** it; a KyPanel account backup is restored straight away as a foreign account. The source is never modified. A whole set of accounts, or a live server, moves with the [KyPanel Server Transfer](/admin/server-transfer/) instead.

## What's imported

| | |
|---|---|
| Account | Username, main domain, addon, sub and parked domains, contact email, package limits, password (the original hash, so the same password works). |
| Files | The home folder, with document roots mapped to the same paths. |
| Email | Mailboxes with their mail and passwords, forwarders, domain forwarders, autoresponders, filters, default addresses (catch-all). |
| Databases | MySQL/MariaDB databases, users with their passwords and grants. Names follow the account's prefix. |
| DNS | Zones, with the server's IPs rewritten to this one, and the DKIM keys (mail keeps its signature). |
| SSL | Installed certificates and keys. |
| Other | Cron jobs, SSH keys, extra FTP accounts, the suspension state. |

## Backup Import

**Import Tools › Backup Import.** Accepted files:

- cPanel: `cpmove-USER.tar.gz`, `USER.tar.gz`, `backup-DATE_USER.tar.gz`, or an extracted folder;
- Hestia: `USER.DATE_TIME.tar` from `/backup` (made by Hestia's backups or `v-backup-user`), gzip or zstd.

Ways to bring the backup in:

- **Upload** it in the browser (up to the server's free space; large files are better copied to `/home` first);
- a **file on this server**;
- a **URL** (downloaded to the server first);
- a **folder** of backups: every backup file directly in it is queued.

Each import is **analyzed first**: what's in the backup, and any conflict with this server (a username or domain already taken, a database name in use, a missing PHP version). Resolve conflicts (rename, skip, replace) and click **Apply**.

**Username here** (optional) imports under another username; databases are renamed to the new prefix.

### From Hestia

Hestia has no main domain: the oldest website that isn't a subdomain of another one becomes it, the other websites become addon domains or subdomains, and each alias stays an alias of its own website (`www.` aliases aren't needed here). Files keep Hestia's paths (`~/web/DOMAIN/public_html`), so the sites' configurations still work. Passwords stay the same: the account and FTP logins (yescrypt), mailboxes (bcrypt) and database users. Extra FTP logins become `LOGIN@DOMAIN`. A mail or DNS domain without a website gets an empty one. Not imported: PostgreSQL databases, and Hestia's web templates (sites use this server's web settings).

## From another KyPanel

A KyPanel account backup (the `USER.tar.gz` a backup run makes, or a customer's full backup) imports straight from **Backup Import**: upload it or point to it on the server, the same as a cPanel one. KyPanel recognizes its own backup and restores it as a **foreign account** — the account, files, email, databases, DNS and SSL exactly as they were — without the analyze-and-review step a cPanel import has. The account keeps its username from the backup; a conflicting name needs **Overwrite**. To move many accounts at once, or a live server, use the [KyPanel Server Transfer](/admin/server-transfer/).

## cPanel Server Transfer

**Import Tools › cPanel Server Transfer** copies accounts straight from a running cPanel server over SSH:

1. Enter the source server's address and root credentials (password or key; kept encrypted only while the transfer runs).
2. Confirm the source's SSH host key.
3. The panel lists the source's accounts: choose which to move.
4. Each account is packaged on the source (`pkgacct`), copied here and imported like a backup, with the same analysis and conflict choices.

The source server isn't changed: accounts keep running there until you point DNS here.

## KyPanel Server Transfer

**Import Tools › KyPanel Server Transfer** moves a whole KyPanel server, or some of its accounts, from another KyPanel over its API: settings, accounts, Cloudflare and GitHub connections and API tokens, with the DNS changed to the new addresses. See [Moving to a new server](/admin/server-transfer/).

## After importing

- Check the sites from the server before switching DNS (for example with your `hosts` file pointing to the new IP).
- Lower the DNS TTLs on the old server a day before the move.
- Point the nameservers or A records to this server. AutoSSL issues certificates as soon as the names resolve here.
- Mail arriving at the old server during the switch stays there: copy it again or check both servers for a day.
