---
title: Single sign-on
description: Send customers into their panel from your own site or billing system, without a password.
---

A billing system or client area can sign a customer into their KyPanel panel with one click: it asks the API for a **one-time link** and sends the customer's browser to it.

## Create a link

```http
POST /api/v1/accounts/{id}/sso
Authorization: Bearer kpt_…
Content-Type: application/json

{ "panel": "user", "goto": "/user/email/accounts" }
```

```json
{ "url": "https://server1.example.com:7711/api/v1/user/auth/sso?token=E5mkgYZfAnF1…", "expires_in": 60 }
```

| Field | |
|---|---|
| `panel` | `user` (the customer panel, default) or `admin` (Ky Admin, for an account that is a reseller). |
| `goto` | Optional page to open, inside the chosen panel: `/user/email/accounts`, `/user/files/manager`, `/admin/accounts`… Default: the home page. |

The link:

- works **once** and only for **60 seconds**: create it when the customer clicks, then redirect;
- opens a normal session for the account (the customer is signed in as themselves);
- is refused for a suspended account (`409 suspended`).

Who can create links: admin tokens with access to accounts, root, and resellers with the "log in to customer panels" privilege for their own accounts. Account tokens can't.

With the CLI:

```sh
ky accounts sso acme --goto /user/email/accounts
```

## Example (PHP)

```php
function kypanel_sso(string $accountId, string $goto = ''): string {
    $ch = curl_init("https://server1.example.com:7711/api/v1/accounts/$accountId/sso");
    curl_setopt_array($ch, [
        CURLOPT_POST => true,
        CURLOPT_RETURNTRANSFER => true,
        CURLOPT_HTTPHEADER => ['Authorization: Bearer ' . getenv('KYPANEL_TOKEN'), 'Content-Type: application/json'],
        CURLOPT_POSTFIELDS => json_encode(['goto' => $goto]),
    ]);
    $res = json_decode(curl_exec($ch), true);
    return $res['url'];
}

header('Location: ' . kypanel_sso($account['kypanel_id'], '/user/'));
```

## Other one-time links

| Link | Endpoint |
|---|---|
| Webmail, signed in to a mailbox | `POST /api/v1/accounts/{id}/email/accounts/{mid}/webmail` (sessions only) |
| phpMyAdmin with the account's databases | `POST /api/v1/accounts/{id}/phpmyadmin` (sessions only) |
| Admin's "log in as" | `POST /api/v1/accounts/{id}/login-as` (sessions only; marked as an admin session) |

These are for the panel's own buttons and can't be created with API tokens.
