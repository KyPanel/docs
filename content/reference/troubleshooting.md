---
title: Troubleshooting
description: Common problems and how to fix them.
---

## I can't sign in to Ky Admin

- **Lost the password:** on the server, as root, run `ky admin reset-root`. It prints a new password, turns off root's two-factor authentication and lifts the administrator sign-in IP list, if one was set.
- **"Too many attempts", or the panel doesn't load from your IP:** brute force protection blocked your address. Blocks are temporary; to clear it now, sign in from another connection (a phone's hotspot) and unblock it in Security Center › Brute Force Protection. Then add your address to the allow list.
- **Lost the 2FA device:** use one of the recovery codes. Without one, `ky admin reset-root`.
- **Administrator sign-in is restricted by IP** (Security Policies) and you're elsewhere: sign in from an allowed address, or run `ky admin reset-root` on the server.

## The browser warns about the certificate

The panel uses a self-signed certificate until Let's Encrypt issues one for the hostname. Check that the hostname's A record points to the server and that port 80 is open, then Service Configuration › Manage Service SSL Certificates › request again.

## AutoSSL didn't issue a certificate

SSL/TLS Status (customer) or Manage AutoSSL (admin) shows why. Usually:

- the name doesn't resolve to this server (DNS not changed yet, or an old AAAA record pointing elsewhere);
- the domain is behind a proxy that blocks `/.well-known/acme-challenge/`;
- Let's Encrypt's rate limit for the domain was reached: it retries later.

## A subdomain shows an error or another site

A subdomain without its own entry isn't served. Add it in Domains, or add the subdomain `*` to show the domain's site for every subdomain (see [wildcard](/user/domains/#domains)). Its DNS needs a `*` record pointing here; the panel adds it to zones it manages.

## A Laravel app stops signing in after changing APP_KEY

Laravel caches its routes, and some of them depend on `.env` values: Livewire's script path comes from `APP_KEY`, domain routes from their domain variables. Save the `.env` with **Run php artisan optimize** checked, or deploy again. "The MAC is invalid" in the log means data encrypted with the old key (sessions, cookies, encrypted columns): use the `APP_KEY` the data was created with, for example the old server's when you moved the database.

## Mail goes to spam or is refused

Open Email › Email Deliverability for the domain and fix what's red: SPF, DKIM, DMARC, and the **reverse DNS (PTR)** of the server's IP, set at your hosting provider. Check Mail Delivery Reports for the remote server's answer. A new IP may need a few days of low volume to build reputation.

## A site shows "503"

The account is suspended (check List Accounts), or its PHP pool can't start: look at the domain's error log (Metrics › Errors) and the account's Resource Usage for memory faults.

## A site is slow

Check Resource Usage for the account: frequent CPU, memory or entry-process faults mean it hits its package limits. Raise the limits or the PHP workers, or cache the site.

## The panel is read-only

The license isn't valid: see Server Configuration › License. Sites and mail keep working. See [license](/license/#read-only-mode).

## An update failed

The update rolled back by itself; the panel keeps running the previous version and the admins got a notification with the reason. Server Configuration › Update Preferences shows the last result. Automatic updates skip that version and wait for the next one; **Update Now** tries it again. Open a ticket with the time it happened.

## Get help

Open a ticket in the [client area](https://my.kypanel.com/tickets/new). Include the server's hostname and the time of the problem. For a failed job, the job's log; for a service, `journalctl -u SERVICE --since "1 hour ago"`.
