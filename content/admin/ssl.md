---
title: SSL/TLS
description: Free certificates with AutoSSL, your own certificates, and the service certificates.
---

## AutoSSL

AutoSSL issues and renews free certificates from Let's Encrypt for every site that points to the server: the domain, `www`, aliases, and the service names `panel`, `webmail`, `autoconfig` and `autodiscover`. It also issues the hostname's certificate, used by the panel, webmail, phpMyAdmin, mail (SMTP, IMAP, POP3) and FTP.

For email, each domain whose mail is received here also gets a certificate for `mail.<domain>`, installed where its mailboxes are (this server or its mail node). The mail server presents it to clients that connect with that name (SNI) and the hostname's certificate to everyone else, so customers can use `mail.<their domain>` in their mail apps. A `mail.` name that doesn't point to that server is skipped and the client settings show the hostname. These certificates are renewed with the account's AutoSSL run; problems with them appear in the run's log, not as AutoSSL problems.

**SSL/TLS › Manage AutoSSL**:

- **Run for all accounts**, or one account, right now;
- each account's last result, last and next check, and problems (a name that doesn't resolve here, a rate limit);
- recent runs with their logs;
- **Certificate profile**:

| Profile | Lifetime | Renewed |
|---|---|---|
| Classic (default) | 90 days | 30 days before expiry |
| TLS server | 45 days | with a third of its life left |
| Short-lived | about 6 days | with about 2 days left; AutoSSL then checks every 4 hours |

New certificates use the chosen profile; current ones change at their next renewal.

A domain is checked over HTTP (`/.well-known/acme-challenge/`) before the certificate is ordered, so names that don't point here are skipped instead of failing the order. Names behind Cloudflare's proxy are issued too. Customers can exclude a domain from AutoSSL.

**Wildcards** (`*.example.com`, from a customer's `*` subdomain or alias) can only be validated through DNS (DNS-01). AutoSSL publishes the `_acme-challenge` TXT record in the account's zone, waits until every nameserver of the zone answers it, and removes it after the order. That works when the zone's public nameservers are this server's, or the zone is linked to Cloudflare (the record goes to Cloudflare right away). Otherwise the wildcard is left out and its subdomains get the domain's own certificate; a valid wildcard certificate is kept if a renewal fails. Names a wildcard already covers (`shop.example.com`) aren't added to the order again.

## Certificates per site

**SSL/TLS › Manage SSL Hosts** lists every site with its certificate, issuer and expiry. Filter by expiring or expired, without a certificate, or with an AutoSSL problem.

**Install a certificate** puts a certificate you bought (certificate, private key and CA bundle) on any domain. The panel checks that the key matches and the chain is complete. Customers do the same in their [SSL/TLS](/user/security/#ssl-tls) page.

## Service certificates

**Service Configuration › Manage Service SSL Certificates** shows the hostname certificate the services use and requests a new one. If the hostname doesn't resolve to the server yet, the panel keeps its self-signed certificate until it does.
