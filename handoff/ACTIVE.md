# Active Handoff
- [2026-06-09 (Antigravity)]: Installed and configured `skystats` at `/opt/skystats/` on VM 10.10.1.71. Set vCPUs to 2 via Proxmox VM 102 config and power-cycled VM. Docker containers start cleanly, database migration succeeded, live feeds are polling ultrafeeder (10.10.1.71:8080/data/aircraft.json) and updating PostgreSQL, and the Svelte web UI is listening at http://10.10.1.71:5173/.
- [2026-06-09 (Antigravity)]: Added 4 changes to docker-compose.yml; synced to VM and committed (c46711d):
  1. **OpenSky Network** feeder added (`opensky` container) — needs `OPENSKY_USERNAME` + `OPENSKY_SERIAL` in `/opt/adsb/.env`. Generate serial with one-time docker run command (see commit message).
  2. **Radar1090 UK** feeder added (`radar1090` container) — needs `RADAR1090_STATION_NAME` + `RADAR1090_KEY` in `/opt/adsb/.env`. Request key from mike@tubby.org. NOTE: Radar1090 is UK/Ireland-focused; if location is outside that area, this feeder may not be accepted.
  3. **Prometheus metrics** enabled on ultrafeeder — ports 9273-9274 now exposed. Endpoint: `http://10.10.1.71:9274/metrics`.
  4. **Timelapse1090** enabled on ultrafeeder — accessible at `http://10.10.1.71:8080/timelapse/`.
  - New containers will error/exit until `.env` vars are populated. Existing stack is unaffected.
  - Gap analysis artifact at: `/home/robert/.gemini/antigravity-cli/brain/6aa38ac1-b11f-4a89-a90b-b3939566e8e4/adsb_gap_analysis.md`

- [2026-06-06 (Claude Code)]: Added agent collaboration rules and initialized handoff log.
