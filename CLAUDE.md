# ads-b — Claude Code Context

## Project Summary

ADS-B aircraft tracking stack for the ScheibTribe home network. Runs as a Docker Compose stack on the DietPi VM at **10.10.1.71** (`/opt/adsb/`). Uses SDR-Enthusiasts images to decode ADS-B/MLAT signals from an RTL-SDR dongle and feeds data to multiple public aggregators (adsb.fi, FlightRadar24, PlaneFinder, ADSBExchange, etc.). Visualization via tar1090/graphs1090, VRS, and Planefence. This repo stores the `docker-compose.yml` for reference and backup — the live stack runs on the DietPi VM.

## Environment

- **Stack host**: DietPi VM at 10.10.1.71 (`/opt/adsb/`)
- **Repo (this machine)**: Ubuntu VM at 10.10.1.19 (`~/projects/adsb-platform/ads-b/`)
- **Key file**: `docker-compose.yml` — all env vars populated from `/opt/adsb/.env` on the DietPi VM
- **Ports**: tar1090 on 8080, piaware on 8081/8088, planefence on 8083, planefinder on 8084, VRS on 8091

## Key Services

| Container | Purpose |
|---|---|
| `ultrafeeder` | Primary decoder (readsb + tar1090 + MLAT hub) |
| `piaware` | FlightAware feeder |
| `fr24` | FlightRadar24 feeder |
| `pfclient` | PlaneFinder feeder |
| `planefence` | Nearby aircraft alerting |
| `vrs` | Virtual Radar Server |
| `watchtower` | Auto-update containers daily |
| `autoheal` | Auto-restart unhealthy containers |

## Rules

- The live stack is on 10.10.1.71 — SSH as `root@10.10.1.71` to manage it
- Never edit `docker-compose.yml` without also syncing to `/opt/adsb/` on the DietPi VM
- Secrets (feeder keys, SDR serials, lat/lon) live in `/opt/adsb/.env` on the DietPi VM — never commit them here

## Reference

- `feeders.md` — full feeder roster (8 installed + 3 not installed: OpenSky, ADS-B One, Airframes), snapshotted from radar-dash `/api/adsb/feeders`.

## Active Handoff

Current state, validation, open work, and operational risks are maintained in `handoff/ACTIVE.md`.
Historical entries migrated on 2026-07-18 are preserved in `handoff/archive/CLAUDE-active-20260718.md`.
