---
title: Resellers
description: Let customers sell hosting from your server, with their own accounts, packages and branding.
---

A reseller is a hosting account that can also create and manage accounts of its own. Resellers sign in to Ky Admin with their account's username and password and see only their accounts, their packages and the tools their privileges allow.

## Make a reseller

- New account: check **Make the account a reseller** in Create a New Account.
- Existing account: **Resellers › Show Reseller Accounts › Make a Reseller**, or Modify Account › **Reseller Privileges**.

Removing reseller status keeps the account's own hosting; its accounts and packages move to root.

## Limits

Per reseller (**Resellers › Edit Reseller Nameservers and Privileges**):

- maximum number of accounts;
- total disk and bandwidth it can hand out, with optional **overselling** (handing out more than the total);
- its own **nameservers** (shown to its customers and used in their zones; empty = the server's);
- whether the reseller **owns itself** (manages its own account like one of its customers).

## Privileges

Privileges decide what the reseller can do. Group them in **privilege lists** to reuse them.

| Group | Privileges |
|---|---|
| Accounts | List, create, terminate, suspend, upgrade/downgrade, edit, change quota, limit bandwidth, change passwords, log in to customer panels, view bandwidth |
| DNS | Edit zones, add zones, delete zones, park domains, edit MX records |
| Packages | Add, edit, assign IPs, assign shell access, allow unlimited packages, unlimited disk, unlimited bandwidth, view root's packages |
| Other | SSL, branding |

The API enforces the same privileges: a reseller's API calls and sessions only reach its own accounts.

## Branding

With the branding privilege a reseller sets its own logo, colors and name for its customers' panels and sign-in pages (**Reseller Branding**). Root sets the server's default branding in **Service Configuration › Branding**. The error pages of the reseller's customers' sites follow the reseller's branding.

## Root acting as a reseller

**Show Reseller Accounts ›** the reseller's menu **› View Ky Admin as this reseller** opens Ky Admin exactly as the reseller sees it, until you exit. Everything you do is logged as root.

## Suspend and terminate a reseller

- **Suspend** suspends the reseller and all of its accounts.
- **Terminate** removes the reseller's accounts, its packages and the reseller's own account.
