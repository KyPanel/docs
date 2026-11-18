---
title: Command line (ky)
description: Manage KyPanel servers from your terminal with ky.
---

`ky` talks to the API of one or more KyPanel servers with API tokens. It runs on Linux, macOS and Windows, and is already installed on every KyPanel server.

## Install

Linux and macOS:

```sh
curl -fsSL https://updates.kypanel.com/cli/install.sh | sh
```

It installs to `/usr/local/bin` as root, or `~/.local/bin` otherwise. Windows and manual downloads: `https://updates.kypanel.com/cli/VERSION/` has `ky_linux_amd64`, `ky_linux_arm64`, `ky_darwin_amd64`, `ky_darwin_arm64`, `ky_windows_amd64.exe` and `ky_windows_arm64.exe`, with a `SHA256SUMS` file signed with the KyPanel package key (`SHA256SUMS.asc`). The current version is in `https://updates.kypanel.com/cli/latest.txt`.

## Sign in

Create an API token (admin or account, see [the API](/developers/api/#api-tokens)) and:

```sh
ky login --server server1.example.com --token kpt_…
```

The port defaults to 7711. Add `--profile NAME` to keep several servers or tokens; `ky profiles` lists them (the current one has a `*`), `--profile NAME` on any command picks one, `ky logout` removes one. Profiles live in `~/.config/ky/config.json` (mode 600).

With an **account token**, commands work on that account and you leave the account name out.

Environment variables override the profile: `KY_SERVER`, `KY_TOKEN`, `KY_PROFILE`, `KY_INSECURE=1` (accept a self-signed certificate; also `--insecure`), `KY_CONFIG`.

## Commands

`ACCOUNT` is a username, a main domain or an id.

| Command | |
|---|---|
| `ky accounts list [--search TEXT] [--status S] [--package NAME]` | List accounts |
| `ky accounts show ACCOUNT` | Everything about an account (JSON) |
| `ky accounts create USER DOMAIN --package NAME [--email E] [--password P] [--wait]` | Create; a password is generated and printed if you give none |
| `ky accounts suspend ACCOUNT [--reason TEXT]` / `unsuspend ACCOUNT` | |
| `ky accounts terminate ACCOUNT --yes [--keep-home]` | |
| `ky accounts package ACCOUNT PACKAGE` | Change the plan |
| `ky accounts password ACCOUNT [--password P]` | |
| `ky accounts sso ACCOUNT [--admin] [--goto /user/email]` | Print a one-time sign-in link |
| `ky packages list` | |
| `ky domains list ACCOUNT` | |
| `ky domains add ACCOUNT DOMAIN [--alias] [--docroot DIR]` | Addon domain or alias |
| `ky domains add ACCOUNT --sub LABEL --parent DOMAIN` | Subdomain |
| `ky domains remove ACCOUNT DOMAIN --yes [--remove-files]` | |
| `ky email list ACCOUNT` | Mailboxes |
| `ky email create ACCOUNT ADDRESS [--password P] [--quota MB]` | |
| `ky email password ACCOUNT ADDRESS [--password P]` / `delete ACCOUNT ADDRESS --yes` | |
| `ky dns records ACCOUNT ZONE` | |
| `ky dns add ACCOUNT ZONE NAME TYPE CONTENT [--ttl S] [--priority N]` / `delete ACCOUNT ZONE RECORD-ID` | |
| `ky databases list ACCOUNT` / `create ACCOUNT NAME [--user NAME --password P]` | |
| `ky jobs show ID` / `wait ID` | |
| `ky webhooks list` / `add URL [--events a.b,c.*]` / `test ID` / `delete ID` | Server webhooks (an account's, with an account token) |
| `ky events [--after SEQ] [--type a,b] [--follow]` | The event feed |
| `ky server status` / `update [--check]` | |
| `ky whoami` | The token's owner and scopes |

Commands that start a background job accept `--wait`: the job's log is printed as it runs and `ky` exits non-zero if it fails. `--json` prints the API's raw JSON instead of a table.

## Any API operation

Every operation of the [API reference](/api/) is reachable by its operation id:

```sh
ky ops dns                     # find operations by id, path or summary
ky call setAccountShell id=acme shell=jailed
ky call listAccounts q=acme status=suspended
ky call createRedirect id=acme -d '{"domain_id":"","path":"/old","target_url":"https://acme.com/new","code":301}'
```

Arguments are `name=value`: path parameters fill the URL (`id=` accepts a username), query parameters go in the query string, and anything else is a body field, typed from the schema (numbers, booleans, JSON arrays and objects; `a,b` for a list). `-d` sends a JSON body as is. `ky api METHOD PATH [-d JSON]` sends a raw request.

## On the server

On a KyPanel server, `ky` is in the path and also runs the server's local commands as root, without a token:

```sh
ky admin reset-root     # new password for the panel's root user
```

## Scripting

`ky` exits with `0` on success, `1` on a usage or connection error and `2` when the API refused the request (the problem is printed on stderr). Combine `--json` with `jq`:

```sh
for u in $(ky accounts list --status suspended --json | jq -r '.items[].username'); do
  ky accounts terminate "$u" --yes
done
```
