---
title: Domains
description: Domains, subdomains, redirects, DNS and Cloudflare.
---

## Domains

**Domains › Domains** lists the account's domains:

| Type | |
|---|---|
| Main | The account's domain. |
| Addon | Another site, with its own folder. |
| Subdomain | `blog.example.com`, with its own folder. |
| Alias | Another name for an existing site (same files), e.g. `example.net` showing `example.com`. Also called a parked domain. |

**Create a New Domain:** type the name; choose whether it's a separate site (and its **document root**, by default a folder named after the domain) or an alias. Subdomains are made the same way with a name under an existing domain.

**A name under another account's domain** (say `portal.example.com`, when `example.com` is hosted here by someone else) can be added as an alias or addon domain once its DNS shows it was pointed to you: a CNAME from that name to your main domain, or a TXT record `_kypanel.<name>` with the code the form shows when it refuses the name. Whoever controls the domain's DNS decides, so nobody can take a name under someone else's domain. Its records stay in the owner's zone. The exact name of another account's domain can't be added.

**Wildcard:** the subdomain `*` (or the alias `*.example.com`) shows example.com's site, or its app, for every subdomain that doesn't have an entry of its own: `shop.example.com` keeps its own site, `anything-else.example.com` gets example.com's. Its DNS is a `*` record in the zone. A wildcard doesn't receive email. Its certificate (`*.example.com`) can only be validated through DNS: AutoSSL does it when the zone's nameservers are this server's or the zone is linked to Cloudflare; otherwise the subdomains are served with the domain's own certificate.

Per domain:

- **Document root:** point the site to another folder (files aren't moved).
- **Force HTTPS Redirect:** send `http://` visitors to `https://` (once the domain has a certificate).
- **PHP version** and **PHP settings** (see [Software](/user/software/)).
- **Remove:** deletes the site, its DNS zone or records, its mail domain and certificate. Files are kept unless you choose to remove them. The main domain can't be removed.

Every new domain gets a DNS zone (or records in its parent's zone; an alias below one of the account's domains, like `blog.example.com`, also goes into that zone), a mail domain with DKIM, and a certificate from AutoSSL as soon as it points here.

## Redirects

**Domains › Redirects:** send a path to another URL with 301, 302, 307 or 308. Options:

- one domain or all of the account's domains;
- with or without `www`, or both;
- **wildcard:** every path below the source keeps its rest and query string (`/old/a?x=1` → `https://new/a?x=1`); without it, only the exact path redirects.

Redirects run in the front web server, before the site's code. Certificate renewals are never redirected.

## Zone Editor

**Domains › Zone Editor** edits the DNS records of the account's zones: A, AAAA, CNAME, MX, TXT, SRV, CAA and more. Changes are live in seconds; other resolvers see them when the TTL expires.

- **Reset Zone** puts the zone back to the server's template (the records the domain started with). Your own records are removed; DKIM is kept.
- The DKIM record is managed by the mail system and can't be edited here.

The domain's nameservers at the registrar must point to the server for the zone to be used. See [Email Deliverability](/user/email/#email-deliverability) for the mail records.

## Cloudflare

**Domains › Cloudflare** connects the account to Cloudflare with an API token, then per domain:

- **link** it to its existing Cloudflare zone, or **create** the zone;
- **sync** the records from the Zone Editor to Cloudflare (preview first), then keep them in sync;
- turn Cloudflare's **proxy** (orange cloud) on or off per record, right in the Zone Editor;
- **purge the cache**;
- **unlink** or **disconnect**.

Create a Cloudflare API token with **Zone › Zone › Read**, **Zone › DNS › Edit**, **Zone › Zone Settings › Edit** and **Zone › Cache Purge › Purge** (plus **Zone › Zone › Edit** to add domains); the page shows which permissions are missing. The token is stored encrypted and isn't carried in backups. Certificates keep renewing behind the proxy. When a site moves here through Cloudflare without a certificate yet, the preview says so and AutoSSL requests one right after publishing; visitors may see a Cloudflare SSL error for about a minute.
