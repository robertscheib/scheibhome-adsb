# SDR-Enthusiasts Stack Gap Analysis

**Date:** 2026-07-30  
**Author:** Codex GPT-5  
**Scope:** Read-only comparison of the live ADS-B host and documented radio
inventory against the current public repositories in the
[SDR-Enthusiasts GitHub organization](https://github.com/sdr-enthusiasts).
No configuration or live state was changed.

## Executive conclusion

The current installation already covers nearly everything in the
SDR-Enthusiasts catalog that adds value to a two-radio ADS-B station:

- 1090 MHz ADS-B reception, visualization, statistics, MLAT, and redistribution
- 978 MHz UAT reception and redistribution
- all of the mainstream dedicated feeder containers relevant to this station
- Planefence, screenshots, VRS, Prometheus output, timelapse, and historical
  analysis through SkyStats and the separate ADS-B 3D/track services

The best genuinely new use of an available healthy spare orange RTL-SDR is an
**ACARS/VDL2 station**, using the definitions already present but disabled in
this repository. The second-best experiment is **local ATC audio**. Both require
a suitable VHF antenna and placement; the short July 24 test decoded no messages
and therefore does not yet establish that the present VHF antenna path is
useful.

**AIS/ship tracking** is also technically compatible with an orange RTL-SDR,
but it needs a dedicated antenna near 162 MHz and is likely to have less local
traffic value at the station's inland location. HF datalink, satellite ACARS,
Airspy/SDRplay-specific receivers, and APRS transmitting are not good matches
for the currently documented radio and antenna equipment.

There is no compelling additional container to add to the two SDRs currently
passed through to the VM. They are both occupied full-time:

- degraded filtered blue RTL-SDR: 1090 MHz ADS-B through Ultrafeeder
- healthy orange RTL-SDR: 978 MHz UAT through dump978

A third service that receives RF needs a third connected dongle, or it must
replace one of those two roles. The project records show two other healthy
orange dongles were tested and removed from VM passthrough after testing. If
either remains physically available, it is the logical radio for a new VHF
service.

## Live inventory

A read-only check of `10.10.1.71` on 2026-07-30 found these running services.

### SDR-Enthusiasts images

| Live service | Function |
|---|---|
| `ultrafeeder` | 1090 MHz decoder, tar1090, graphs1090, MLAT hub, direct feeds |
| `dump978` | 978 MHz UAT decoder |
| `piaware` | FlightAware ADS-B/UAT feeder |
| `fr24` | FlightRadar24 ADS-B and dedicated UAT feeders |
| `pfclient` | PlaneFinder feeder |
| `airnavradar` | AirNav RadarBox feeder |
| `radarvirtuel` | RadarVirtuel feeder and MLAT |
| `adsbhub` | ADS-B Hub feeder |
| `opensky` | OpenSky Network feeder |
| `radar1090` | Radar1090 feeder |
| `planefence` | Aircraft alerts and reports |
| `screenshot` | Browser screenshot service |
| `vrs` | Virtual Radar Server |

### Other live services

| Live service | Function |
|---|---|
| `planewatch` | Plane.watch feeder |
| `fly-overhead-feeder.service` | Fly Overhead JSON feeder (host service) |
| `skystats`, `skystats-db` | Long-term reception/statistics application |
| `adsb-3d`, `track-service`, `timescaledb-adsb` | 3D and historical track stack |
| `vnstat` | Network statistics |
| `autoheal`, `watchtower` | Container maintenance |

The current `feeders.md` snapshot is stale: OpenSky and Radar1090 are now
running and healthy. The live Ultrafeeder configuration also directly feeds
adsb.fi, ADSB.lol, Airplanes.live, PlaneSpotters, The Air Traffic, AV Delphi,
HPRadar, Fly Italy ADS-B, and ADS-B Exchange, with MLAT where configured.

## Current equipment constraints

The VM currently sees two Realtek RTL-SDR-class USB devices. Both are assigned:

| Radio | Current role | Relevant finding |
|---|---|---|
| Filtered blue RTL-SDR | 1090 MHz ADS-B | Known severe RF-path degradation; still operational |
| Orange Dongle 1 | 978 MHz UAT | Healthy and producing useful direct UAT/TIS-B reception |

The July hardware matrix documented three healthy orange dongles. Dongles 2 and
3 were removed from Proxmox passthrough after testing, rather than documented as
failed or disposed of. Their physical availability should be confirmed before
planning a permanent third receiver.

The known antennas matter more than the container choice:

- Antenna C was the best tested 1090 MHz antenna.
- Moving the UAT antenna to a poor physical position caused a 98.4% message-rate
  loss.
- The available VHF antenna decoded no ACARS or VDL2 messages during short tests.
  The decoders tuned normally and showed no USB errors, so a weak antenna/feed
  line, poor placement, or simply a quiet sample remains possible.

## Comparison with the current SDR-Enthusiasts catalog

The organization had 63 public repositories at the time of review. Many are
libraries, base images, installers, build infrastructure, archived predecessors,
or hardware-specific alternatives—not additional station applications.

### Already covered or redundant

| Available project/capability | Current coverage | Assessment |
|---|---|---|
| `docker-adsb-ultrafeeder` | Running | Core receiver already installed |
| `docker-dump978` | Running | UAT already installed |
| PiAware, FR24, PlaneFinder, OpenSky, ADS-B Hub, AirNav, RadarVirtuel, Radar1090 | Running | Dedicated feeders already installed |
| `docker-tar1090` | Included in Ultrafeeder | Separate container is redundant; its own README recommends Ultrafeeder |
| `docker-telegraf-adsb` | Prometheus already enabled; SkyStats also stores history | Possible, but adds little unless an InfluxDB/Grafana deployment is specifically desired |
| `docker-reversewebproxy` | Nginx Proxy Manager is used elsewhere | Redundant |
| `docker-readsb-protobuf`, `docker-multifeeder`, `docker-adsbexchange` | Archived/superseded by Ultrafeeder | Do not add |
| `docker-api2sbs` | Local RF data already available | Imports remote Airplanes.live API data; it is not additional reception and should not be fed back into the local aggregation path |
| `docker-flightairmap` / `FlightAirMap` | SkyStats, tar1090, VRS, ADS-B 3D already installed | Archived container and duplicative visualization/history |
| `docker-ModeSMixer2` | Ultrafeeder/readsb already merges inputs | Archived and redundant |
| `docker-beast-splitter` | Intended for Mode-S Beast hardware | Current receivers are RTL-SDRs |
| `airspy_adsb` | Airspy hardware only | Not compatible with current RTL-SDRs |
| `docker-sdrplay-beast1090` | SDRplay hardware only | Not compatible with current RTL-SDRs |

### Feasible with a spare orange RTL-SDR

#### 1. ACARS and VDL2 — best next project

Relevant current projects:

- [`docker-acarsdec`](https://github.com/sdr-enthusiasts/docker-acarsdec)
- [`docker-dumpvdl2`](https://github.com/sdr-enthusiasts/docker-dumpvdl2)
- [`acars_router`](https://github.com/sdr-enthusiasts/acars_router)
- [`docker-acarshub`](https://github.com/sdr-enthusiasts/docker-acarshub)
- [`acars-bridge`](https://github.com/sdr-enthusiasts/acars-bridge)

The repository already has disabled definitions for `acarsdec`, `dumpvdl2`,
`acars_router`, and `acarshub`. This is therefore primarily a hardware/RF
validation task, not a missing-software task.

One RTL-SDR can normally cover multiple nearby ACARS channels or multiple VDL2
channels within its usable bandwidth, but simultaneous ACARS and VDL2 coverage
may require careful frequency planning or separate radios. The existing
definitions also need review before enablement:

- `acarshub` maps host port `8091`, which currently conflicts with live VRS.
- The configured tar1090 URL uses hostname `tar1090`, but the live service is
  named `ultrafeeder`.
- The radio serial and frequency variables must be assigned without exposing
  their values.
- The VHF antenna/feed line should first be validated against a continuous
  local signal such as NOAA weather radio or airport ATIS/AWOS.

Recommendation: validate the VHF RF path, then start with **VDL2** for a longer
test because it provides richer digital traffic near a major aviation market.
Add ACARS if the antenna proves useful and bandwidth/radio allocation permits.

#### 2. Local ATC audio — easy, but mutually exclusive with other radio uses

Relevant project:

- [`docker-rtlsdrairband`](https://github.com/sdr-enthusiasts/docker-rtlsdrairband)

The disabled `airband` service is already present. It can scan or monitor local
aviation frequencies and stream through Icecast. It needs a spare RTL-SDR and a
working airband antenna. It cannot share a conventional RTL-SDR simultaneously
with dump978, Ultrafeeder, or another decoder.

The Compose definition currently contains the `RTLSDRAIRBAND_SERIAL` variable
twice and has not been validated as a production service. This is suitable for
an experiment after RF-path validation, not immediate enablement.

#### 3. AIS ship tracking — technically feasible, lower expected local value

Relevant projects:

- [`docker-shipfeeder`](https://github.com/sdr-enthusiasts/docker-shipfeeder)
- [`docker-vesselalert`](https://github.com/sdr-enthusiasts/docker-vesselalert)
- `docker-sdrmap`

Shipfeeder uses AIS-Catcher, provides a local map, and can feed multiple marine
aggregators. Its documented prerequisite is a dedicated SDR capable of about
160 MHz with an appropriate antenna. A healthy orange RTL-SDR meets the radio
requirement, but the existing ADS-B/UAT antennas are not appropriate substitutes
for a properly placed marine-VHF antenna near 162 MHz.

At this inland suburban location, useful reception would depend heavily on
antenna height and line of sight toward waterways or Lake Michigan traffic.
This makes AIS a reasonable experiment only if a suitable VHF/AIS antenna is
already available or the marine-tracking use case is especially interesting.
`docker-vesselalert` is useful only after AIS reception works.

### Not a good match for the currently documented equipment

| Available project | Missing requirement / reason |
|---|---|
| `docker-dumphfdl`, `docker-hfdlobserver` | Practical HF antenna and an HF-capable receiver/front end; current antennas and assigned receivers are for VHF/UHF |
| `docker-jaero` | L-band satellite antenna/dish, LNA/filter, and suitable receiver setup |
| `docker-aprs-tracker` | GPS and amateur-radio/APRS transmit path; transmitting requires licensing and is outside the receive-only station |
| `docker-sdrreceiver`, `docker-xng` | Specialized roles with no clear benefit over the current ADS-B applications |
| AIS `ais2adsb` bridge | Requires AIS reception first; adds ship/SAR targets to VRS but no new RF capability |

## Recommended order

1. **Repair or replace the degraded blue 1090 MHz receiver/RF path.** This will
   produce more value than adding another application while the primary ADS-B
   receiver is known to be severely impaired.
2. **Confirm whether orange Dongle 2 or 3 is physically available** for permanent
   assignment and add it to Proxmox passthrough only when a test is approved.
3. **Validate the VHF antenna path** with a continuous known signal.
4. **Run a longer isolated VDL2 trial**, then ACARS if useful. Do not enable the
   existing full ACARS group unchanged because of the VRS port conflict and
   stale hostname.
5. **Try ATC audio** if listening is preferred over collecting datalink
   messages.
6. **Consider AIS only with an appropriate 162 MHz antenna** and realistic
   expectations for the station's location.

## Bottom line

Yes, there are additional SDR-Enthusiasts applications that can run with the
documented equipment, but not on the two radios currently assigned without
displacing ADS-B or UAT. If one of the other healthy orange dongles is still
available, ACARS/VDL2 is the strongest next addition. No new container should be
enabled until the VHF antenna path is proven, and fixing the degraded 1090 MHz
hardware remains the highest-value improvement to the station.

