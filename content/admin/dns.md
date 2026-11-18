---
title: DNS
description: Zones, records and zone templates of the server's DNS.
---

KyPanel runs an authoritative DNS server (PowerDNS). Every account's domain gets a zone, created from a template, that the customer edits in the [Zone Editor](/user/domains/#zone-editor). Root sees and edits every zone in **DNS Functions › DNS Zone Manager**.

## Nameservers

Set the server's nameservers in the setup wizard or **Server Configuration › Basic Setup** (two to four names, like `ns1.example.com`). Register them at the registrar of their domain, with **glue records** pointing to the server's IP. Resellers can have their own nameservers.

Without nameservers, new accounts get no DNS zone: their DNS stays with whoever hosts it.

## Zones

**DNS Zone Manager** lists every zone with its account. Open a zone to:

- add, edit and delete records (A, AAAA, CNAME, MX, TXT, SRV, CAA, NS and more);
- **turn a record off**: it stays in the zone but isn't published;
- edit the **SOA** (primary nameserver, contact, refresh, retry, expire, negative cache, default TTL, serial);
- **export** the zone file (BIND format) or **import** one, replacing the zone;
- **reset** the zone to the template;
- **publish** it again (bumps the serial for secondaries).

**Add a DNS Zone** creates a zone for a domain that has none yet: a parked domain's parent, the hostname's domain, a domain whose site is elsewhere. Optionally attach it to an account so the customer sees it in their Zone Editor.

### Records the panel manages

DKIM records (`default._domainkey`) are kept by the mail system. The other template records (A, MX, SPF, DMARC, `www`, `mail`, `panel`, `webmail`, `autoconfig`, `autodiscover`) are created once and are then the customer's to change. Changing the account's IP updates the records that point to the old IP.

## Zone templates

**DNS Functions › Edit Zone Templates.** New account zones are created from the **default template**:

| Template | Records |
|---|---|
| `standard` | NS, A/AAAA, `www`, `mail`, MX, SPF and DMARC; `panel`, `webmail`, `autoconfig` and `autodiscover` point at the server IP (served by this server, so they keep working when the domain's site is hosted elsewhere). |
| `simple` | NS, A/AAAA and `www`: for sites whose mail is hosted elsewhere. |

Edit a template or create your own; choose which one is the default. Variables:

| Variable | Value |
|---|---|
| `%domain%` | The zone's domain |
| `%ip%`, `%ipv6%` | The account's IPv4 and IPv6 |
| `%ns1%` … `%ns4%` | The nameservers (of the account's reseller, if it has its own) |
| `%hostname%` | The server's hostname |

A line whose variable has no value (`%ns3%` on a server with two nameservers) is skipped.

## The hostname

The server's hostname needs an A record wherever its domain's DNS is. When that domain's zone is on this server, **DNS Zone Manager** shows it and **Set A record** points it at the main IP; otherwise add it at the DNS provider of the hostname's domain.

## Cloudflare

Customers can publish their zones to Cloudflare from their panel, with the proxy toggle per record. See [Cloudflare](/user/domains/#cloudflare).
