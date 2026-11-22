---
title: FAQ
description: Short answers to common questions.
---

## Licensing

**How much does it cost?** $49 a month or $490 a year per server, with unlimited accounts. Mail and database nodes are free. See [license](/license/).

**Is there a trial?** 30 days, fully working, from the first install on an IP. No card needed.

**What happens if I stop paying?** The panel becomes read-only. Sites, mail and DNS keep running. See [read-only mode](/license/#read-only-mode).

**Can I move my license to another server?** Yes: open a ticket and we move it to the new IP.

## Installing

**Can I install on a server that already hosts sites?** No. Use a fresh server and [import](/admin/import/) the accounts.

**Which distribution should I choose?** Any supported one works the same, and every release is tested on all of them. With no preference, AlmaLinux 10 gets updates from its vendor the longest.

**Does it support ARM servers?** Not yet: x86_64 only. The [ky CLI](/developers/cli/) runs on ARM.

**Can I uninstall it?** KyPanel takes over the server's services; to remove it, reinstall the operating system.

## Hosting

**Which PHP versions?** 8.1 to 8.5, chosen per domain.

**Can customers run Node.js or Python?** Yes, with [Apps](/user/apps/): Git deploys, a process per app, a port behind the web server, and optional Redis.

**Does it support WordPress?** Yes, like any PHP application: create a database with the Database Wizard and upload WordPress, or deploy it with Apps.

**Can I use my own SMTP relay?** Yes: Server Configuration › Server Settings › relay host.

**Can mail run on another server?** Yes, with a [mail node](/admin/nodes/).

**Can my support staff have their own logins?** Not yet. Ky Admin has one administrator login, with two-factor
authentication; scripts and billing systems use [API tokens](/developers/), which can be limited to some areas.
Resellers have their own logins for their own accounts.

**Does it work with CloudLinux and Imunify360?** KyPanel installs on CloudLinux 8, 9 and 10 and, when it's there, sets
each account's limits through CloudLinux's LVE; the CloudLinux license stays yours. Imunify360 isn't integrated.

**Is there a DNS cluster?** Zones are served by this server's DNS; customers can also publish them to [Cloudflare](/user/domains/#cloudflare).

## Moving

**Can I move accounts from cPanel, Plesk, DirectAdmin or other panels?** Yes: convert the account backups in Convert Backup; they're restored right after (or later, in Backup Import). Mail, databases, DNS and the passwords in formats KyPanel uses come along. See [import tools](/admin/import/).

**What do my customers notice after the move?** Their sites, mail and databases keep working with the same passwords
when the old panel stored them in a format KyPanel uses; any other login gets a new password, listed in the restore's
notes. They sign in at `panel.<their domain>` (or the server's address) and read webmail at `webmail.<their domain>`.
Bookmarks to the old panel's address stop working. Lower the DNS TTL a day before moving so the switch is quick.

**How do I move to a bigger server?** Install KyPanel on the new server and run the [KyPanel Server Transfer](/admin/server-transfer/) from there: settings, accounts, Cloudflare and GitHub connections and API tokens come along, and the DNS records get the new addresses. The old server keeps running until you switch.

**Can I bring mail and databases back from nodes into one server?** Yes, by moving to a new server without nodes: the [KyPanel Server Transfer](/admin/server-transfer/) puts every account's mail and databases on it.

## Automation

**Is there an API?** Every screen uses the [API](/developers/api/), documented in the [reference](/api/). There's also a [command line](/developers/cli/), [webhooks](/developers/webhooks/) and [single sign-on](/developers/sso/).
