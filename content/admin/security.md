---
title: Security
description: Brute force protection, firewall, two-factor authentication and security policies.
---

## Brute Force Protection

**Security Center › Brute Force Protection** counts failed logins to the panel, webmail, mail (IMAP, POP3, SMTP), FTP and SSH, on this server and its nodes.

- An **IP address** that keeps failing is **blocked at the firewall** for a while; repeat offenders are blocked longer.
- An **account name** that keeps failing is **locked for the panel** (other services keep working), so a distributed attack can't guess a password either.
- **Slow attacks** that stay under the window (a few tries every hour) are caught by a second count: 30 failures in 24 hours by default.
- **Guessed email addresses:** 5 failed email logins (IMAP, POP3, SMTP, webmail) to addresses that don't exist on this server block the address. Only attackers try `sac@` or `financeiro@` when they don't exist.
- **Email login countries** (off by default): list the countries your customers read their mail from, like `BR`. One failed email login from any other country blocks the address; a correct login from abroad still works, so travelers aren't locked out.
- The thresholds and durations are adjustable.

A device still configured with an old password counts like an attacker: after a server move or a password change, update every phone and computer, or its address gets blocked (unblock it under **Blocked**).

**Blocked** lists what's blocked now (unblock with one click); **History** shows what happened. Your current IP can't block you out if it's on the **allow list**; add your office or VPN there.

### Allow and block lists

IPs, ranges (CIDR) and **countries**. Allowed entries are never blocked; blocked entries can't reach the server at all. Country rules use a GeoIP database that you can update from the page.

## WordPress logins

Every site gets a limit on password guessing against WordPress: POSTs to `wp-login.php` and `xmlrpc.php` from one IP address. Above the limit (20 per minute by default, after a burst of 20) the web server answers **429 Too Many Requests** without starting PHP, so an attack barely uses CPU. Normal visits, other pages and other forms aren't counted. Change it or turn it off (0) in **Server Settings › Web server › WordPress logins per IP**. Behind Cloudflare the limit applies to the visitor's real address.

Customers can also block `xmlrpc.php` and hide the user list per site: [WordPress Protection](/user/security/#wordpress-protection).

## Firewall

**Networking Setup › Firewall** shows the open ports and opens them. Each port can be **limited to some addresses** (the pencil: IPs or ranges like `203.0.113.0/24`, one per line; empty or `0.0.0.0/0` means anyone), **disabled** (closed, but kept in the list to enable again later) or **deleted** (closed and taken off the list). Limiting SSH (22) to your own address is safer than leaving it open, and the screen warns if your address isn't in the list. The same works on a node, with the page's server selector. KyPanel manages firewalld (AlmaLinux, Rocky, CloudLinux) or ufw (Ubuntu); don't change the same rules by hand.

**Security Center › Host Access Control** adds rules by address: allow or deny a source to a port, for example the panel port (7711) only from your office. Nodes have their own firewall in **Link Server Nodes**. The ports KyPanel uses are in [ports](/reference/ports/).

## Two-factor authentication

Everyone can protect their login with an authenticator app (TOTP) and recovery codes:

- admins: your name (top right) › **Password & 2FA**;
- customers: **Two-Factor Authentication** in their panel.

**Security Center › Two-Factor Authentication** shows who has it and turns it off for an admin who lost their device. For customers: List Accounts › **Reset two-factor sign-in**.

## Security policies

**Security Center › Configure Security Policies**:

| Policy | |
|---|---|
| Require 2FA for administrators | Admins without it set it up right after signing in. |
| Require 2FA for customers | Customers set it up the next time they sign in. Logging in as a customer from Ky Admin isn't affected. |
| Administrator sign-in | IPs or ranges allowed to sign in to Ky Admin. Your own IP must be on the list. API tokens have their own lists. |
| Minimum password strength | Per kind of password (panel, mail, FTP, databases). Applies to the next password chosen. |
| Password maximum age | Forces a new password after a number of days. |
| Restrict outgoing SMTP to the mail server | Only Exim and root may connect to ports 25, 465 and 587 on other servers, so a hacked script can't send spam directly. |

## API tokens

Scripts and billing systems use API tokens instead of passwords: scoped, optionally limited to IPs, with an expiry. See [the API](/developers/api/#api-tokens).

## Audit log

Every change made in the panels or through the API is recorded with who made it, from where and through what (panel, API token, system). Account-related entries are shown with the account.
