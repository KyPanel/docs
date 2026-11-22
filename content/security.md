---
title: Security
description: How to report a vulnerability in KyPanel and how security fixes are published.
---

## Reporting a vulnerability

Write to **support@kypanel.com** with **Security** at the start of the subject. The message becomes a private ticket that only KyPanel staff can read. Include the version (**Server Configuration › Update Preferences**), what an attacker can do and the steps to reproduce it.

Every report gets a reply. Please don't publish the details before a fixed release is out; you'll be credited in the advisory if you wish.

The same contact is published at `https://kypanel.com/.well-known/security.txt`.

## How fixes are published

A security fix is released as the next patch version on both channels right away, without waiting for the usual time on beta. Servers with automatic updates install it on their next check. In **Server Configuration › Update Preferences**, a release that fixes a security problem is marked as such.

Fixes to the system packages KyPanel installs (PHP, MariaDB, nginx, Dovecot and others) arrive through the [security fast lane](/updates/#security-fast-lane), checked every 15 minutes.

## Advisories

Each security fix is listed under **Security** in the [changelog](/changelog/) with the versions it affects and the version that fixes it. Advisories are numbered by year (2026-0001).

No advisories have been published yet.
