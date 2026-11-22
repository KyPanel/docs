---
title: Updates
description: How KyPanel and the operating system stay up to date.
---

A KyPanel server keeps itself up to date in two independent ways: KyPanel's own releases and the operating system's security updates. Nodes do both on their own.

## KyPanel releases

Settings: **Server Configuration › Update Preferences**.

| Setting | |
|---|---|
| Channel | **Stable** (default) gets a release once it has run on beta. **Beta** gets each release first. |
| Automatic | On by default. The server checks every 6 hours and installs new releases. Off: you get a notification and install with **Update now**. |

Releases are numbered (0.2.0, 0.2.1); the same release, with the same package, moves from beta to stable. What's new in each one is in the [changelog](/changelog/) and shows in **Server Configuration › Update Preferences** before you install it. Which versions get fixes: [support policy](/support-policy/).

Every release is signed by KyPanel; the server checks the signature and the package checksum before anything runs. An update:

1. backs up the panel's binaries, its database and the service configurations;
2. runs the installer of the new release (binaries, SELinux policy, web, mail and DNS configuration, webmail and phpMyAdmin);
3. checks that the panel answers, and **rolls everything back** if it doesn't.

Sites and mail keep running during an update. The panel is unavailable for a few seconds while it restarts.

From the command line: `ky server update --check` shows whether an update is available and `ky server update` installs it. See [the CLI](/developers/cli/).

The channel and the automatic setting apply to the panel server and all of its nodes, so they always run the same version. Moving to a channel with an older release changes nothing until that channel catches up: a server never downgrades.

## Operating system updates

Every day around 04:00 server time KyPanel installs security updates. Turn it off in **Software › System Update** (on the server and its nodes at once).

| Source | What's installed |
|---|---|
| AlmaLinux, Rocky, CloudLinux, EPEL | Security fixes only |
| Ubuntu | The `-security` pocket |
| PHP, MariaDB, PowerDNS, Dovecot, Rspamd, nginx repositories | Every update of the pinned branch (patch releases) |

The update runs at the lowest CPU and disk priority, with a memory cap, so it never starves the sites. On a server with too little memory for the package manager the run fails with an error instead.

Afterwards the services still running an old binary or library are restarted (nginx, Apache, PHP-FPM, Exim, Dovecot, MariaDB, PowerDNS, Pure-FTPd, Rspamd, Valkey, app processes, cron, SSH). When a new kernel or system library needs a reboot, administrators are notified. **The server never reboots on its own**; reboot from **System Reboot** when it suits you.

**Update this server now** in **Software › System Update** starts a run immediately and shows its log.

Customers' own software (WordPress, plugins, an app's dependencies) is theirs to update.

## The KyPanel package mirror

PHP, nginx, MariaDB, PowerDNS, Dovecot and Rspamd don't come straight from their vendors: servers install them from
KyPanel's mirror at `updates.kypanel.com/repo`, which publishes the vendors' packages (with the vendors' own signatures)
as tested snapshots, signed by KyPanel. A new vendor version reaches your servers once KyPanel has installed it on
every supported distribution. The mirror follows the panel's channel: **beta** servers get a snapshot first, **stable** servers after it
passed.

If the mirror can't be reached during an install, the vendors' repositories are used instead.

### Security fast lane

When a fix can't wait for the next day, KyPanel publishes a **security advisory** on the mirror. Servers (and their
nodes) check for advisories every 15 minutes and install the update right away; the admins get a notification.
Turning off the operating system updates turns this off too.
