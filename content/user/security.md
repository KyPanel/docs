---
title: Security
description: Certificates, SSH, two-factor sign-in, API tokens, blocking visitors and WordPress protection.
---

## SSL/TLS Status

Every domain with its certificate: issuer, expiry, and why AutoSSL couldn't issue one (usually: the name doesn't point to this server yet). **Run AutoSSL** checks again right away. Exclude a domain from AutoSSL if you install your own certificate for it. A wildcard (`*.example.com`) joins its domain's certificate when its validation record can be published: the zone's nameservers are this server's, or the zone is linked to Cloudflare.

## SSL/TLS

**Security › SSL/TLS** manages certificates you bring:

- **Private Keys:** generate (RSA 2048/3072/4096 or ECDSA P-256/P-384) or upload;
- **Certificate Signing Requests:** create a CSR to buy a certificate;
- **Certificates:** upload a certificate;
- **Install an SSL Certificate:** put a certificate, its key and the CA bundle on a domain. The panel checks that they match.

## SSH Access

When the account has shell access: generate or import SSH keys, and **authorize** the ones allowed to log in. Connect with `ssh USERNAME@SERVER`. With a **jailed shell** you see your own files and a minimal set of tools, not the rest of the server. SFTP uses the same keys.

## Two-Factor Authentication

Protect the panel login with an authenticator app (Google Authenticator, Authy, 1Password…). Scan the QR code, enter a code, and **save the recovery codes**: each works once if you lose the phone. Your hosting provider can also turn it off for you.

## Manage API Tokens

Tokens let scripts and tools manage the account through the API without your password: **full access**, **read-only**, or chosen tools, with an expiry and optional IP limits. The token is shown once. Tokens can't change the account's password or two-factor settings. See [the API](/developers/api/) and [the CLI](/developers/cli/).

## IP Blocker

Block an address, a range (`203.0.113.0/24`) or a list of them from all of the account's sites. Visitors from those addresses get "403 Forbidden".

## Hotlink Protection

Stop other sites from embedding your images and files: list the URLs allowed to link (your own domains are added), the file extensions to protect, whether direct requests (no referrer) are allowed, and optionally a URL to redirect blocked requests to.

## WordPress Protection

Per site (aliases follow their site):

- **Block xmlrpc.php:** XML-RPC is a common way to guess WordPress passwords. Requests get "403 Forbidden". Don't turn it on if the site uses Jetpack or the WordPress mobile app: they need it.
- **Hide user list:** visitors can't list the site's usernames through `/wp-json/wp/v2/users` or `?author=1` (bots do this before guessing passwords). Signed-in WordPress users still see it, so the editor keeps working. With "Plain" permalinks, author pages (`?author=N`) stop working for visitors.

Login attempts on every site are also limited per IP by the server, whatever these settings are.
