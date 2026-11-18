---
title: License
description: Trial, pricing and what happens when a license lapses.
---

## How licensing works

- **One license per server**, bound to the server's main public IPv4. There is no key to copy: the server checks in with `license.kypanel.com` and the license follows its IP.
- **30-day free trial** from the first install on an IP. Everything works during the trial.
- **$49 a month or $490 a year** per server, unlimited accounts and domains. [Mail and database nodes](/admin/nodes/) are free: only the panel server needs a license.

## Buy a license

1. In Ky Admin open **Server Configuration › License** and click **Register this server**. It opens the client area with the server's IP filled in. You can also go to [my.kypanel.com](https://my.kypanel.com/) and choose **Add a server**.
2. Choose monthly or yearly and pay by card.
3. Back in the panel, click **Check now**. The status turns to **Active**. Without clicking, the server picks the license up at its next check-in (every 12 hours).

Invoices, cards and cancellations are in the client area under **Billing**.

## Moving to another IP

The license is bound to the IP. If the server's main IP changes, or you move to a new server, open a ticket and we move the license.

## Statuses

| Status | Meaning |
|---|---|
| Trial | First 30 days. Fully working. |
| Active | Paid. The server holds a signed lease, renewed at every check-in. |
| Grace | The server couldn't reach the license server when the lease ran out. 14 more days, fully working; administrators are notified. |
| Read-only | Trial over without a license, grace over, or the license was canceled. |

## Read-only mode

A license problem **never takes sites, mail or DNS down**. Without a valid license the panel becomes read-only:

- Everything keeps running: websites, mail, DNS, databases, cron jobs, backups and certificate renewals.
- Admins and customers can sign in and see everything.
- Changes are refused (the API answers `402`), except what's needed to keep the server safe and running: passwords, two-factor settings, restarting services, killing processes, suspending, unsuspending and terminating accounts, license checks and panel updates.

As soon as the license is active again, click **Check now** and the panel is writable.

## What the check-in sends

The server's IP, hostname and KyPanel version, signed with a key unique to the install. Never your customers' data.
