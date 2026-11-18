---
title: Advanced
description: Terminal, cron jobs, directory indexes, error pages and webhooks.
---

## Terminal

A shell in the browser, as the account's user, in the home folder. Available when the account has shell access; with a **jailed shell** you see your own files and a minimal set of tools. The session ends when you close the tab.

## Cron Jobs

Run commands on a schedule. Choose a common setting (every 5 minutes, hourly, daily…) or a custom schedule in cron syntax:

```text
*/15 * * * *   php /home/acme/public_html/cron.php
0 3 * * *      /home/acme/bin/cleanup.sh >/dev/null 2>&1
```

Times use the server's clock. The output of every job is emailed to the address at the top (empty: no emails). Add `>/dev/null 2>&1` to silence one job. Cron jobs are paused while the account is suspended.

## Indexes

What a visitor sees when they open a folder without an `index` file: **no indexing** (403, the default), **filenames only**, or **filenames and descriptions**. Set it per folder.

## Error Pages

Custom pages for 400, 401, 403, 404, 500 and 503 errors, per domain, written in HTML in the editor. Leave one empty to use the server's page, which shows your host's branding. Your pages also replace the errors the web server answers itself, such as a blocked IP's 403.

## Webhooks

Get a signed `POST` on your own URL when something in the account changes: a domain added, a mailbox created, a database created, a deploy finished. Add the URL, choose the events (or all), and copy the signing secret, shown once. **Deliveries** shows every attempt with the response; failed deliveries are retried for about two days.

The events and how to check the signature are in [webhooks](/developers/webhooks/).
