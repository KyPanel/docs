---
title: Software
description: PHP version and PHP settings per domain.
---

## MultiPHP Manager

Choose the PHP version of each domain (8.1 to 8.5, whatever the server has), or **inherit** the server's default. The change takes effect within seconds; check your site's requirements first.

## MultiPHP INI Editor

Change PHP settings for one domain. **Basic mode** shows the usual ones:

| Setting | Example |
|---|---|
| `memory_limit` | `256M` |
| `upload_max_filesize`, `post_max_size` | `64M` |
| `max_execution_time`, `max_input_time` | `120` |
| `max_input_vars` | `3000` |
| `display_errors` | Off on live sites |
| `date.timezone` | `America/New_York` |

**Editor mode** shows every setting you're allowed to change. Values are checked and capped by the hosting provider's limits; an empty value means the server's default. Settings that would weaken the server's security can't be changed.

## Apps

Git-deployed apps (Laravel, PHP, Node.js, Python, static sites) are in [Apps](/user/apps/).
