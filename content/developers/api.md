---
title: Using the API
description: Authentication, tokens, errors, jobs and conventions of the KyPanel API.
---

Every screen of KyPanel uses the same JSON API, so anything you can do in the panel you can do with a script. The [API reference](/api/) lists every operation; each server also serves its own description at `/api/v1/openapi.json` (OpenAPI 3.1).

## Base URL

```text
https://SERVER:7711/api/v1/
```

Use the server's hostname: its certificate is valid for it.

## API tokens

Scripts authenticate with an API token in the `Authorization` header:

```sh
curl -H "Authorization: Bearer kpt_…" https://server1.example.com:7711/api/v1/accounts
```

| Token | Created in | Reaches |
|---|---|---|
| Admin token | Ky Admin › Development › Manage API Tokens | Every account and the admin endpoints allowed by its scopes |
| Account token | The customer panel › Manage API Tokens | Only that account's `/api/v1/accounts/{id}/…` endpoints |

Each token has:

- **scopes** (at least one): full access, read-only (GET only), or chosen areas, each read or read-and-change:
  - admin: `accounts`, `packages`, `resellers`, `security`, `backups`, `imports`, `webhooks`, `server`;
  - account: `domains`, `dns`, `email`, `files`, `mysql`, `ssl`, `ssh`, `cron`, `logs`, `account`, `webhooks`, `events`.

  The [API reference](/api/) shows each operation's area. An endpoint outside every area answers only full-access tokens;
- **allowed IPs:** optional addresses or ranges it may be used from;
- **expiry:** 30 days, 90 days, a year, a date, or never.

The secret is shown once. Every change made with a token is in the audit log with the token's name. `GET /api/v1/token` tells a client whose token it holds.

What tokens can never do: manage tokens, change two-factor settings, open "log in as" sessions, open phpMyAdmin or webmail sessions, or change the system root password. Account tokens can't change the account's password; admin tokens can (billing systems do).

## Sessions

The panel's own screens use cookies: `kp_admin` for Ky Admin, `kp_user` for the customer panel. Every request that isn't GET must also send `X-CSRF-Token` with the token received at sign-in (`GET /api/v1/auth/session` or `/api/v1/user/auth/session`). Prefer tokens for scripts.

## Requests and responses

- JSON in, JSON out (`Content-Type: application/json`). Unknown fields in a request are refused.
- Times are Unix time in **milliseconds**.
- IDs are 26-character ULIDs (`01JB4X7Q9M2K8T5R3W6Y1Z0A2B`).
- Lists answer `{"items": [...]}`. The account list pages with `?page=` and `?per_page=` and returns `total`.

## Errors

Errors use `application/problem+json`:

```json
{
  "type": "https://kypanel.com/problems/validation",
  "title": "Check the highlighted fields",
  "status": 400,
  "detail": "",
  "fields": { "username": "Already taken" }
}
```

| Status | |
|---|---|
| 400 / 422 | Invalid input; `fields` names the field. |
| 401 | No session, or an invalid or expired token. |
| 402 | The server has no valid license: the panel is read-only. See [license](/license/#read-only-mode). |
| 403 | Not allowed: privileges, token scope, a feature turned off for the account, or a token on a forbidden endpoint. |
| 404 | Not found, or not yours. |
| 409 | Conflict: a limit reached, the object is busy, it already exists. |
| 429 | Too many requests (sign-in and some heavy operations). |

The last part of `type` is a stable code you can match on (`validation`, `limit-reached`, `feature-disabled`, `token-scope`…).

## Background jobs

Operations that take time (creating an account, adding a domain, AutoSSL, backups, deploys) answer `202 Accepted` with a `job_id`:

```json
{ "account": { "id": "01JB…", "username": "acme", … }, "job_id": "01JB…" }
```

Follow it with `GET /api/v1/jobs/{id}` (status `queued`, `running`, `succeeded`, `failed`, `cancelled`; `?log_after=` returns new log lines), or subscribe to the `job.completed` and `job.failed` [webhooks](/developers/webhooks/). `ky … --wait` does the polling for you.

## Who can call what

Each operation in the [reference](/api/) says who may call it:

- **root:** the server administrator;
- **reseller (ACL: …):** a reseller with one of those privileges, only for its own accounts;
- **customer:** the account's own session or token, on its own `{id}`;
- the **token area** a token needs, and the **feature** that must be on for the account.

## Example: create an account and wait

```sh
TOKEN=kpt_…
API=https://server1.example.com:7711/api/v1

PKG=$(curl -s -H "Authorization: Bearer $TOKEN" $API/packages | jq -r '.items[] | select(.name=="Starter") | .id')
JOB=$(curl -s -H "Authorization: Bearer $TOKEN" -H "Content-Type: application/json" \
  -d "{\"username\":\"acme\",\"domain\":\"acme.com\",\"password\":\"$(openssl rand -base64 18)\",\"contact_email\":\"owner@acme.com\",\"package_id\":\"$PKG\"}" \
  $API/accounts | jq -r .job_id)
while :; do
  S=$(curl -s -H "Authorization: Bearer $TOKEN" $API/jobs/$JOB | jq -r .job.status)
  case $S in queued|leased|running) sleep 2 ;; *) echo "$S"; break ;; esac
done
```

Or with the [CLI](/developers/cli/): `ky accounts create acme acme.com --package Starter --email owner@acme.com --wait`.
