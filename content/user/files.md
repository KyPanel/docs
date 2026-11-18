---
title: Files
description: File manager, FTP, disk usage, protected folders and backups.
---

The account's files live in its home folder, `/home/USERNAME`. The main domain's site is in `public_html`; addon domains and subdomains get their own folder (e.g. `/home/USERNAME/shop.example.com`), which can be changed.

## File Manager

**Files › File Manager** — browse, upload (drag and drop, several files at once), download, create files and folders, rename, move, copy, delete, change permissions, compress (zip, tar.gz, tar.bz2) and extract archives, and edit text files in a code editor with syntax highlighting. Search by name. Show or hide dotfiles like `.htaccess`.

Deleted files go to the **Trash** (Files › Trash) and can be restored, unless you choose to delete permanently.

## Disk Usage

What takes space, folder by folder. Mail and databases are shown separately. The report is measured on request and cached.

## FTP Accounts

Extra FTP logins, each limited to a folder of the account and an optional quota. The account's username and password also work for FTP, with access to the whole home folder.

Connect with FTPS (explicit TLS) on port 21, or with SFTP on port 22 when the account has shell access. **FTP Connections** lists open sessions and disconnects them.

## Directory Privacy

Password-protect a folder of a site: the browser asks for a username and password. Add one or more users per folder.

## Backup

Download a full backup of the account, or parts of it (home folder, a database, forwarders, filters); upload a partial backup to restore it; restore files from the server's scheduled backups. See [backups](/admin/backups/#customers-own-backups).

**Restore from another host** (Files › Backup › Restore from another host) brings your account in from another server: upload a full backup of your old account — a cPanel `cpmove`, a Hestia backup, or a KyPanel backup made with this same username — and it is restored into this account (files, email, databases and DNS). It replaces what's in your account now, keeps your username and plan, and can't claim a domain already hosted here by another account.

## Permissions

Sites run as the account's own user, so files don't need to be world-writable: `644` for files and `755` for folders is right; `777` is never needed.
