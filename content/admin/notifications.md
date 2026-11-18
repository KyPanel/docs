---
title: Notifications
description: Alerts about the server, and where they're sent.
---

The bell at the top of Ky Admin shows the server's notifications. The same alerts can be sent by email and to webhooks.

## What's reported

| Alert type | When |
|---|---|
| Service down | A monitored service failed, was restarted, or keeps failing. |
| Disk space | A filesystem is almost full. |
| Quotas | An account or mailbox reached its disk quota. |
| Bandwidth | An account went over its monthly bandwidth. |
| SSL certificates | A certificate is about to expire or couldn't be renewed. |
| Brute force | An address was blocked or an account name locked after failed logins. |
| Updates | A panel update is available, was installed or failed; a reboot is needed. |
| License | The license needs attention. |

Customers get their own notifications (quota, bandwidth, certificates) in their panel and at their contact email.

## Contact Manager

**Server Contacts › Contact Manager** sets where alerts go and how important each type is.

- **Channels:** email addresses and webhooks. A webhook channel receives a signed JSON `POST` per alert (`X-KyPanel-Signature`, the same scheme as [webhooks](/developers/webhooks/#verify-the-signature)); use it for Slack, Discord or your monitoring through a small relay.
- **Priorities:** high, medium, low or off per alert type; each channel takes alerts from a minimum priority.
- **Send a test** checks a channel.

Failed deliveries are retried with backoff.

## Webhooks for automation

Contact Manager is for people. For systems that react to changes (an account created, a domain added, a job finished) use [webhooks](/developers/webhooks/) instead.
