---
title: Billing integration
description: Connect a billing system: provisioning, suspension, plan changes, usage and sign-on.
---

## Ready-made modules

These do everything on this page for you. They're free and open source:

| Billing system | Module |
|---|---|
| WHMCS | [KyPanel/whmcs-module](https://github.com/KyPanel/whmcs-module) |
| Blesta | [KyPanel/blesta-module](https://github.com/KyPanel/blesta-module) |
| FOSSBilling | [KyPanel/fossbilling-module](https://github.com/KyPanel/fossbilling-module) |
| Paymenter | [KyPanel/paymenter-module](https://github.com/KyPanel/paymenter-module) |
| Clientexec | [KyPanel/clientexec-module](https://github.com/KyPanel/clientexec-module) |

Each one has the install steps in its README. For anything else, the API below is all you need.

## Your own integration

A billing system needs a handful of calls. Create an **admin API token** for it (Ky Admin › Development › Manage API Tokens), with the `accounts` and `packages` areas, read and change, and limit it to the billing server's IP.

## The lifecycle

| Billing action | API | CLI |
|---|---|---|
| Create | `POST /api/v1/accounts` `{username, domain, password, contact_email, package_id}` → `202 {account, job_id}` | `ky accounts create` |
| Suspend | `POST /api/v1/accounts/{id}/suspend` `{reason}` | `ky accounts suspend` |
| Unsuspend | `POST /api/v1/accounts/{id}/unsuspend` | `ky accounts unsuspend` |
| Terminate | `DELETE /api/v1/accounts/{id}?confirm=USERNAME` | `ky accounts terminate --yes` |
| Change package | `PUT /api/v1/accounts/{id}/package` `{package_id}` | `ky accounts package` |
| Change password | `POST /api/v1/accounts/{id}/password` `{new_password}` | `ky accounts password` |
| Usage | `GET /api/v1/accounts/{id}` (disk, bandwidth, counts) and `GET /api/v1/accounts/{id}/usage` | `ky accounts show` |
| Sign the customer in | `POST /api/v1/accounts/{id}/sso` → `{url}` | `ky accounts sso` |
| List packages | `GET /api/v1/packages` | `ky packages list` |

Store the account's `id` (returned on creation) on your side; usernames and main domains can change.

Before creating, `GET /api/v1/accounts-check?username=…&domain=…` tells you whether the username and domain are free, so you can suggest another name before the order goes through.

## Waiting for provisioning

Creation, suspension, termination and package changes are background jobs. Either:

- poll `GET /api/v1/jobs/{job_id}` until it's `succeeded` or `failed`; or
- add a [server webhook](/developers/webhooks/) for `job.completed` and `job.failed` and match `data.job_id`.

## Keeping in sync

Changes made by admins in the panel (a suspension, a plan change, a termination) reach your system through webhooks: `account.suspended`, `account.unsuspended`, `account.package_changed`, `account.terminated`, `account.password_changed`.

## Errors to handle

| Problem | Meaning |
|---|---|
| `409 limit-reached` | A reseller's account limit, or a package limit. |
| `400 validation` with `fields.username` / `fields.domain` | Taken, reserved or invalid. |
| `402 license-readonly` | The server's KyPanel license lapsed: provisioning is paused. |
| `403 token-scope` / `403 token-ip` | The token can't do this, or is used from another IP. |

## Moving existing customers

To bring accounts from another server into KyPanel and keep billing them, use the [import tools](/admin/import/), then look the imported accounts up by username (`GET /api/v1/accounts?q=USERNAME`) to store their ids.
