---
title: Isolation and security model
description: How accounts are kept apart and how the panel protects the server.
---

## Accounts

Each hosting account is a separate Linux user. Everything it runs runs as that user:

- **PHP:** a PHP-FPM pool per account (and per PHP version), started on demand, as the account's user. Sites can't read each other's files.
- **Files:** other accounts can't list or read a home folder; the web server only reaches what it serves.
- **Resource limits:** a cgroup (v2) per account caps CPU, memory, disk I/O, processes and concurrent web requests, as set by the package. A runaway site slows only itself; the times it hits a limit are recorded as **faults** (Resource Usage).
- **Disk quota:** filesystem quotas per user (files, mail and databases count).
- **Jailed shell:** accounts with a jailed shell (SSH, terminal, cron) see their own home and a minimal system, not other users or the server's configuration.
- **Databases:** names and users are prefixed with the username; the panel never lets an account touch another's prefix.
- **Mail:** sending limits per account and hour, with automatic holds on bulk sending.

## SELinux

On AlmaLinux, Rocky and CloudLinux, SELinux stays **enforcing**. KyPanel installs its own policy modules for what it adds (per-account PHP pools, the jailed shell, CGI, the browser terminal), so nothing needs to run unconfined. On Ubuntu, AppArmor's default profiles apply.

The system's crypto policy stays at its default. The one exception is the accounts' own code (PHP, apps, cron, the jailed shell): it may sign with SHA-1, as on Ubuntu, because some government services still require RSA-SHA1 signatures (Brazil's NFS-e, for example). The server's own services keep the policy.

## The panel

- **Two processes:** `kyd` (panels, API, jobs) runs as an unprivileged user; `kyd-agent` is the only part running as root and accepts only a fixed list of operations, with validated arguments, over a local socket.
- **Secrets** (API tokens, webhook and backup credentials, Cloudflare tokens) are stored encrypted with the server's master key; API tokens and passwords only as hashes.
- **Sessions:** HttpOnly, Secure, SameSite cookies, CSRF tokens on every change, idle and absolute timeouts, optional two-factor authentication, which can be required.
- **Brute force protection** on every login: panel, webmail, mail, FTP, SSH.
- **Separate origins:** webmail (7712) and phpMyAdmin (7713) run on their own ports and in their own PHP pools, so a bug in them can't reach the panel.
- **Audit log** of every change, with who made it and from where.

## Updates

Releases are signed (Ed25519) and verified before installing; packages are checked against their SHA-256. Updates roll back automatically if the panel doesn't come back. Operating system security updates install daily. See [updates](/updates/).
