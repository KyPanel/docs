---
title: Email
description: Mailboxes, forwarders, filters, spam and deliverability.
---

## Email Accounts

**Email › Email Accounts** — create mailboxes on any of the account's domains, with a password and a quota (or unlimited, up to the package's maximum). Every account has a default mailbox, `USERNAME@MAIN-DOMAIN`, that can't be deleted.

Per mailbox:

- **Manage:** quota, password, and **restrictions**: suspend logins (closes open IMAP/POP3 sessions), receiving or sending;
- **Check Email:** opens the webmail already signed in;
- **Connect Devices:** the client settings and an Apple profile for iPhone, iPad and Mac.

Usage is refreshed every few minutes.

## Forwarders

**Email › Forwarders**:

- **Address forwarders:** send `sales@example.com` to one or more addresses; or discard the mail, or bounce it with a message. A mailbox that also forwards keeps its own copy.
- **Domain forwarders:** forward a whole domain to another domain (addresses that have no mailbox or forwarder).

## Email Routing

Whether the server delivers a domain's mail locally (**Local**), only queues it as a backup MX (**Backup**), or hands it to another provider (**Remote**, for Google Workspace, Microsoft 365 and the like). **Automatic** decides from the MX records.

## Autoresponders

Automatic replies for a mailbox: from name, subject, body (plain text or HTML), how often the same sender gets it (hours), and optional start and stop dates.

## Default Address

What happens to mail sent to an address that doesn't exist on a domain: refuse it (default), deliver it to a mailbox (catch-all), forward it, or discard it.

## Email Filters

Rules on From, To, Subject, body, headers or spam score, with actions: move to a folder, forward, discard, refuse with a message, or stop processing. **Global Email Filters** apply to every mailbox of the account; each mailbox can also have its own. Filters run in order; drag to reorder.

## Spam Filters

Every incoming message gets a spam score (Rspamd). With the spam filter on:

| Setting | |
|---|---|
| Spam threshold score | At or above it, mail is spam. 5 is a good start; lower catches more. |
| Spam Box | Spam goes to a "Spam" folder instead of the inbox. |
| Subject prefix | Tag spam, e.g. `[SPAM]`. |
| Delete at or above | Discard mail with a very high score. |
| Never spam | Senders (`someone@example.com` or `@example.com`) that are never treated as spam. |
| Always refused | Senders whose mail is rejected. |

Off: scores are still added to the headers but nothing is moved. Mail with a very high score is refused by the server either way.

The filter learns from what you sort. Moving a message into **Junk** (in the webmail or any IMAP client):

- sends that sender's next messages to Junk, for that mailbox (**Senders moved to Junk**, at the bottom of the page, lists them, with a button to take one off);
- sends copies of the same message to Junk, from any sender and for every mailbox on the server, from the first one;
- teaches the statistical filter, which counts for every mailbox and weighs in after 50 messages of each kind.

Moving a message out of Junk to another folder (not Trash) undoes all three. The last move wins: a message moved out by mistake goes back to being spam when you move it into Junk again.

## Email Deliverability

For each domain: whether **SPF**, **DKIM**, **DMARC**, the MX and the reverse DNS are right, and the exact records to fix them. When the domain's DNS is on this server, **Repair** installs them. The SPF customizer adds outside senders (a newsletter service, another mail server).

## Track Delivery

Every message the account sent, and mail to or from its domains, with the remote server's answer. Search by address or message ID. Use it when a customer says "my mail didn't arrive".

## Configure Mail Client

Server names, ports and security for IMAP, POP3 and SMTP, for any mailbox:

| | Secure (recommended) |
|---|---|
| IMAP | `mail.<domain>`, port 993, SSL/TLS |
| POP3 | `mail.<domain>`, port 995, SSL/TLS |
| SMTP | `mail.<domain>`, port 465 (SSL/TLS) or 587 (STARTTLS) |

AutoSSL gives `mail.<domain>` its own certificate on the server that holds the mailboxes (this one, or a [mail node](/admin/nodes/)), so mail apps check it without warnings. Until it has one (for example while `mail.<domain>` still points elsewhere), the page shows the server's hostname instead, whose certificate is always valid; both names keep working.

Username: the full email address. Most mail apps also configure themselves automatically from the address (autoconfig and autodiscover), with the same server name.

**Apple profile** downloads a `.mobileconfig` that sets up the mailbox on an iPhone, iPad or Mac. **Thunderbird for Android** shows a QR code with one mailbox or all of them: in the app, Import settings › Scan QR code. Passwords aren't in it, the app asks for each one. Many mailboxes take more than one code; scan them in order.
