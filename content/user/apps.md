---
title: Apps
description: Deploy Laravel, PHP, Node.js, Python and static sites from Git with no downtime.
---

**Software › Apps** deploys a Git repository to a domain. Push to your branch and the site updates: each deploy builds a new release next to the live one and switches over only when it's ready, so visitors never see a half-built site. A failed deploy leaves the live release untouched.

## App types

| Type | For | Serves |
|---|---|---|
| Laravel | Composer, Vite build, migrations, queues and scheduler | `public/` through PHP-FPM |
| PHP | Any PHP project with a public folder | The public folder through PHP-FPM |
| Node.js | Next.js, Nuxt, Express… | Your process, listening on `127.0.0.1:$PORT` |
| Python | Django, Flask, FastAPI with gunicorn or uvicorn | Your process, listening on `127.0.0.1:$PORT` |
| Static | HTML or a built site (Vite, Astro…) | The output folder |

## Create an app

1. **Source:** a GitHub repository (connect GitHub once under **Apps › GitHub accounts**; your provider enables it) or any Git URL over SSH (the panel gives you a **deploy key** to add to the repository, read-only).
2. **Branch** and **domain**: a domain or subdomain of the account. It keeps its current site until the first deploy is live.
3. Versions: PHP, Node.js (for builds and Node apps) or Python.
4. Optionally **create a database** for the app: its credentials go into the app's `.env`.

A Git server on a private network (an address like `10.x`, `172.16–31.x` or `192.168.x`) works once your hosting provider allows that network; otherwise the form says so. The Git server's SSH host key is read on the first deploy and pinned; **Read the host keys again** (in the app's settings) refreshes it after the server changes its keys.

## Layout

```text
~/apps/NAME/
  releases/20261017031405/   one folder per deploy (the last ones are kept)
  current -> releases/…      the live release; the domain serves it
  shared/                    kept between releases: .env, storage/, uploads…
```

**Shared paths** (one per line, e.g. `storage`, `.env`, `public/uploads`) are linked into every release.

## Deploy script

Each app has a deploy script you can edit. The Laravel default:

```sh
$CREATE_RELEASE()

cd $KY_RELEASE_DIRECTORY

composer install --no-dev --no-interaction --prefer-dist --optimize-autoloader

if [ -f package.json ]; then
    npm ci || npm install
    npm run build
fi

php artisan storage:link
php artisan migrate --force
php artisan optimize

$ACTIVATE_RELEASE()

$RESTART_QUEUES()
```

| Macro | Does |
|---|---|
| `$CREATE_RELEASE()` | Clones the commit into a new release folder and links the shared paths. |
| `$ACTIVATE_RELEASE()` | Switches `current` to the new release and reloads PHP / restarts the app process. |
| `$RESTART_QUEUES()` | Restarts the queue workers so they run the new code. |

Variables available to the script:

| Variable | |
|---|---|
| `KY_RELEASE_DIRECTORY`, `KY_RELEASE` | The new release's folder and id |
| `KY_CURRENT_PATH`, `KY_APP_PATH` | `current` and the app folder |
| `KY_COMMIT`, `KY_COMMIT_SHA`, `KY_BRANCH`, `KY_REPOSITORY` | What's being deployed |
| `KY_DEPLOY_AUTHOR`, `KY_DEPLOY_MESSAGE`, `KY_DEPLOY_TRIGGER`, `KY_DEPLOYMENT_ID` | Who and why |
| `KY_DOMAIN`, `KY_USER`, `KY_APP_NAME`, `KY_APP_TYPE` | |
| `KY_PHP`, `KY_PHP_FPM`, `KY_COMPOSER` | The app's PHP binary, its FPM service, Composer |
| `KY_PORT` | Node.js/Python: the port your process must listen on |

Node.js and Python apps listen on `127.0.0.1:$PORT`, a port of the 20000–29999 range kept for apps: only the app's own process can answer on it, and only the web server, the panel and your account can connect to it.

`php`, `node`, `npm` and `python` in the script are the app's versions. The script runs as your user, with a time limit (settings). A **health check path** (e.g. `/up`) is requested after activation.

## Deploying

- **Deploy now** in the app's page.
- **Push to deploy:** with GitHub, pushes to the branch deploy automatically. With another Git host, add the app's **webhook URL** and secret (in the app's settings) to the repository's webhooks.
- **Deploy hook URL:** a secret URL that starts a deploy when requested, for CI pipelines: `curl -X POST https://SERVER:7711/hooks/deploy/TOKEN`.

Each deploy has a live log. Cancel a deploy that's running; **roll back** to any kept release in one click.

## Environment

**Environment** edits `shared/.env` (Laravel and PHP) or the variables the process gets (Node.js, Python). Every save keeps a version you can look back at. The web process and workers read the environment when they start: saving can restart the app's processes so they pick it up; otherwise the next deploy does. For Laravel, **Run php artisan optimize** rebuilds the cached configuration and routes, so values that routes depend on (`APP_KEY`, domains) take effect too.

**Composer credentials:** paste an `auth.json` for private package repositories (Nova, Spark, Filament…); it's used during deploys only.

## Processes

Long-running commands, restarted if they stop: queue workers (`php artisan queue:work`), Horizon, the scheduler, or any daemon. Set how many run side by side and how long a stopping process may finish its work. Each process has its log.

The **scheduler** (Laravel) runs `php artisan schedule:run` every minute when turned on.

## Laravel Reverb

**Processes › Laravel Reverb** runs the app's WebSocket server (`php artisan reverb:start`) and routes it through the app's domain, so browsers connect to `wss://DOMAIN/app/KEY` on port 443, Cloudflare's proxy included. Install Reverb in the project first (`php artisan install:broadcasting --reverb`).

Turning it on:

- gives Reverb a port on `127.0.0.1` and starts it; every deploy restarts it;
- fills the `.env`: `REVERB_APP_ID`, `REVERB_APP_KEY` and `REVERB_APP_SECRET` only when they are missing (new keys would disconnect everyone), `BROADCAST_CONNECTION=reverb` unless another broadcaster is set, and the addresses: PHP publishes to `127.0.0.1` (`REVERB_HOST`, `REVERB_PORT`, `REVERB_SCHEME=http`), browsers use the domain (`VITE_REVERB_HOST`, port 443, `https`);
- deploys again, because Vite writes the `VITE_REVERB_` values into the built JavaScript.

Only WebSocket requests to `/app/<key>` go to Reverb: the app's own `/app/...` routes and files keep working. **Test connection** opens the WebSocket through the domain on this server and checks Reverb's answer. Turning it off stops the process and the route; the keys stay in the `.env`.

One Reverb process handles about 1,000 connections at a time (PHP's default event loop); more need the `uv` PHP extension.

## Redis

**Turn on Redis** gives the account its own Redis-compatible instance (Valkey) with a password and a memory size. The app's `.env` gets the connection. When it's full, writes fail instead of evicting your queued jobs.

## Logs and commands

**Logs & commands** shows the app's logs (Laravel's `storage/logs`, or the process output; **Copy** takes the whole text shown, **Download** saves the full file, **Clear** empties it) and runs one-off commands in the live release as your user, up to 5 minutes each, for example `php artisan cache:clear` (the output has its own **Copy**).
