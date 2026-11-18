---
title: Server configuration
description: Basic setup, settings, services, processes, IPs and the server's health.
---

## Basic Setup

**Server Configuration › Basic Setup**:

- **Contact email** for alerts, and the server's **nameservers**;
- defaults for new accounts;
- **root password** of the operating system (**Change Root Password**);
- **Update Preferences** (KyPanel's channel and automatic updates) and **System Update** (the operating system). See [updates](/updates/).

## Server Settings and Tweak Settings

**Server Settings** holds the web and mail server options: web server connections, request timeout, TLS versions and the WordPress login limit; the largest message, an extra submission port, and an outgoing **relay host** (smarthost with port, username and password) when the server must send mail through your provider. **Tweak Settings** lists every setting of the panel, searchable, with its default; **Reset** goes back to it. Examples: session lengths, upload limits, brute-force thresholds, mail delivery report retention, webhook timeouts, AutoSSL options.

**Apps › Private networks Git repositories may use:** customers' apps can deploy over SSH from a Git server on a private network (10.x, 172.16–31.x, 192.168.x, fc00::/7, 100.64/10) only when its address or range is listed. Empty by default; public Git servers always work.

## Hostname and network

**Networking Setup**:

- **Change Hostname:** a full domain name with an A record pointing to the server. The panel's certificate is requested again for the new name, and the accounts' `panel.` and `webmail.` names redirect to it.
- **Resolver Configuration:** the DNS resolvers the server uses for lookups.
- **Firewall:** open ports. See [security](/admin/security/#firewall).

## IP addresses

**IP Functions**: add IPs (one or a range), **bind** them to the network interface, mark each as **shared** or **dedicated**, and choose the main shared IP. **Show IP Address Usage** lists which accounts use each IP. An account's IP is changed in List Accounts › **Change IP Address**.

## Services

**Service Configuration › Service Manager** lists the services (web, PHP, mail, DNS, databases, FTP, panel) with their state. Start, stop, restart or reload them, and choose which ones the **monitor** watches: a monitored service that stops is restarted and the admins are notified.

**Apache Configuration** shows the web server's settings and modules.

## Status and processes

- **Server Status › Server Information:** load, memory, swap, disks, uptime, kernel, SELinux, the panel's version.
- **Hardware & Disks:** CPU, memory, disks with usage and inodes, disk I/O.
- **System Health › Process Manager:** running processes; kill one, or every process of a user.
- **Account Information › Resource Usage:** each account's CPU, memory, I/O and processes over the last day, with the times it hit its limits.

## Reboot

**System Reboot:** **graceful** (services stop cleanly) or **forceful**. The server never reboots on its own, even after updates; the dashboard tells you when a reboot is pending.

## Databases

**Database Services**: the MariaDB server's status, its running queries (**Show Database Processes**, with kill), and **root access** repair. MariaDB's root uses socket authentication: only the system's root logs in, so there's no root password to leak.

## Branding

**Service Configuration › Branding:** logo, colors and the panel's name on the sign-in pages and both panels. Resellers can have their own. See [resellers](/admin/resellers/#branding).

The server's error pages follow the branding too: when the web server answers an error itself (a blocked IP, the WordPress login limit, a site that is down, an upload too large, a missing static file), visitors see a page with the logo, the accent color and **Hosted by** the public contact's company. Errors a site answers itself (a WordPress 404, an API's JSON) pass through unchanged, and a domain's own page in [Error Pages](/user/advanced/#error-pages) comes first. Saving the branding updates the pages on every site in a few minutes.

## GitHub

**Server Configuration › GitHub** creates the server's GitHub App in two clicks. Customers then connect their GitHub accounts or organizations to deploy repositories with [Apps](/user/apps/), without keys or tokens.

GitHub sends push events to `https://HOSTNAME:7711/hooks/github`. **Change Hostname** points the app's webhook at the new name and sends again the pushes GitHub couldn't deliver; when the address doesn't match (a hostname changed outside the panel), the page shows it and **Update webhook URL** fixes it. The app's **Callback URL** can only be changed on GitHub: set it to `https://HOSTNAME:7711/api/v1/github/callback` so customers can connect new GitHub accounts.

## License

**Server Configuration › License:** status, trial end, the licensed IP, **Check now**. See [license](/license/).
