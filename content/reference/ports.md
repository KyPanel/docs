---
title: Ports
description: Network ports a KyPanel server and its nodes use.
---

The installer opens these in the server's firewall (firewalld or ufw). If your provider has a firewall in front of the server, open them there too.

## Panel server

| Port | Protocol | Service |
|---|---|---|
| 22 | TCP | SSH (and SFTP). Can be closed or restricted from the panel. |
| 21 | TCP | FTP and FTPS (explicit TLS) |
| 30000–50000 | TCP | FTP passive data |
| 25 | TCP | SMTP: incoming mail from other servers |
| 465 | TCP | SMTP submission over TLS (mail clients) |
| 587 | TCP | SMTP submission with STARTTLS (mail clients) |
| 110, 995 | TCP | POP3, POP3 over TLS |
| 143, 993 | TCP | IMAP, IMAP over TLS |
| 53 | TCP, UDP | DNS (authoritative) |
| 80, 443 | TCP | Websites; also `panel.DOMAIN` and `webmail.DOMAIN` |
| 7711 | TCP | Ky Admin, the customer panel and the API |
| 7712 | TCP | Webmail |
| 7713 | TCP | phpMyAdmin (opened only through the panel's sign-in links) |

## Nodes

| Port | Node | Service |
|---|---|---|
| 7714 | all | The link to the panel (mutual TLS). Allowed only from the panel server's IP. |
| 22 | all | SSH, optional: the panel manages the node over 7714. |
| 25, 465, 587, 110, 995, 143, 993 | mail | Mail |
| 3306 | database | MariaDB, for the panel server's sites |

## Outgoing

The server needs to reach: `updates.kypanel.com` and `license.kypanel.com` (443), Let's Encrypt (443), the distribution's and vendors' package mirrors (443/80), other mail servers (25), DNS resolvers (53). Some cloud providers block outgoing port 25 until you ask them.

## Internal

Bound to `127.0.0.1` only, never exposed: Apache (behind nginx), PHP-FPM sockets, MariaDB (3306) on a server with role `all`, Rspamd, Valkey instances, the PowerDNS API, app processes (`$PORT`, 20000–29999).

The 20000–29999 range is kept for apps: each port answers only from its app's process and accepts connections only from nginx, the panel and the app's account. Other processes can't serve or reach ports in that range.
