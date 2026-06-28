# ADS-B Feeder Roster

Snapshot from the radar-dash `/api/adsb/feeders` endpoint (which audits the live
stack on **10.10.1.71** via Docker container inspection + realtime feeder stats).

> **Captured:** 2026-06-28 · Station 41.6993, -88.10787, 680 ft.
> Source of truth is the running stack on 10.10.1.71 (`/opt/adsb/docker-compose.yml`),
> not this file — re-check with `curl -s http://10.10.1.19:3010/api/adsb/feeders`.

## Installed & feeding (8)

| Feeder | Container / source | Notes |
|---|---|---|
| FlightRadar24 (FR24) | `fr24` (realtime) | alias `T-KLOT9`, MLAT active |
| FlightAware (PiAware) | `piaware` (realtime) | PiAware 11.0; MLAT clock "unstable" |
| ADSBexchange | ultrafeeder (audit) | via `feed1.adsbexchange.com` + `mlat.adsbexchange.com` |
| PlaneFinder | `pfclient` (audit) | healthy |
| ADSBhub | `adsbhub` (audit) | — |
| AirNav RadarBox | `airnavradar` (audit) | rbfeeder, Server: ready |
| RadarVirtuel | `radarvirtuel` (audit) | Receiver: connected, MLAT active |
| Plane.watch | `planewatch` (audit) | ADSB=healthy, MLAT=healthy |

## NOT installed (3)

These appear on the radar-dash Feeders page as **NOT INSTALLED** (dashed gray
card). They are recognized aggregators that the stack *could* feed but currently
does not — "Not configured on this host."

| Feeder | Public site | Status | How to add |
|---|---|---|---|
| **OpenSky Network** (`opensky`) | https://opensky-network.org/my-opensky | not_installed | `opensky` container was previously added to `docker-compose.yml` (commit c46711d) but needs `OPENSKY_USERNAME` + `OPENSKY_SERIAL` in `/opt/adsb/.env`; generate serial via one-time docker run. |
| **ADS-B One** (`adsbone`) | https://adsb.one/ | not_installed | Feed via ultrafeeder `ULTRAFEEDER_CONFIG` (adsb.one accepts Beast/`mlat` ingest, `feed.adsb.one`). No container needed. |
| **Airframes** (`airframes`) | https://airframes.io/ | not_installed | Airframes is ACARS/VDL/HFDL-focused (not pure ADS-B); needs a separate acarsdec/dumpvdl2 feeder, not just an SDR ADS-B feed. |

Note: a 4th feeder, **Radar1090 UK** (`radar1090`), was also added to the
compose file (commit c46711d) but is UK/Ireland-focused and may not accept this
US station — it does not appear in the radar-dash roster.
