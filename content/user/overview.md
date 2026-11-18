---
title: The customer panel
description: Where hosting customers manage their account.
---

Each hosting account has its own panel at `https://panel.THEIR-DOMAIN/` (or `https://SERVER:7711/user/`). Customers sign in with the account's username (or main domain) and password.

The home page groups the tools like the menu: **Email**, **Files**, **Databases**, **Domains**, **Metrics**, **Security**, **Software**, **Advanced** and **Preferences**, with a search box that finds any tool by name or by what it does ("catch-all", "htaccess", "dkim"). The side shows the account's usage against its limits: disk, bandwidth, domains, mailboxes, databases.

Tools the hosting provider switched off (through the account's feature list) don't appear.

## Preferences

- **Password & Security:** change the account password (it's also the default mailbox's and the main FTP login's).
- **Two-factor sign-in:** an authenticator app and recovery codes. See [security](/user/security/#two-factor-authentication).
- **Change Language** and **Contact Information** (where notifications go).

## Sessions

A session lasts until sign-out, or until it's idle for the time the server allows. An administrator who opens the panel through **Log in to the Account's Panel** gets a separate session marked as such.

## Resellers

A reseller's own account panel has a switch to **Ky Admin**, where it manages its customers. See [resellers](/admin/resellers/).
