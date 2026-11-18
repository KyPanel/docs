---
title: Moving to a new server
description: Move a whole KyPanel server, with its settings, accounts and connections, to another server.
---

**Import Tools › KyPanel Server Transfer** copies a KyPanel server to a new one: its settings, every account with its files, email and databases, and the connections the accounts use (Cloudflare, GitHub, API tokens). The new server ends up the same as the old one, with its own IP addresses already in the DNS. The old server isn't changed, so its sites keep running until you switch.

Use it to move to a bigger server or another provider, or to bring separate mail and database nodes back into one server. To move only some accounts to a server that already has others, use the same page without copying the settings.

## Before you start

1. **Update the old server** to the latest version (it needs the transfer endpoints). If the page says the other server is too old, update it and connect again.
2. **Install KyPanel on the new server** and sign in. Leave it empty: the server settings are only copied to a server without accounts.
3. **Choose the hostname.** Giving the new server the old one's hostname (moved to its address when you switch) keeps every URL the same. A new hostname works too; see [another hostname](#another-hostname).
4. **Lower the DNS TTLs** on the old server a day before, so the switch is quick for domains whose DNS it serves.
5. **Check the disk space** of the new server: it needs room for the largest account twice while it is copied.

## Run the transfer

1. On the **old** server, create an API token in **Ky Admin › API Tokens** for root, with full access (or the **Accounts** and **Import Tools** areas).
2. On the **new** server, open **Ky Admin › Import Tools › KyPanel Server Transfer** and enter the old server's hostname or IP, its Ky Admin port (`7711`) and the token. The token is kept encrypted only while the transfer runs.
3. Compare the certificate fingerprint shown with the old server's, then confirm. On the old server:

   ```
   openssl x509 -in /etc/kypanel/tls/server.crt -outform der | openssl dgst -sha256 -binary | base64 | tr -d =
   ```

4. Select the accounts (all of them for a whole server) and keep **Copy the server settings too** checked.
5. Click **Transfer**. The task runs in the background:
   1. the server settings are copied;
   2. resellers come next, so their packages and privileges exist when their customers arrive;
   3. then every other account: packed on the old server, copied here and restored.

Each account's restore has its own log in **Backup › Backup Restoration**. The transfer list shows each account's state.

> [!IMPORTANT]
> After the server settings are copied, the new server has the old one's administrators: sign in with the old server's root password. The old server's API tokens work on the new one too.

## What is copied

### Server settings

Root's packages, feature lists and resource limit profiles; ACL lists; administrators with their passwords, two-factor authentication, passkeys and API tokens; the GitHub App; backup configuration and destinations; server contacts and notification preferences; webhooks; branding, news and templates; DNS zone templates; the DNS zones outside accounts (such as the hostname and nameserver zones), with their addresses moved to the new server and published; Host Access Control; ModSecurity rules; greylisting trusted hosts and inbound mail blocks; and the settings of Tweak Settings and Basic Setup, the mail relay password included. They replace the new server’s own (of its DNS zones, only those with the same name).

### Each account

| | |
|---|---|
| Account | Username, password, owner, package and limits, shell access, two-factor and passkeys, API tokens, preferences. |
| Files | The home folder. |
| Email | Mailboxes with their mail and passwords, forwarders, autoresponders, filters, default addresses, spam settings, DKIM keys. |
| Databases | Databases with their data, users with their passwords and privileges. |
| DNS | Zones and records, with the addresses changed (see below). |
| SSL | Certificates and keys. |
| Apps | Settings, every release, `.env` versions, processes and webhook URLs. |
| Connections | Cloudflare (token and linked zones) and GitHub. |
| Reseller | Privileges, its own packages, feature lists and limit profiles, branding, news, templates, contacts and webhooks. |

### Addresses

Every DNS record that pointed at the old server, or at its email node, now points at the new server, or at the new server's email node when it has one. SPF records change the same way. Zones linked to Cloudflare are published there again with the new addresses as soon as each account is restored.

## What isn't copied

- **The license.** The new server uses its own.
- **The old server's nodes.** Mail and databases land on the new server, or on the nodes already linked to it.
- **History:** statistics, logs, deploy history and the audit log.
- **IP addresses delegated to resellers.** Delegate the new server's addresses again.
- **GitHub connections** when the new server already had a different GitHub App and the server settings weren't copied: the customer connects GitHub again. The affected apps are named in the restore log, and their first deploy stops with a clear message until the app is linked to a GitHub connection again (or switched to an SSH URL).

## Check, then switch

1. Open a few sites through the new server before changing DNS, for example with your computer's `hosts` file pointing their names at the new IP.
2. Sign in to a mailbox on the new server and check its mail; send a test message.
3. Deploy an app from GitHub, or push to its branch, and check that it deploys.
4. Switch:
   - **Domains on Cloudflare** already point at the new server.
   - **Domains whose DNS this server serves** follow when the registrar's nameserver records (glue records of `ns1`/`ns2`) point at the new server's address.
   - **Domains with DNS elsewhere:** change their records at that provider.
5. Mail arriving at the old server during the switch stays there: keep it running for a day, then copy any late messages.
6. When everything answers from the new server, turn the old one off.

## Another hostname

When the new server's hostname differs from the old one's:

- the GitHub App's webhook is pointed at the new server automatically. Change its **callback URL** to `https://NEW-HOSTNAME:7711/api/v1/github/callback` in the app's settings on GitHub, so customers can connect new GitHub accounts;
- webhook URLs that apps gave to other Git services include the old hostname: update them there;
- customers who bookmarked `https://OLD-HOSTNAME:7711/` use the new address (`panel.` on their own domain doesn't change).
