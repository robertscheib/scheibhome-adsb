# ads-b — Claude Code Context

## Project Summary

ADS-B aircraft tracking stack for the ScheibTribe home network. Runs as a Docker Compose stack on the DietPi VM at **10.10.1.71** (`/opt/adsb/`). Uses SDR-Enthusiasts images to decode ADS-B/MLAT signals from an RTL-SDR dongle and feeds data to multiple public aggregators (adsb.fi, FlightRadar24, PlaneFinder, ADSBExchange, etc.). Visualization via tar1090/graphs1090, VRS, and Planefence. This repo stores the `docker-compose.yml` for reference and backup — the live stack runs on the DietPi VM.

## Environment

- **Stack host**: DietPi VM at 10.10.1.71 (`/opt/adsb/`)
- **Repo (this machine)**: Ubuntu VM at 10.10.1.19 (`~/projects/ads-b/`)
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

## Agent Collaboration Rules

- **Read History First**: At the start of every session, the agent MUST run `git status` and `git log -n 5` to understand recent changes, and read the `## Active Handoff` section in this file.
- **Commit with Context**: Every commit message must explain the *why* behind a change, not just the *what*.
- **The Handoff Journal**: Before concluding a session or completing a major task, the active agent MUST update the `## Active Handoff` section at the bottom of this file.
- **Interactive Dry Runs**: The agent must always perform a dry run and list planned changes for user approval before modifying code, databases, or configuration files.
- **Explicit Task Tracking**: Maintain a shared checklist of tasks in `task.md` or `CLAUDE.md`. Mark tasks as `[x]` for complete, `[/]` for in-progress, and `[ ]` for pending.

## Active Handoff

- [2026-06-06 (Claude Code)]: Added agent collaboration rules and initialized handoff log.
