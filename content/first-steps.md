---
title: First steps
description: From a fresh install to the first hosted site.
---

## 1. Sign in

Open `https://SERVER:7711/admin/` and sign in as `root` with the password the installer printed. You're asked to choose a new password right away.

The panel asks Let's Encrypt for a certificate for the hostname as soon as it starts. Until it arrives (a minute or two, once the hostname's A record points to the server) the browser warns about a self-signed certificate.

## 2. Setup wizard

The first sign-in opens a short wizard:

1. **Terms.** Accept the license terms.
2. **License, contact and nameservers.**
   - The license block shows the trial and the IP the license is bound to. See [license](/license/).
   - **Administrator email:** where alerts go (disk full, service down, failed backups, updates).
   - **Nameservers:** the two names your customers' domains will use, like `ns1.example.com` and `ns2.example.com`. Leave them empty if this server won't host DNS; new accounts then get no DNS zone until you set them in Server Configuration › Basic Setup.
3. **Done.**

## 3. Secure the admin login

- Turn on two-factor authentication for root: click your name (top right) › **Password & 2FA**.
- Review **Security Center › Brute Force Protection**. It's on by default for every login (panel, webmail, mail, FTP, SSH).
- If you know where you manage the server from, restrict the panel port in **Security Center › Host Access Control**.

## 4. Create a package

A package is a plan: disk and bandwidth quotas, how many domains, mailboxes, databases and FTP accounts, shell access, PHP limits and which customer features are available.

**Packages › Add a Package.** Name it (for example "Starter"), set the limits and save. See [packages](/admin/packages/).

## 5. Create an account

**Account Functions › Create a New Account.**

| Field | |
|---|---|
| Domain | The customer's main domain, e.g. `example.com`. |
| Username | The Linux user and login name. Up to 16 lowercase letters and digits, starting with a letter. Suggested from the domain. |
| Password | Generated, or your own. Checked against the password policy. |
| Contact email | Where the customer's notifications go. |
| Package | The plan. |

The account is ready in a few seconds: Linux user, home folder, PHP-FPM pool, website, DNS zone, mail domain with DKIM, default mailbox and FTP login. The job log shows each step.

## 6. Point the domain

The customer (or you) changes the domain's nameservers at the registrar to the ones from step 2. If the domain's DNS stays elsewhere, add an A record for the domain and `www` pointing to the server's IP, plus the MX and TXT records listed in the customer panel under **Email Deliverability**.

As soon as the domain points to the server, **AutoSSL** issues free certificates for the domain, `www`, `panel`, `webmail`, `autoconfig` and `autodiscover`, and renews them automatically.

## 7. Turn on backups

Backups are **off** after install. Open **Backup › Backup Configuration**, turn them on, choose the schedule and add a destination (S3-compatible storage, SFTP, rsync or FTPS) under **Backup Destinations**. See [backups](/admin/backups/).

## 8. Give the customer access

Send the customer:

- the panel address: `https://panel.example.com/` (or `https://SERVER:7711/user/`), with their username and password;
- the webmail address: `https://webmail.example.com/`.

To open their panel yourself without the password, use **List Accounts ›** the account's menu **› Log in to the Account’s Panel**.

## Next

- Moving accounts from another server: [import tools](/admin/import/).
- Moving this server to a bigger one later: [Moving to a new server](/admin/server-transfer/).
- Selling hosting: connect your billing system with the [API](/developers/billing/).
- Mail on its own server: [nodes](/admin/nodes/).
