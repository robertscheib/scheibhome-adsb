# Airplanes.live and ADSB.lol integration research

Date: 2026-07-24
Scope: Research only. No code, configuration, container, network, or live-stack changes were made.

## Executive conclusion

Both services are extremely easy to integrate with this ADS-B stack because the
repository's Ultrafeeder configuration **already contains the current,
upstream-supported ADS-B and MLAT connections for both**:

```yaml
adsb,in.adsb.lol,30004,beast_reduce_plus_out;
adsb,feed.airplanes.live,30004,beast_reduce_plus_out;
mlat,in.adsb.lol,31090,--privacy;
mlat,feed.airplanes.live,31090,--privacy;
```

The global Ultrafeeder identity and MLAT site name are also already wired:

```yaml
- UUID=${ULTRAFEEDER_UUID}
- MLAT_USER=${FEEDER_NAME}
```

For outbound 1090 MHz ADS-B and MLAT sharing, no new container, install script,
account, API key, inbound firewall rule, or exposed Docker port should be
necessary. If the live `/opt/adsb/docker-compose.yml` matches this repository
and Ultrafeeder successfully established its outbound sessions, the station is
probably already feeding both networks.

The remaining work is therefore verification, not integration. A later,
approved maintenance session should compare the live Compose file, inspect
Ultrafeeder's generated connections/logs without exposing secrets, and check
each provider's IP-based feeder-status page.

## What is present in this setup

The stack uses `ghcr.io/sdr-enthusiasts/docker-adsb-ultrafeeder`, with local
1090 MHz decoding, a shared `ULTRAFEEDER_CONFIG`, accurate station coordinates
and altitude supplied from environment variables, and an MLAT hub. This is the
ideal architecture for both networks.

Repository history shows the two ADS-B destinations were present in the initial
Compose commit on 2026-05-29. Their MLAT destinations and `--privacy` flags were
added on 2026-05-30. They are not part of the current uncommitted Compose diff;
the present dirty changes concern UAT and radio experiments.

Important limitation: this review intentionally did not connect to the DietPi
production VM at `10.10.1.71`. The repository describes itself as a reference
copy and identifies `/opt/adsb/docker-compose.yml` on that VM as the source of
truth. Consequently, configuration presence is confirmed in the repository,
while current live connections are not confirmed.

## Compatibility assessment

| Item | Airplanes.live | ADSB.lol |
|---|---|---|
| Existing Ultrafeeder support | Yes | Yes |
| ADS-B endpoint in this repo | `feed.airplanes.live:30004` | `in.adsb.lol:30004` |
| ADS-B format | Beast Reduce Plus | Beast Reduce Plus |
| MLAT endpoint in this repo | `feed.airplanes.live:31090` | `in.adsb.lol:31090` |
| Separate container needed | No | No |
| Account/API key needed to feed | No indication of either for direct Ultrafeeder feeding | No indication of either for direct Ultrafeeder feeding |
| Outbound-only network path | Yes | Yes |
| Current privacy mode | Enabled for MLAT | Enabled for MLAT |
| Integration effort | Already configured; verify only | Already configured; verify only |

The official SDR-Enthusiasts documentation lists exactly these hosts, ports,
and Beast Reduce Plus/MLAT constructs. It also states that the optional MLAT
return-port field is retained mainly for legacy compatibility, so this
repository's shorter MLAT entries are valid. The repository-wide `UUID`
supplies a stable receiver identity, while `MLAT_USER` supplies the site name.

## Airplanes.live

### Feeding

Airplanes.live's own existing-receiver guide installs separate systemd feeder
clients, but that is unnecessary here because Ultrafeeder natively implements
the same two outbound connections. Installing the Airplanes.live script beside
the existing Ultrafeeder connection would risk duplicating the feed and would
add services that this Compose stack does not need.

Its official verification guidance looks for established TCP sessions on ports
30004 and 31090 and directs the operator to `https://airplanes.live/myfeed/`.
MyFeed is IP based and reports Beast and MLAT connection detection. This should
be checked from the station's public internet connection during a later
approved verification session.

### Privacy

The current `--privacy` MLAT flag is intentional and supported. Airplanes.live
says feeder name and approximate location may be displayed publicly unless the
privacy option is enabled; precise coordinates are still required privately
for MLAT. Keeping `--privacy` is the conservative choice. Removing it would be
a policy choice, not a technical requirement.

### Data/API use

If “integration” also means consuming network-wide aircraft data in another
ScheibTribe application, Airplanes.live offers a v2 REST API with searches by
hex, callsign, registration, type, squawk, military/LADD/PIA status, and a
point-radius query up to 250 nautical miles. The published API is currently
available without feeder authentication, limited to one request per second,
non-commercial, and provided without an SLA. This is useful for occasional
lookup/enrichment, but it should not become a hard availability dependency.

## ADSB.lol

### Feeding

ADSB.lol explicitly says an existing ADS-B station can be added without
interfering with the setup and only needs Beast output. Its own scripts or
Docker Toolkit are alternatives for other architectures; adding either here
would duplicate capabilities already built into Ultrafeeder.

The current direct connections match both ADSB.lol and SDR-Enthusiasts
documentation. No additional feeder container is warranted.

### Feeder benefits

ADSB.lol provides feeders with:

- an IP-based “My Map” for aircraft received by the station;
- an MLAT synchronization map;
- an IP-restricted, unfiltered readsb HTTP API (`re-api`);
- experimental network-wide Beast and MLAT output streams.

Because this setup uses `--privacy`, it should not appear on the public MLAT
map. That does not imply MLAT is failing. ADSB.lol's documentation says the
privacy flag hides a station there.

The network-wide raw outputs must **not** be connected back into this
Ultrafeeder instance. ADSB.lol explicitly warns that consumers should use a
separate readsb instance to avoid a feeding loop. They are potentially useful
for a future isolated analytics service, but not for the current decoder/feed
path.

### Data/API use

ADSB.lol also exposes a public v2 aircraft API with point-radius, identity,
type, squawk, military, LADD, and PIA queries. Its published data/API license is
ODbL; data sent to its ingest endpoints is contributed under CC0. The API page
says access is presently free, an API key may be required in the future, and
production consumers should contact the operator so applications are not
unexpectedly broken.

For local dashboards, ADSB.lol is the richer of the two feeder integrations
because feeding unlocks the IP-restricted `re-api` and raw aggregate streams.
Those capabilities are optional and separate from the already-configured
outbound feed.

## UAT/978 MHz nuance

The current uncommitted Compose work adds `dump978` into Ultrafeeder using
`uat_in`. Research confirms that the ADSB.lol Docker Toolkit can support UAT,
but the public direct-Ultrafeeder examples for both networks document their
standard Beast/MLAT feeds rather than guaranteeing how merged UAT/TIS-B content
is handled by each backend. This review therefore does **not** claim that the
present direct connections provide either service with native 978 MHz UAT.

No additional UAT-specific change should be made without checking each
operator's current expectations and validating the generated outbound stream.
This does not affect the high-confidence 1090 MHz ADS-B/MLAT conclusion.

## Operational impact and risk

For the connections already shown in the repository:

- CPU and memory impact should be small: Ultrafeeder already supports multiple
  outbound Beast connections and one MLAT client per network.
- Bandwidth impact is incremental outbound traffic only and should be modest
  for a home ADS-B receiver.
- No host ports need to be published for either provider.
- Accurate latitude, longitude, altitude, stable time synchronization, and a
  stable UUID matter for MLAT.
- The stack runs in a VM. SDR-Enthusiasts warns that VM timing can harm MLAT;
  existing handoff notes also mention unstable FlightAware MLAT timing. ADS-B
  feeding can work normally even if MLAT quality is poor.
- MLAT results must never be retransmitted as original receiver observations.
  The supported Ultrafeeder `mlat`/MLAT-hub design is intended to prevent that;
  a hand-built aggregate-data import into the feeder would be dangerous.
- Both projects are community services with limited/no service guarantees.

## Recommended next step, only after approval

No configuration edit should be the first action. The safest later sequence is:

1. Read-only compare the live `/opt/adsb/docker-compose.yml` with the repository
   entries for both services.
2. Confirm the live environment has nonempty values for the existing UUID,
   feeder name, coordinates, and altitude **without printing their values**.
3. Inspect Ultrafeeder's effective configuration/processes and recent logs for
   both hostnames and connection errors.
4. Confirm established outbound TCP sessions to each provider on 30004 and
   31090.
5. Visit Airplanes.live MyFeed and ADSB.lol My Map/re-api from the feeder's
   public IP.
6. Treat absence from either public MLAT map as expected while `--privacy` is
   enabled; use logs/peer synchronization instead.
7. Only if a connection is absent or rejected, prepare a minimal Compose/env
   correction for review before applying it.

Expected implementation difficulty if the live stack lacks the entries:
approximately a four-line `ULTRAFEEDER_CONFIG` change followed by a controlled
Ultrafeeder recreation and validation. In this repository, even that edit is
already done.

## Bottom line

- **Airplanes.live:** already configured; likely only needs live verification.
- **ADSB.lol:** already configured; likely only needs live verification.
- **Do not install either project's standalone feeder scripts** on top of the
  existing direct Ultrafeeder feeds.
- **Keep `--privacy` unless public station visibility is explicitly desired.**
- **Do not import ADSB.lol's aggregate Beast stream into the feeding decoder.**
- **No action was taken against the production stack.**

## Sources

- SDR-Enthusiasts Ultrafeeder README, direct aggregator configuration:
  https://github.com/sdr-enthusiasts/docker-adsb-ultrafeeder
- SDR-Enthusiasts example Compose:
  https://github.com/sdr-enthusiasts/docker-adsb-ultrafeeder/blob/main/docker-compose.yml
- Airplanes.live existing receiver guide:
  https://airplanes.live/how-to-feed/
- Airplanes.live feeder client:
  https://github.com/airplanes-live/feed
- Airplanes.live MyFeed:
  https://airplanes.live/myfeed/
- Airplanes.live privacy policy:
  https://airplanes.live/privacy/
- Airplanes.live REST API guide:
  https://airplanes.live/api-guide/
- ADSB.lol feeding overview:
  https://www.adsb.lol/docs/get-started/introduction/
- ADSB.lol feeder-only features:
  https://www.adsb.lol/docs/feeders-only/
- ADSB.lol My Map:
  https://www.adsb.lol/docs/feeders-only/my-map/
- ADSB.lol MLAT map:
  https://www.adsb.lol/docs/feeders-only/mlat-map/
- ADSB.lol re-api:
  https://www.adsb.lol/docs/feeders-only/re-api/
- ADSB.lol Beast/MLAT output warning:
  https://www.adsb.lol/docs/feeders-only/beast-mlat-out/
- ADSB.lol API:
  https://api.adsb.lol/
- ADSB.lol privacy and licensing:
  https://www.adsb.lol/privacy-license/
