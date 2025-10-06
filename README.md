# jellyfin-ts (Compose setup)

This repository contains a small Docker Compose setup for running Jellyfin alongside a Tailscale sidecar.

## What is included

- `compose.yml` - Docker Compose configuration for `tailscale` and `jellyfin` services.

## Config & recommended layout

Keep service-specific files (state, authentication keys, config files) out of the repository or in a top-level `config/` folder. Recommended layout:

```
config/
  tailscale/
    serve-config         # your tailscale files or a place to mount state
  jellyfin/
    # optional jellyfin-specific helpers
compose.yml
.env                    # local environment variables (not committed)
```

Mounts in `compose.yml` currently use Docker volumes for state. If you prefer bind-mounts, update the `volumes` section (example below).

## .env and secrets

Put sensitive values in a `.env` file and add it to `.gitignore`. Key used in the compose file:

- `TS_AUTHKEY` - Tailscale auth key
- `JELLYFIN_URL` - optional published server URL for Jellyfin

Example `.env` snippet:

```env
# .env (example)
TS_AUTHKEY=tskey_xxx
JELLYFIN_URL=https://jellyfin.example.com
```

## Quick start

Start services in detached mode:

```bash
docker compose -f compose.yml up -d
```

Follow logs:

```bash
docker compose -f compose.yml logs -f jellyfin
```

If you change to a bind-mount for Tailscale state, ensure the folder is writable by the container (or adjust ownership):

```bash
mkdir -p ./config/tailscale
sudo chown 1000:1000 ./config/tailscale
```

On macOS the file permission semantics differ from Linux containers; test mounts and adjust as needed.

## Notes

- The Compose file includes a `healthcheck` for Jellyfin. If Jellyfin fails to come up, check logs and ensure the media mount (`/mnt/media`) is accessible to the container.
- Keep secrets out of version control. Use `.env` and secret managers for production.

---

If you want, I can: create the `config/tailscale` folder and update `compose.yml` to bind-mount it (or add a sample `.env`). Tell me which option you prefer.
