---
title: Import tools
description: Convert backups from other panels, restore KyPanel backups and move accounts from another KyPanel.
---

KyPanel restores KyPanel backups. A backup from another panel is converted first in **Convert Backup**; the result is a KyPanel backup that **Backup Import** restores. The source server is never modified, and KyPanel never connects to it: the old panel sends the backup (upload, FTP or SSH) and it's converted here. A whole set of accounts, or a live server, moves from another KyPanel with the [KyPanel Server Transfer](/admin/server-transfer/).

## What comes along

| | |
|---|---|
| Account | Username, main domain, addon, sub and parked domains, contact email, package limits, the account's password when it's in a format KyPanel uses. |
| Files | The home folder, with document roots mapped to the same paths. |
| Email | Mailboxes with their mail and passwords, forwarders, domain forwarders, autoresponders, filters, default addresses (catch-all). |
| Databases | MySQL/MariaDB databases, users with their passwords and grants. Names follow the account's prefix. |
| DNS | Zones, with the server's IPs rewritten to this one, and the DKIM keys (mail keeps its signature). |
| SSL | Installed certificates and keys. |
| Other | Cron jobs, SSH keys, extra FTP accounts, the suspension state. |

A password in a format KyPanel doesn't use is left out, with a note: that login gets a new password after the restore.

## Convert Backup

**Import Tools › Convert Backup.** The page lists the converters; the format is detected from the file.

| Converter | Files |
|---|---|
| cPanel | `cpmove-USER.tar.gz`, `USER.tar.gz`, `backup-DATE_USER.tar.gz` (Backup Wizard), split parts (`.part00001`) |
| HestiaCP | `USER.DATE_TIME.tar` (Hestia's backups or `v-backup-user`), gzip or zstd |
| VestaCP | `USER.DATE_TIME.tar` (`v-backup-user`) |
| Plesk | A subscription's or customer's backup: `pleskbackup --domains-name DOMAIN` or a download from Backup Manager (`.tar`) |
| DirectAdmin | `user.CREATOR.USER.tar.zst` or `.tar.gz` (Admin Backup/Transfer, Reseller or User backups) |
| aaPanel | Backup & Restore: `DATE_TIMESTAMP_backup.tar.gz` |
| CyberPanel | `backup-DOMAIN-DATE.tar.gz` (one website) |
| Virtualmin | A top-level server with its sub-servers: one file for all of them, or one file per server (`.tar.gz`) |

Upload the backup, or point to a file on this server (for example one the old server copied to `/home`). The KyPanel backup is written to `/home` as `backup-DATE_USER.tar.gz` and shows up in **Backup Import**.

**Restore after converting** (on by default) imports it right away: the page opens the import's report, and the account is created as soon as the analysis finds no conflict. With a conflict (a username or domain already taken, for example) the import waits for you in the report. Unchecked, the backup just waits in Backup Import.

### From Hestia

Hestia has no main domain: the oldest website that isn't a subdomain of another one becomes it, the other websites become addon domains or subdomains, and each alias stays an alias of its own website (`www.` aliases aren't needed here). Files keep Hestia's paths (`~/web/DOMAIN/public_html`), so the sites' configurations still work. Extra FTP logins become `LOGIN@DOMAIN`. A mail or DNS domain without a website gets an empty one. Not converted: PostgreSQL databases, and Hestia's web templates (sites use this server's web settings).

### From Plesk

One subscription becomes one account: its system user is the username, its domain the main domain, its other sites addon domains or subdomains and its aliases parked domains. The webspace keeps Plesk's folders (`httpdocs`, and each site's own). A customer's backup with one subscription converts the same way; with several, back up each subscription on its own (`plesk bin pleskbackup --domains-name DOMAIN --output-file=/root/DOMAIN.tar`). Plesk keeps the passwords readable in its backups, so mailboxes, databases and FTP logins keep theirs; a backup protected with a password can't be read that way, and those logins get new passwords. Not converted: PostgreSQL databases, Plesk for Windows backups (`.zip`).

### From DirectAdmin

The account keeps its username, and files keep DirectAdmin's paths (`~/domains/DOMAIN/public_html`, subdomains in `public_html/SUB`). Domain pointers become parked domains; mailboxes, forwarders, catch-all, autoresponders, FTP accounts, cron jobs, SSL certificates and DNS zones come along. Not converted: vacation messages and pipes to programs.

### From aaPanel

aaPanel has no accounts, so its backup becomes one account: the first website is the main domain and the others addon domains (or subdomains). Each website keeps its folder name in the home, and the database and FTP passwords are kept. Scheduled shell tasks of one line and URL tasks become cron jobs (backups from aaPanel 8 don't include the scheduled tasks: add them again here). aaPanel's mail server isn't converted: create the mailboxes and copy the mail over IMAP.

### From CyberPanel

One website becomes one account, its system user the username: its child domains, aliases, databases with their users, email accounts with their mail and passwords, DNS records and Let's Encrypt certificates come along.

### From Virtualmin

One top-level server becomes one account: its user is the username, its domain the main domain, its sub-servers addon domains or subdomains and its alias servers parked domains. Files keep Virtualmin's paths (`~/public_html`, sub-servers in `~/domains/DOMAIN/public_html`). Back up the server with its sub-servers in one file: `virtualmin backup-domain --domain DOMAIN --parent --all-features --dest /root/DOMAIN.tar.gz` (or **Backup Virtual Servers** with the sub-servers selected). A file with several top-level servers is refused: back up each one on its own. Mailboxes with their mail, forwarders, the catch-all, databases with the server's MySQL login, DNS zones, SSL certificates and the server's cron jobs come along. Recent Virtualmin versions keep only hashed passwords, so the panel login and the mailboxes get new passwords (unless the server stores plain passwords). Not converted: autoreplies, PostgreSQL databases and scripts installed by Virtualmin (their files are in the home).

## Backup Import

**Import Tools › Backup Import** restores KyPanel backups. Ways to bring one in:

- **Upload** it in the browser (up to the server's free space; large files are better copied to `/home` first);
- a **file on this server**;
- a **URL** (downloaded to the server first);
- a **folder** of backups: every backup file directly in it is queued.

A converted backup is **analyzed first**: what's in it, and any conflict with this server (a username or domain already taken, a database name in use, a missing PHP version). Resolve conflicts (rename, skip, replace) and click **Apply**. **Username here** (optional) restores under another username; databases are renamed to the new prefix.

A backup made by a KyPanel server (the `USER.tar.gz` a backup run makes, or a customer's full backup) is restored straight away as a **foreign account**: the account, files, email, databases, DNS and SSL exactly as they were. The account keeps its username from the backup; a conflicting name needs **Overwrite**.

Any other file is refused as an invalid backup.

## KyPanel Server Transfer

**Import Tools › KyPanel Server Transfer** moves a whole KyPanel server, or some of its accounts, from another KyPanel over its API: settings, accounts, Cloudflare and GitHub connections and API tokens, with the DNS changed to the new addresses. See [Moving to a new server](/admin/server-transfer/).

## After importing

- Check the sites from the server before switching DNS (for example with your `hosts` file pointing to the new IP).
- Lower the DNS TTLs on the old server a day before the move.
- Point the nameservers or A records to this server. AutoSSL issues certificates as soon as the names resolve here.
- Mail arriving at the old server during the switch stays there: copy it again or check both servers for a day.
