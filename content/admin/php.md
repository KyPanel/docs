---
title: PHP
description: PHP versions, extensions and settings, per server and per domain.
---

PHP 8.1, 8.2, 8.3, 8.4 and 8.5 are installed side by side (on Ubuntu, the versions the repository has for the release). Each account runs its own PHP-FPM pool per version it uses, as its own Linux user.

## PHP Versions

**Software › PHP Versions** lists the installed versions and sets the **server default**, used by every domain that doesn't choose one.

## MultiPHP Manager

**Software › MultiPHP Manager** lists every domain with its PHP version. Select domains and set a version for all of them, or *inherit* to follow the default. Customers choose per domain in their own [MultiPHP Manager](/user/software/).

## PHP Extensions

**Software › PHP Extensions**: per version, install or remove any extension package the repository offers for it (redis, imagick, intl, and so on). The version's pools restart afterwards.

## MultiPHP INI Editor

**Software › MultiPHP INI Editor** sets a version's global `php.ini` values (memory limit, upload size, execution time, timezone, disabled functions…). Customers can override a safe subset per domain in their own INI editor; the list of what they may change and the allowed ranges are fixed by KyPanel.

## Pools and workers

The number of PHP workers per account comes from the package (**PHP workers per account**) and can be overridden per account. Pools start workers on demand, so an idle site costs no processes; each worker counts against the account's memory limit. CPU, memory and process limits apply to PHP like to everything else the account runs.
