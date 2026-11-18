---
title: Webhooks
description: Signed HTTP notifications when something changes on the server.
---

A webhook sends a `POST` with a JSON body to your URL whenever an event happens: an account created or suspended, a domain added, a mailbox created, a background job finished. Use them to keep a billing system, a CRM or your monitoring in sync without polling.

| Webhook | Created in | Receives |
|---|---|---|
| Server webhook | Ky Admin › Development › Webhooks | Events of every account, plus server events (packages, resellers) |
| Account webhook | Customer panel › Advanced › Webhooks | Events of that account only |

Both can be managed with the API (`/api/v1/webhooks`, `/api/v1/accounts/{id}/webhooks`) and the CLI (`ky webhooks`). Up to 20 per server or account.

## Add a webhook

1. **Payload URL:** `https://`. Account webhooks must point to a public address; server webhooks may also use `http://127.0.0.1…` for a relay on the server itself.
2. **Events:** all (including events added later), or a choice. Groups like `dns.*` work in the API.
3. Copy the **signing secret** (`whsec_…`). It's shown once; **Rotate secret** makes a new one.

**Send test event** delivers a `webhook.ping` right away. **Deliveries** lists every attempt with the HTTP status and the start of your response; **Redeliver** sends one again.

## The request

```http
POST /hooks/kypanel HTTP/1.1
Content-Type: application/json
User-Agent: KyPanel-Webhooks/1
X-KyPanel-Event: account.suspended
X-KyPanel-Delivery: 01JB4X7Q9M2K8T5R3W6Y1Z0A2B-42
X-KyPanel-Timestamp: 1792207155
X-KyPanel-Signature: sha256=f0e732d7fc92728c51a4043ac80b7a5672d41085d4125fa7ec5af2f654e29d33

{
  "id": "01JB4X7Q9M2K8T5R3W6Y1Z0A2B",
  "type": "account.suspended",
  "created_at": 1792207154981,
  "server": "server1.example.com",
  "account_id": "01JB2ZK0Q8R5V7W9X1Y3Z5A7C9",
  "data": {
    "account": { "id": "01JB2ZK0Q8R5V7W9X1Y3Z5A7C9", "username": "acme", "domain": "acme.com" },
    "object": "account",
    "object_id": "01JB2ZK0Q8R5V7W9X1Y3Z5A7C9",
    "details": { "reason": "unpaid", "locked": false },
    "actor": { "type": "api_token", "id": "01JB1…" },
    "via": "api"
  }
}
```

- `id` is the event's id: the same event delivered twice (a retry, a redelivery) has the same `id`. Use it to ignore duplicates.
- `data.details` depends on the event (the domain's name, the mailbox's address, the job's kind…).
- `job.completed` and `job.failed` carry `job_id`, `kind` (`account.create`, `domain.create`, `backup.account`, `app.deploy`…), the `account`, and `error` when it failed.
- Events fire when the panel accepts a change; a change that runs in the background is done when its `job.completed` arrives.

Answer with any **2xx** within 10 seconds. Do the work after answering, or in a queue.

## Verify the signature

The signature is HMAC-SHA256 of `TIMESTAMP + "." + RAW_BODY` with the secret, hex encoded, prefixed with `sha256=`. Check it on the raw body (before parsing JSON), and reject old timestamps (5 minutes) to stop replays.

**PHP**

```php
$body = file_get_contents('php://input');
$ts   = $_SERVER['HTTP_X_KYPANEL_TIMESTAMP'] ?? '';
$sig  = 'sha256=' . hash_hmac('sha256', $ts . '.' . $body, getenv('KYPANEL_WEBHOOK_SECRET'));
if (!hash_equals($sig, $_SERVER['HTTP_X_KYPANEL_SIGNATURE'] ?? '') || abs(time() - (int) $ts) > 300) {
    http_response_code(401);
    exit;
}
$event = json_decode($body, true);
```

**Node.js**

```js
import crypto from "node:crypto";

// raw: the request body as a Buffer (express.raw({ type: "application/json" }))
function verify(raw, headers, secret) {
  const ts = headers["x-kypanel-timestamp"];
  const want = "sha256=" + crypto.createHmac("sha256", secret).update(ts + "." + raw).digest("hex");
  const got = headers["x-kypanel-signature"] || "";
  return got.length === want.length && crypto.timingSafeEqual(Buffer.from(got), Buffer.from(want))
    && Math.abs(Date.now() / 1000 - Number(ts)) < 300;
}
```

**Python**

```python
import hashlib, hmac, time

def verify(raw: bytes, headers, secret: str) -> bool:
    ts = headers.get("X-KyPanel-Timestamp", "")
    want = "sha256=" + hmac.new(secret.encode(), ts.encode() + b"." + raw, hashlib.sha256).hexdigest()
    return hmac.compare_digest(want, headers.get("X-KyPanel-Signature", "")) and abs(time.time() - int(ts or 0)) < 300
```

**Go**

```go
func verify(raw []byte, h http.Header, secret string) bool {
	ts := h.Get("X-KyPanel-Timestamp")
	mac := hmac.New(sha256.New, []byte(secret))
	mac.Write([]byte(ts + "."))
	mac.Write(raw)
	want := "sha256=" + hex.EncodeToString(mac.Sum(nil))
	t, _ := strconv.ParseInt(ts, 10, 64)
	return hmac.Equal([]byte(want), []byte(h.Get("X-KyPanel-Signature"))) && math.Abs(float64(time.Now().Unix()-t)) < 300
}
```

## Retries

A delivery that times out or answers anything but 2xx is retried after 1 minute, 5 minutes, 30 minutes, 2 hours, 6 hours, 12 hours and 24 hours, then given up. After **50 failed attempts in a row** the webhook is switched off (with the reason shown); switching it back on resets the count. Redirects aren't followed.

## Events

| Event | When | Account webhooks |
|---|---|---|
| `account.created` | An account was created | yes |
| `account.suspended` | An account was suspended | yes |
| `account.unsuspended` | An account was unsuspended | yes |
| `account.terminated` | An account was terminated | — |
| `account.package_changed` | An account moved to another package | yes |
| `account.password_changed` | An account's password was changed | yes |
| `account.renamed` | An account's username changed | yes |
| `account.domain_changed` | An account's main domain changed | yes |
| `account.limits_changed` | An account's quota or limits changed | yes |
| `domain.created` | A domain was added to an account | yes |
| `domain.deleted` | A domain was removed | yes |
| `dns.zone.created` | A DNS zone was created | yes |
| `dns.zone.deleted` | A DNS zone was deleted | yes |
| `dns.zone.reset` | A DNS zone was reset to the template | yes |
| `dns.record.created` | A DNS record was added | yes |
| `dns.record.updated` | A DNS record was changed | yes |
| `dns.record.deleted` | A DNS record was deleted | yes |
| `email.account.created` | A mailbox was created | yes |
| `email.account.deleted` | A mailbox was deleted | yes |
| `email.forwarder.created` | A forwarder was created | yes |
| `email.forwarder.deleted` | A forwarder was deleted | yes |
| `mysql.database.created` | A database was created | yes |
| `mysql.database.deleted` | A database was deleted | yes |
| `mysql.user.created` | A database user was created | yes |
| `mysql.user.deleted` | A database user was deleted | yes |
| `ftp.account.created` | An FTP account was created | yes |
| `ftp.account.deleted` | An FTP account was deleted | yes |
| `ssl.installed` | A certificate was installed | yes |
| `ssl.uninstalled` | A certificate was removed | yes |
| `app.created` | An app was created | yes |
| `app.deleted` | An app was deleted | yes |
| `app.deploy_started` | A deployment was queued | yes |
| `app.rolled_back` | An app was rolled back to a previous release | yes |
| `backup.started` | A backup was queued | yes |
| `package.created` | A package was created | — |
| `package.updated` | A package was changed | — |
| `package.deleted` | A package was deleted | — |
| `reseller.created` | An account became a reseller | — |
| `reseller.suspended` | A reseller was suspended | — |
| `reseller.unsuspended` | A reseller was unsuspended | — |
| `reseller.removed` | An account stopped being a reseller | — |
| `job.completed` | A background job finished (account creation, backup, deploy…) | yes |
| `job.failed` | A background job failed for good | yes |
| `webhook.ping` | Test event sent from the panel | yes |

## Polling instead

The same events are a feed: `GET /api/v1/events?after=SEQ` (server) or `GET /api/v1/accounts/{id}/events?after=SEQ` (one account), oldest first, up to 500 at a time, optionally `&type=a,b`. Each event has a `seq`; keep the last one and ask for what's after it. Events are kept for 30 days. `ky events --follow` prints them as they come.
