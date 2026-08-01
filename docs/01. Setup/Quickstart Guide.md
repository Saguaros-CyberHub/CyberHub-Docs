# 1.1 Quickstart

This guide takes you from an empty machine to a running CyberHub. It covers the
default deployment: the whole stack in Docker Compose behind Caddy, serving
HTTPS on a public domain.

For what each piece does, see [3.9 Deployment & Ops](../03. Developer Overview/09-deployment-and-ops.md).
To run without a public domain, see [2.3 Offline / LAN Mode](../02. User and Admin Guides/03-offline-mode.md).

## Before you start

You need:

| Requirement | Why |
|---|---|
| Docker with Compose v2 | The whole stack runs as one Compose project. |
| A host machine (the "orchestrator VM") | Runs the stack. |
| A domain name pointing at the host, ports 80 and 443 open | Caddy provisions a Let's Encrypt certificate on first boot. Skip this only in LAN mode. |
| A Proxmox VE cluster and an API token | Required to provision lanes. The hub boots without it, but no lab environment will deploy. |
| An Anthropic API key | Only for the CiaB plugin's AI generation. |

## 1. Clone the repository

```bash
git clone https://github.com/Saguaros-CyberHub/CyberCore.git
cd CyberCore
```

## 2. Create your configuration

Two files, both copied from checked-in examples:

```bash
cp example.env .env
cp config/example-site.json config/site.json
```

Edit `.env` and replace every `REPLACE_ME`. At minimum, set:

- `CYBERHUB_HOST` – your domain, which tells Caddy to serve HTTPS.
- `JWT_SECRET` and `SESSION_SECRET` – **set these explicitly.** If you leave
  them unset the server generates random ones at every boot, which logs out
  every user on restart.
- `COOKIE_SECURE=true` – you are serving over HTTPS.
- `ADMIN_EMAIL` – becomes the first admin account.
- `CORE_DB_*` and `DB_*` – database credentials.
- `PROXMOX_API_URL`, `PROXMOX_TOKEN_ID`, `PROXMOX_TOKEN_SECRET` – for provisioning.

Then edit `config/site.json` to describe your cluster: Proxmox nodes, network
plans, and scheduling limits.

The full variable list is in the
[environment variable reference](../03. Developer Overview/09-deployment-and-ops.md#environment-variable-reference).

## 3. Start the stack

```bash
docker compose up -d
docker compose logs -f app
```

The first run builds the app image and takes several minutes.

On a fresh Postgres volume the boot does a lot of one-time work: the
`config/postgres/*` scripts seed `cybercore_db` with the schema, your admin
account, and the module rows; `guacamole-init` loads the Guacamole schema; and
the app creates `clinic_db` and `cle_db` as it loads its plugins.

Watch the log until the app reports it is listening.

## 4. Log in

Open `https://<your-domain>` and sign in with the `ADMIN_EMAIL` account.

## 5. Change the Guacamole password

Guacamole ships with the default credentials `guacadmin` / `guacadmin`. Go to
`/guacamole/` and change the password now — this account brokers every VM
console in the platform.

## Verifying it worked

- `docker compose ps` shows `caddy`, `app`, `postgres`, `redis`, `guacd`, and
  `guacamole` running. `guacamole-init` should show as exited; that is correct,
  it runs once.
- The hub loads over HTTPS with a valid certificate.
- The admin UI lists your Proxmox nodes.

If a feature is missing from the hub, check the log for a module that failed to
load. A broken module is non-fatal by design — the server starts anyway and
degrades that one feature.

## Next steps

- [3.1 Overview](../03. Developer Overview/01-overview.md) – what you just
  installed, and the vocabulary the rest of the docs use.
- [3.5 Lanes & Provisioning](../03. Developer Overview/05-lanes-and-provisioning.md) – deploy your first lane.
- [3.9 Deployment & Ops](../03. Developer Overview/09-deployment-and-ops.md) – logging, operational gotchas, and the migration story.
