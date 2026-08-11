# Docker transition note

## Status and recommendation

This project already uses Docker Compose appropriately. The live ADS-B stack runs
on the DietPi VM from `/opt/adsb`, while this repository holds its reference
Compose configuration. No broad transition is recommended.

Keep vendor clients that require host installers or systemd as host services.
Do not force them into unofficial containers merely for uniformity.

## Future work

- Continue synchronizing repository and live Compose definitions deliberately.
- Preserve `/opt/adsb/.env` outside version control and retain explicit volume and
  USB-device mappings.
- Validate Compose and recreate only affected services after changes.
- Treat receiver USB passthrough, host networking, SDR ownership, and feeder
  credentials as migration boundaries.

