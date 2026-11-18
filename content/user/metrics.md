---
title: Metrics
description: Visitors, errors, bandwidth, raw logs and resource usage.
---

## Visitors

The latest requests to a domain, newest first: time, visitor IP, page, status, size, referrer and browser.

## Errors

The latest errors from the web server and from PHP, newest first. Use them to find out why a page fails. PHP also writes its errors to `~/logs/php-error.log`.

## Bandwidth

Traffic per day for the last weeks and per domain and protocol (web, mail, FTP), against the monthly limit. Days are in the server's time zone; data is collected every 10 minutes.

## Raw Access

Download each domain's web access log (combined format, real visitor IPs) to analyze traffic with your own tools. Settings: **Archive logs** in your home folder when they rotate, and **Remove old archives** at the end of each month.

## Resource Usage

What the account used over the last day: CPU, memory, disk I/O and processes, against the package's limits, and **faults**: the times a limit was reached. Frequent faults mean the site needs a bigger package or optimization (caching, fewer plugins).
