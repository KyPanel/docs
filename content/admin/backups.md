---
title: Backups
description: Scheduled account backups, destinations and restores.
---

Backups are **off** after install. Turn them on in **Backup › Backup Configuration**.

## Configuration

| Setting | |
|---|---|
| Type | **Compressed** (one `.tar.gz` per account), **uncompressed**, or **incremental** (a folder per account; only changed files are copied). |
| Schedule | **Daily** on the days you choose, **weekly** on one day, **monthly** on chosen days of the month. |
| Retention | How many daily, weekly and monthly backups to keep. |
| What to include | Accounts, suspended accounts, access logs, bandwidth data, system files (the panel's database and configuration). |
| Databases | Per account (a dump per database), the whole MariaDB data directory, or both. |
| Keep a local copy | Keep the backups on this server too, or only at the destinations. |
| Minimum free space | Skip the run instead of filling the disk. |
| Exclusions | Paths not to back up, for every account. |

**Backup User Selection** turns backups on or off per account. **Run now** starts a run immediately.

Each run backs up one account at a time and records what happened. Runs with problems notify the admins.

## Destinations

**Backup › Backup Destinations** — where copies go besides the server:

| Destination | |
|---|---|
| Local directory | Another disk mounted on the server. |
| Amazon S3 | A bucket on AWS. |
| S3-compatible | Cloudflare R2, Backblaze B2, Wasabi, MinIO and others (endpoint, region, path-style). |
| SFTP | Any server you reach over SSH (password or key). |
| Rsync over SSH | Only what changed travels; best with incremental backups. |
| FTP / FTPS | FTP over TLS (explicit or implicit), or plain FTP. |

**Validate** tests a destination by writing and reading a file. Secrets (keys, passwords) are stored encrypted.

## Restore

**Backup › Backup Restoration** — choose by **date** or by **account**, then what to restore:

- the whole account (it's recreated if it no longer exists);
- or parts: files, databases, mail, DNS.

Restores run as background jobs with their log.

## Customers' own backups

In their panel (**Files › Backups**), customers can:

- make a **full backup** of their account (saved in their home or sent to their FTP/SFTP server);
- download their home folder, a database, a domain's forwarders or their email filters;
- upload a partial backup to restore it;
- browse the server's backups of their account and **restore files** or folders from them;
- **restore parts** of a KyPanel backup (the files, a mailbox, a database, an app, DNS, SSL, cron jobs, keys and connections…) and leave the rest of the account as it is, or restore the whole account;
- convert a backup from another panel in **Files › Convert Backup** (see [import tools](/admin/import/)).

KyPanel restores only KyPanel backups; any other file is refused as an invalid backup.

### Keys and connections

Backups include the account's keys and connections by default: the Cloudflare and GitHub connections, API tokens, two-factor, passkeys, webhooks, contact channels and app secrets (webhook secret, Composer credentials, deploy hook). A restore on another server then works without connecting everything again. Turn it off in **Backup › What goes in** (server backups) or when making a full backup (customers). The archive isn't encrypted: keep backups somewhere safe. GitHub connections need the same GitHub App on the new server, and passkeys work only on the same panel address.

## Before updates

Panel updates take their own safety copy of the panel's database and configuration and roll back on failure. That's not a substitute for scheduled backups.
