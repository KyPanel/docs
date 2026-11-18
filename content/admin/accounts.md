---
title: Accounts
description: Create, change, suspend and terminate hosting accounts.
---

A hosting account is a customer: a Linux user with a home folder, a main domain, mail, databases and the limits of its package. Accounts are under **Account Information** and **Account Functions** in Ky Admin.

## Create an account

**Account Functions › Create a New Account.**

| Field | |
|---|---|
| Domain | The main domain. Stored in punycode; `www.` is added automatically. |
| Username | 1 to 16 characters: a lowercase letter, then lowercase letters or digits. Can't be changed by the customer. |
| Password | Must pass the password policy (Security Center › Password Strength Configuration). |
| Email | The customer's contact address. |
| Choose a Package | The plan, with its limits and feature list. |
| IP Address | Shared (default) or a free dedicated IP. |
| Shell Access, CGI Access, Locale | Defaults from the package. |
| Email Server, Database Server | This server or a [node](/admin/nodes/), when nodes are linked. |
| Mail settings | Spam filter, spam box, DKIM, SPF, DMARC (all on by default). |
| Reseller | "Make the account a reseller" and "Make the account own itself". |

Creating an account runs a background job; its log lists each step (system user, home folder, web server and PHP, DNS zone, mail domain and DKIM key, disk quota, resource limits, FTP). AutoSSL is queued at the end.

From the API or the command line:

```sh
ky accounts create acme acme.com --package Starter --email owner@acme.com --wait
```

## List and find accounts

**Account Information › List Accounts** shows every account with its domain, owner, package, disk and bandwidth use and status. Search by username, domain or contact email; filter by status, package or owner; export the list as CSV.

Other views: **List Suspended Accounts**, **Show Accounts Over Quota**, **List Parked Domains**, **List Subdomains**, **View Bandwidth Usage** (per month) and **Resource Usage** (CPU, memory, I/O and processes per account, with the times an account hit its limits).

## Change an account

Each account's menu in List Accounts:

| Action | |
|---|---|
| Modify Account | Contact email, owner, locale, shell access, CGI, reseller privileges, and the limits (which then override the package's: the account shows as *customized*). |
| Change Plan | Move to another package; every limit, the PHP workers and the shell follow. |
| Change Quota | Disk quota in MB. |
| Change Password | The account's login password (also used for FTP and the default mailbox). |
| Change IP Address | Shared or dedicated IP; the web server and DNS are updated. |
| Change Owner | Root or a reseller. |
| Log in to the Account’s Panel | Opens the customer panel as the account (a one-time link). |
| Reset two-factor sign-in | Turns off the customer's 2FA when they lost their phone and recovery codes. |
| Repair | Rewrites everything the panel manages for the account (web, PHP, DNS, mail, quotas, limits) from the panel's database. |

**Changing the username or the main domain** is under Modify Account. The panel shows what will change before doing it (home folder, databases and their users, mail, DNS).

## Suspend and unsuspend

**Suspend Account** (with an optional reason, shown to the customer):

- websites answer with a "suspended" page (HTTP 503);
- logins are blocked: panel, SSH, SFTP, FTP, mail;
- sending mail is blocked, but **incoming mail is still delivered**;
- cron jobs are paused and the account's processes stopped.

Nothing is deleted. **Unsuspend** restores everything. A *locked* suspension can only be lifted by root, not by the account's reseller.

## Terminate

**Terminate Account** deletes the account: its sites, mail, databases, DNS zones, FTP accounts, cron jobs and certificates. Options:

- **Keep the home folder** (moved aside, not deleted);
- **Keep the DNS zones**.

You type the username to confirm. Terminating can't be undone, unless you have a backup. Several accounts at once: select them in List Accounts and choose **Terminate**.

## Account Settings

**Account Functions › Account Settings** holds the server-wide rules for suspended accounts and bandwidth limits: the page suspended sites show (HTTP 503), whether accounts over their bandwidth get their sites suspended, and the page those sites show (HTTP 509). Certificate renewals keep working on suspended sites.

## Email all users

**Account Functions › Email All Users** sends one message to every account's contact address (or every reseller's, from Resellers › Email All Resellers). It's queued as a task; one message per address.
