---
title: Installation
description: Install KyPanel with one command.
---

## Install

Log in to the new server as root and run:

```sh
curl -fsSL https://updates.kypanel.com/install.sh | bash
```

The installer downloads the current release, checks its signature and checksum, updates the operating system, installs and configures every service, opens the firewall and prints the first login:

```text
[kypanel] ready: https://server1.example.com:7711  (version 0.2.0)

  First panel login
  user:     root
  password: 9f3Kq…
  Save this password now: it will not be shown again.
```

It takes 10 to 25 minutes, depending on the server and the mirrors. A first install on AlmaLinux/Rocky/CloudLinux 8 stops once to reboot into cgroups v2; reboot and run the same command again.

## Options

Pass options after `bash -s --`:

```sh
curl -fsSL https://updates.kypanel.com/install.sh | bash -s -- --hostname server1.example.com
```

| Option | Meaning |
|---|---|
| `--hostname NAME` | The server's hostname (full domain name). Default: the current hostname. |
| `--channel NAME` | Release channel: `stable` (default) or `beta`. See [updates](/updates/). |
| `--role ROLE` | What this server does: `all` (default), `web`, `mail` or `database`. See below. |
| `--node-host NAME` | For a node: the address the panel reaches it at (default: the hostname). |
| `--force` | Install on a system that isn't on the supported list. |

## Roles

| Role | Runs |
|---|---|
| `all` | The panel and every service: web, PHP, mail, DNS, databases, FTP. The usual choice. |
| `web` | The panel, the sites, DNS and FTP. Mailboxes and databases live on nodes; this server only sends mail. |
| `mail` | A node with mailboxes only (Exim, Dovecot, Rspamd), managed by a panel server. |
| `database` | A node with MariaDB only, managed by a panel server. |

A node prints a **pairing code** at the end of its install. Paste it in Ky Admin › Server Configuration › Link Server Nodes. See [nodes](/admin/nodes/).

The role is kept in `/etc/kypanel/role`. Changing it later needs a reinstall.

## Run it again

The installer is idempotent: running it again updates binaries and configuration without touching accounts, mail or databases. Updates use the same installer, so you rarely need to.

## What gets installed

| Service | Software |
|---|---|
| Web | nginx in front of Apache, PHP-FPM per account |
| PHP | 8.1 to 8.5 side by side (Remi on AlmaLinux/Rocky/CloudLinux, the ondrej PPA on Ubuntu) |
| Mail | Exim, Dovecot 2.4 with Sieve, Rspamd, Roundcube webmail |
| DNS | PowerDNS (authoritative) |
| Databases | MariaDB 11.8 LTS, phpMyAdmin |
| FTP | Pure-FTPd with TLS |
| Firewall | firewalld (AlmaLinux/Rocky/CloudLinux) or ufw (Ubuntu) |

The panel itself lives in `/usr/local/kypanel`, its data in `/var/lib/kypanel` and its configuration in `/etc/kypanel`. See [paths](/reference/paths/).

## Lost the first password?

On the server, as root:

```sh
ky admin reset-root
```

It prints a new password for the panel's `root` user and asks you to change it at the next login.
