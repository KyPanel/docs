---
title: Requirements
description: Operating systems, hardware and network a KyPanel server needs.
---

## Operating system

A **fresh, minimal** installation of one of:

| Distribution | Versions |
|---|---|
| AlmaLinux | 8, 9, 10 |
| Rocky Linux | 8, 9, 10 |
| CloudLinux | 8, 9, 10 |
| Ubuntu LTS | 24.04, 26.04 |

64-bit x86 (`x86_64`) only. The installer refuses other systems; `--force` skips the check at your own risk.

Install on a server with nothing else running: KyPanel installs and configures its own web, mail, DNS, database and FTP services and will overwrite their configuration.

## Hardware

| Role | Minimum | Recommended |
|---|---|---|
| Panel server (`all` or `web`) | 2 GB RAM, 1 CPU, 20 GB disk | 4 GB RAM or more, 2+ CPUs, SSD |
| Mail or database node | 1 GB RAM (a 1 GB plan reports about 750 MB; that is fine) | 2 GB RAM |

Plan disk for the accounts' files, mail and databases, plus local backups if you keep them on the same disk.

## System

- **cgroups v2.** Used for per-account CPU, memory and I/O limits. AlmaLinux/Rocky/CloudLinux 8 boot with v1: the installer switches the kernel to v2 and asks for a reboot, then you run it again.
- **SELinux** (AlmaLinux, Rocky, CloudLinux) stays **enforcing**. KyPanel ships its own policy modules; don't turn SELinux off.
- **Root access** over SSH.

## Network

- A **public IPv4** address. The license is bound to the server's main IPv4. IPv6 works alongside it.
- A **hostname** that's a full domain name (for example `server1.example.com`) with an A record pointing to the server. The panel's certificate is issued for it.
- **Reverse DNS (PTR)** for the IP set to the hostname, at your provider. Mail servers check it.
- **Port 25 outbound** open. Some cloud providers block it until you ask.

The ports the installer opens are listed in [ports](/reference/ports/). If your provider has its own firewall in front of the server, open them there too.

## DNS for your customers

To host DNS for customer domains, you need two nameserver names (for example `ns1.example.com` and `ns2.example.com`) registered at your domain registrar with glue records pointing to the server. You set them in the setup wizard. Without nameservers, accounts can still be created; their DNS stays with whoever hosts it now.
