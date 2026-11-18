---
title: Databases
description: MariaDB databases and users, remote access and phpMyAdmin.
---

Databases and their users always start with the account's username and an underscore: on account `acme`, a database `shop` is `acme_shop`.

## Database Wizard

**Databases › Database Wizard** creates a database, a user and the user's privileges in one step, the quickest way to set up WordPress or another app.

## Manage My Databases

- **Databases:** create, see the size, delete.
- **Users:** create (with a generated or your own password), change the password, rename, delete.
- **Privileges:** give a user ALL PRIVILEGES on a database, or pick them one by one; remove a user from a database.

Apps connect to `localhost` (or `127.0.0.1`) on port 3306 with the full names (`acme_shop`, `acme_shopuser`). With a [database node](/admin/nodes/#database-nodes) the page shows the node's address instead.

## Remote Database Access

By default only the server itself connects. Add the IP addresses (or `%` wildcards, like `203.0.113.%`) of other machines that may log in as your database users, for example your office for a desktop SQL client.

> [!NOTE]
> The database server listens only on the server itself unless the hosting provider opens it to the network (MariaDB's `bind-address` and port 3306 in the firewall). The page says so when it's closed. Until then, connect through an SSH tunnel: `ssh -L 3306:127.0.0.1:3306 USERNAME@SERVER`.

## phpMyAdmin

**Databases › phpMyAdmin** opens phpMyAdmin already signed in, with the account's databases only. It runs on its own address (port 7713) and its sign-in link works once.
