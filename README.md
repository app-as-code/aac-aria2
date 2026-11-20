<p align="center">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="assets/icon-dark.svg">
    <img src="assets/icon.svg" alt="aria2 logo" width="128">
  </picture>
</p>

<h1 align="center">aac-aria2</h1>

<p align="center">Self-hosted deployment definition for AriaNg, a web interface for the aria2 download utility, together with an aria2 download backend.</p>

<p align="center">
  <a href="https://github.com/mayswind/AriaNg"><img alt="Upstream" src="https://img.shields.io/badge/upstream-mayswind%2FAriaNg-blue?logo=github"></a>
  <img alt="Image" src="https://img.shields.io/badge/image-p3terx%2Fariang%3Atest-2496ED?logo=docker&logoColor=white">
  <a href="https://github.com/mayswind/AriaNg/blob/master/LICENSE"><img alt="License" src="https://img.shields.io/badge/license-MIT-green"></a>
</p>

## About

AriaNg is a lightweight, web-based front end for [aria2](https://aria2.github.io/), a command-line
download utility that supports HTTP(S), FTP, BitTorrent and Metalink. AriaNg runs entirely in the
browser and talks to the aria2 JSON-RPC interface, so downloads can be added, monitored and
managed without a desktop client. This deployment runs the AriaNg web interface next to an
aria2 download backend (the `aria2-pro` image) that stores downloads in a persistent volume.

Key features (as provided by upstream):

- Add HTTP, FTP, magnet and torrent downloads from the browser
- Monitor active, waiting and stopped tasks with speed and progress information
- Adjust global and per-task options such as speed limits and file selection
- Responsive, dark-mode capable interface

## Upstream

- AriaNg project: <https://github.com/mayswind/AriaNg>
- aria2 project and documentation: <https://aria2.github.io/>
- aria2 backend image project: <https://github.com/P3TERX/Aria2-Pro-Docker>
- Container images: `p3terx/ariang:test` (web interface), `p3terx/aria2-pro:202209060423` (aria2 backend, deployed as `aria2-pro`)
- License: MIT (upstream projects)

## Quick start (Docker Compose)

The Compose file is rendered from `service.yml` by the App-as-Code template engine. For a
manual run, the external networks used for routing must exist first:

```bash
docker network create services-secured
docker network create services-exposed

docker compose up -d
```

No secrets are required by this definition (`secrets: {}` in `service.yml`).

## Configuration

Environment variables (defaults from `service.yml`):

| Variable | Default | Description |
|---|---|---|
| `PUID` / `PGID` | `1000` | User and group ID the containers run as |
| `TZ` | `Europe/Berlin` | Time zone |
| `RCLONE_CONFIG` | `/config/rclone.conf` | Path to an optional rclone configuration inside the aria2 container |
| `UPDATE_TRACKERS` | `true` | Update the BitTorrent tracker list on start |
| `LISTEN_PORT` | `6888` | Port aria2 listens on for BitTorrent peer connections |
| `RPC_PORT` | `6800` | Port of the aria2 JSON-RPC interface |
| `IPV6_MODE` | `false` | Enable IPv6 mode in the aria2 backend |
| `UMASK_SET` | `1000` | File creation mask for downloaded files |

Ports:

| Port | Protocol | Served by | Purpose |
|---|---|---|---|
| `26801` (external) -> `6880` | TCP | AriaNg web | AriaNg web interface (`web`) |
| `6800` | TCP | aria2 backend | aria2 JSON-RPC interface |
| `6888` | TCP and UDP | aria2 backend | aria2 BitTorrent peer connections |

Volumes:

| Mount | Description |
|---|---|
| `downloads` -> `/downloads` | Downloaded files (shared between the web interface and aria2) |
| `config` -> `/config` | aria2 configuration (for example `rclone.conf`) |

## Deploy with App-as-Code

1. Edit `service.yml`. It is the single source of truth for image, ports, environment,
   volumes, dependencies and per-stage overrides. Do not edit the rendered output.
2. Pushing to `main` (or a configured stage branch) runs the service pipeline, which renders
   the manifests with `aac-template-engine` and triggers the deployment.
3. Releases of this definition are published under <https://github.com/app-as-code/aac-aria2>.

## Credits and license

AriaNg and aria2-pro are developed by their respective upstream projects and are licensed under
the MIT License. This repository only contains the deployment definition for them and is not a
fork of either application. The icon is taken from the selfh.st icon collection; see
[`assets/ICON_SOURCE.md`](assets/ICON_SOURCE.md) for sources and licenses.
