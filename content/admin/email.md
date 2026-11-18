---
title: Email
description: The mail server, its queue, sending limits and deliverability.
---

Mail runs on Exim (SMTP), Dovecot 2.4 (IMAP, POP3, Sieve filters) and Rspamd (spam scoring), with Roundcube as webmail. Every domain gets DKIM signing, SPF and DMARC when it's added. Customers manage mailboxes in their panel; this page covers the server side, under **Email** in Ky Admin.

Rspamd learns server-wide (Bayes): it learns spam from mail it rejects and ham from mail that scores below zero, and every message a user moves into or out of **Junk** over IMAP is taught to it. Its statistics are kept in a dedicated Valkey (`kp-rspamd-valkey.service`, `127.0.0.1:11380`); the classifier weighs in once it has learned 50 messages of each kind (Rspamd's default is 200).

A message moved into Junk is also added to a local fuzzy storage (Rspamd's `fuzzy_storage` worker on `127.0.0.1:11335`, same Valkey, hashes kept 90 days): copies of it score `KP_FUZZY_JUNK` (8 points, above the default threshold but not rejected) for every mailbox on the server. Its sender goes to that mailbox's **Senders moved to Junk**, whose mail is filed in Junk by the mailbox's Sieve script. Moving the message out of Junk removes all three; the last move wins. Rspamd.com's public fuzzy feeds stay on.

## Mail Queue Manager

Messages the server hasn't delivered yet. Filter by sender, recipient or message ID; open a message to see its headers and delivery attempts. Selected messages can be:

- **delivered now** (retry immediately);
- **bounced**: delivery stops and the sender gets a failure notice;
- **deleted**: removed without notice.

## Mail Delivery Reports

Every delivery the server made or tried, with the remote server's answer: searchable by address, message ID or host. Customers see the same report for their own domains as **Track Delivery**. Reports are kept for a set number of days (Sending Limits page).

## Sending Limits

Hourly limits per account, enforced by Exim for both mail clients and scripts:

- **Default limit (recipients per hour)** for accounts whose package doesn't set one;
- **Alert above** a number of different recipients per hour, the usual sign of a hacked site;
- **Hold automatically**: an account over the alert threshold has its outgoing mail held until you release it.

**Last hour** shows each account's outgoing volume. **Hold an account's outgoing mail** stops it manually (messages stay queued); **Release** lets them go.

Also in **Security Center › Configure Security Policies**: *Restrict outgoing SMTP to the mail server* makes only Exim and root connect to ports 25, 465 and 587, so a compromised script can't send mail directly.

## Email Deliverability

What other mail servers check before trusting mail from this server: the hostname's A record, reverse DNS (PTR), and SPF/DKIM/DMARC for the hostname. Missing records in local zones are fixed with **Repair**. The PTR is set at your hosting provider.

Customers check their own domains in [Email Deliverability](/user/email/#email-deliverability).

## Mail Troubleshooter

Shows how the server would route an address (`exim -bt`): local mailbox, forwarder, remote server, or failure, step by step. On a server with mail nodes you choose which server to test.

## Email Routing Configuration

How each domain's mail is routed:

| Routing | |
|---|---|
| Local | Mailboxes are on this server (or its mail node). |
| Backup | This server is a backup MX: it queues mail and passes it on to the primary. |
| Remote | Mail is hosted elsewhere (Google Workspace, Microsoft 365…): local messages to the domain are sent out. |
| Automatic | Decided from the domain's MX records. |

## Mail nodes

Mailboxes can live on a separate mail server managed by this panel. New accounts choose their mail server; existing domains can be moved. See [nodes](/admin/nodes/).
