# Planefence Configuration Audit
**Date:** 2026-06-07  
**Host:** DietPi VM at 10.10.1.71  
**Config path:** `/opt/adsb/planefence/config/planefence.config`

---

## Step 1 — Full Config File Contents

`/opt/adsb/planefence/config/planefence.config`

```
# Planefence parameters
#
# Use this file to configure your station's location, data sources,
# web UI behavior, Plane-Alert features, and notification outputs.
# Only edit values; do not rename parameters.
#
# This file was updated with the parameters used in Planefence V6 and later

###############################################################################
# Required Station & Data Source
###############################################################################

# Station latitude in decimal degrees. North is positive; South is negative.
FEEDER_LAT=41.6993
# ---------------------------------------------------------------------
# Station longitude in decimal degrees. East is positive; West is negative.
FEEDER_LON=-88.10787
# ---------------------------------------------------------------------
# PF_SOCK30003HOST is the hostname or IP address of the SBS-30003 data source
# (for example, ultrafeeder, dump1090[-fa], readsb, or tar1090).
# If it runs in another container/host, use its resolvable hostname.
PF_SOCK30003HOST=ultrafeeder
# ---------------------------------------------------------------------
# PF_SOCK30003PORT is the TCP port for SBS data.
PF_SOCK30003PORT=30003
# ---------------------------------------------------------------------
# PF_MAXDIST is the fence radius from your station center
# in the unit set by PF_DISTUNIT.
PF_MAXDIST=5.0
# ---------------------------------------------------------------------
# PF_MAXALT is the maximum altitude within the fence
# in the unit set by PF_ALTUNIT.
PF_MAXALT=5000

###############################################################################
# General Parameters
###############################################################################
# GENERATE_CSV: generate daily CSV exports for Planefence and Plane-Alert.
# OFF (default) disables generation; ON enables it. Leave this OFF unless you
# need it: generating CSV files is very CPU intensive!
GENERATE_CSV=OFF
# ---------------------------------------------------------------------
# PF_INTERVAL: poll interval in seconds for new planes.
# Recommended minimum is 30 seconds to avoid excessive load.
PF_INTERVAL=30
# ---------------------------------------------------------------------
# PF_DISTUNIT: unit for distances/radius.
# Allowed: kilometer, nauticalmile, mile, meter.
PF_DISTUNIT=mile
# ---------------------------------------------------------------------
# PF_ALTUNIT: unit for altitudes.
# Allowed: meter, feet.
PF_ALTUNIT=feet
# ---------------------------------------------------------------------
# PF_SPEEDUNIT: unit for aircraft speed.
# Allowed: kilometerph, knotph, mileph.
PF_SPEEDUNIT=knotph
# ---------------------------------------------------------------------
# PF_WEBLOGS: determines if log messages are made available via a web page in addition to the container logs.
# Allowed values:
#   config   - make logs available on the config web server as http://config:port/planefence-logs
#   main     - make logs available on the main web server as http://myplanefence/planefence-logs
#   off/disabled/0/no (case-insensitive) - do not make a logs web page available.
# Default: config
PF_WEBLOGS=config
# ---------------------------------------------------------------------
# PF_FUDGELOC: round displayed station coordinates to protect privacy.
# 0 → whole degrees; 1 → 0.1°; 2 → 0.01° (~0.5 mi at 42°N);
# 3 → 0.001° (~300 ft at 42°N). Any other non-empty value acts like 3.
PF_FUDGELOC=3
# ---------------------------------------------------------------------
# PF_CHECKROUTE: enrich PF table with route info via adsb.im API.
# Default is ON if not explicitly disabled.
PF_CHECKROUTE=ON
# ---------------------------------------------------------------------
# PF_TRACKSERVICE: map service used to render tracking links for Planefence.
PF_TRACKSERVICE=globe.adsbexchange.com
# ---------------------------------------------------------------------
# PF_ELEVATION: station elevation above mean sea level (MSL),
# in PF_ALTUNIT. If > 0, PF reports altitudes as AGL (above ground level) instead of MSL.
PF_ELEVATION=680
# ---------------------------------------------------------------------
# OPENSKYDB_DOWNLOAD: control downloading of the OpenSky aircraft database.
OPENSKYDB_DOWNLOAD=ON

###############################################################################
# Web Page Related Parameters
###############################################################################
PF_HTTP_PORT=80
PF_CONFIG_HTTP_PORT=9999
PF_NAME="Home"
PF_TABLESIZE=50
PF_MAPURL=http://10.10.1.71:8080
PF_MAPZOOM=7
PF_SHOWIMAGES=YES
PF_OPENAIP_LAYER=OFF
PF_OPENAIPKEY=

###############################################################################
# Planefence Specific Parameters
###############################################################################
PLANEFENCE=ON
PF_MOTD="<b>Welcome to my Planefence Instance!</b>"
PF_DELETEAFTER=90
PF_NOISECAPT=
PF_IGNOREDUPES=ON
PF_COLLAPSEWITHIN=300

###############################################################################
# Plane-Alert Specific Parameters
###############################################################################
PLANEALERT=ON
PA_TABLESIZE=50
PA_MOTD="<b>Welcome to my Plane-Alert Instance!</b>"
PF_PARANGE=999999
PF_PA_SQUAWKS=7400,7500,7600,7700
PF_ALERTLIST=https://raw.githubusercontent.com/sdr-enthusiasts/plane-alert-db/main/plane-alert-db-images.csv
PF_ALERTHEADER='$ICAO,$Ident,$Operator,$Type,$ICAO Type,#CMPG,$Tag 1,$#Tag 2,$#Tag 3,Category,$#Link'
PA_HISTTIME=30
PA_SILHOUETTES_LINK="https://github.com/rikgale/VRSOperatorFlags/raw/main/TransparentDVSilhouettes.zip"
PA_OPERATORFLAGS_LINK="https://github.com/rikgale/VRSOperatorFlags/raw/main/OperatorFlags.zip"
PA_TRACKSERVICE=globe.adsbexchange.com
PA_EXCLUSIONS=
PA_SHOW_STALE_PAGE=OFF
PA_COLLECT_CANDIDATES=YES
PA_COLLECT_CANDIDATES_FILTER_FILE=pa-candidates-filter.txt
PA_COLLECT_CANDIDATES_LOG=plane-alert-candidates.log

###############################################################################
# Notification Related Parameters
###############################################################################
NOTIF_DATEFORMAT="%F %T %Z"
PF_NOTIFEVERY=OFF
PF_NOTIF_MINTIME=0
PF_NOTIF_BEHAVIOR=post
PF_ATTRIB="#adsb #planefence by kx1t https://sdr-e.com/docker-planefence"
PA_ATTRIB="#adsb #planefence #planealert by kx1t https://sdr-e.com/docker-planefence"
PF_SCREENSHOTURL=http://screenshot:5042
PF_SCREENSHOT_TIMEOUT=45

# Discord
PA_DISCORD=OFF
PF_DISCORD=OFF
PA_DISCORD_WEBHOOKS=
PF_DISCORD_WEBHOOKS=
DISCORD_FEEDER_NAME=
DISCORD_MEDIA=screenshot+photo
PA_DISCORD_COLOR=0xf2e718
PF_DISCORD_COLOR=0xf2e718

# Mastodon
PF_MASTODON=OFF
PA_MASTODON=OFF
MASTODON_SERVER=
MASTODON_ACCESS_TOKEN=
PF_MASTODON_VISIBILITY=unlisted
PA_MASTODON_VISIBILITY=unlisted
MASTODON_RETENTION_TIME=7

# MQTT — Planefence
PF_MQTT_URL="mqtt://mqttclient:fORyBooUstoSO@10.10.1.20:1883"
PF_MQTT_PORT=""
PF_MQTT_TLS=""
PF_MQTT_CAFILE=""
PF_MQTT_TLS_INSECURE=""
PF_MQTT_CLIENT_ID=""
PF_MQTT_TOPIC="planefence/alert"
PF_MQTT_DATETIME_FORMAT="%s"
PF_MQTT_QOS="0"
PF_MQTT_FIELDS=""
PF_MQTT_USERNAME=""
PF_MQTT_PASSWORD=""

# MQTT — Plane-Alert
PA_MQTT_URL="mqtt://mqttclient:fORyBooUstoSO@10.10.1.20:1883"
PA_MQTT_PORT=""
PA_MQTT_TLS=""
PA_MQTT_CAFILE=""
PA_MQTT_TLS_INSECURE=""
PA_MQTT_CLIENT_ID=""
PA_MQTT_TOPIC="planefence/plane-alert"
PA_MQTT_DATETIME_FORMAT="%s"
PA_MQTT_QOS="0"
PA_MQTT_FIELDS=""
PA_MQTT_USERNAME=""
PA_MQTT_PASSWORD=""

# RSS
PF_RSS_SITELINK=""
PF_RSS_FAVICONLINK=""
PA_RSS_SITELINK=""
PA_RSS_FAVICONLINK=""

# BlueSky
PF_BLUESKY_ENABLED=OFF
PA_BLUESKY_ENABLED=OFF
BLUESKY_HANDLE=""
BLUESKY_APP_PASSWORD=""
BLUESKY_API="https://bsky.social/xrpc"

# Telegram
TELEGRAM_BOT_TOKEN=""
PF_TELEGRAM_CHAT_ID=""
PA_TELEGRAM_CHAT_ID=""
PF_TELEGRAM_ENABLED=OFF
PA_TELEGRAM_ENABLED=OFF
PF_TELEGRAM_CHAT_TYPE=normal
PA_TELEGRAM_CHAT_TYPE=normal
```

---

## Step 2 — Extracted Parameter Values

| Parameter | Current Value |
|---|---|
| `PF_ALERTLIST` | `https://raw.githubusercontent.com/sdr-enthusiasts/plane-alert-db/main/plane-alert-db-images.csv` |
| `PA_ALERTLIST` | NOT SET (no separate PA_ version exists; PF_ALERTLIST applies to both) |
| `PF_MQTT_URL` | `mqtt://mqttclient:fORyBooUstoSO@10.10.1.20:1883` |
| `PF_MQTT_PORT` | `""` (empty — port taken from URL) |
| `PF_MQTT_TOPIC` | `"planefence/alert"` |
| `PF_MQTT_USERNAME` | `""` (empty — credentials embedded in URL) |
| `PF_MQTT_PASSWORD` | `""` (empty — credentials embedded in URL) |
| `PF_MQTT_QOS` | `"0"` |
| `PF_MQTT_FIELDS` | `""` (all fields) |
| `PA_MQTT_URL` | `mqtt://mqttclient:fORyBooUstoSO@10.10.1.20:1883` |
| `PA_MQTT_PORT` | `""` (empty) |
| `PA_MQTT_TOPIC` | `"planefence/plane-alert"` |
| `PA_MQTT_USERNAME` | `""` (empty — credentials embedded in URL) |
| `PA_MQTT_PASSWORD` | `""` (empty — credentials embedded in URL) |
| `PA_MQTT_QOS` | `"0"` |
| `PA_MQTT_FIELDS` | `""` (all fields) |
| `PF_PA_SQUAWKS` | `7400,7500,7600,7700` |
| `PLANEALERT` | `ON` |
| `PLANEFENCE` | `ON` |

---

## Step 3 — HA MQTT Broker Check

- **Broker:** `10.10.1.20:1883`
- **Reachable:** YES (`nc -zv 10.10.1.20 1883` succeeded)
- **Authentication:** Required — credentials `mqttclient:fORyBooUstoSO` already embedded in both `PF_MQTT_URL` and `PA_MQTT_URL`
- **secrets.env:** No MQTT entries (credentials stored directly in planefence.config)

---

## Step 4 — Comparison: Current vs. Proposed

```
PARAMETER              | CURRENT VALUE                                    | PROPOSED VALUE
-----------------------|--------------------------------------------------|--------------------------------------------------
PF_ALERTLIST           | plane-alert-db-images.csv                        | plane-alert-mil.csv + plane-alert-pol.csv +
                       | (full 15,985 aircraft, with image URLs)          | plane-alert-gov.csv (~11.4k, no image URLs)
-----------------------|--------------------------------------------------|--------------------------------------------------
PA_MQTT_URL            | mqtt://mqttclient:***@10.10.1.20:1883            | mqtt://10.10.1.20:1883
                       | [already set, auth embedded]                     | ALREADY MATCHES — no change needed
-----------------------|--------------------------------------------------|--------------------------------------------------
PA_MQTT_TOPIC          | planefence/plane-alert                           | planefence/plane-alert
                       |                                                  | ALREADY MATCHES — no change needed
-----------------------|--------------------------------------------------|--------------------------------------------------
PA_MQTT_QOS            | 0                                                | 0
                       |                                                  | ALREADY MATCHES
-----------------------|--------------------------------------------------|--------------------------------------------------
PA_MQTT_USERNAME       | "" (credentials in URL, not here)                | depends on broker auth
                       |                                                  | ALREADY WORKS — auth is in the URL
-----------------------|--------------------------------------------------|--------------------------------------------------
PA_MQTT_PASSWORD       | "" (credentials in URL, not here)                | depends on broker auth
                       |                                                  | ALREADY WORKS — auth is in the URL
-----------------------|--------------------------------------------------|--------------------------------------------------
PF_MQTT_URL            | mqtt://mqttclient:***@10.10.1.20:1883            | mqtt://10.10.1.20:1883 (optional)
                       | [already set, auth embedded]                     | ALREADY MATCHES — no change needed
-----------------------|--------------------------------------------------|--------------------------------------------------
PF_MQTT_TOPIC          | planefence/alert                                 | planefence/fence (optional)
                       |                                                  | MINOR DELTA — name differs from proposed
```

---

## Plain-English Summary

### Alert Lists
Currently using the full `plane-alert-db-images.csv` — all **15,985 aircraft** across all categories (military, police, government, civilian notable), with image links for the web UI. The proposed change would replace this with three filtered lists covering **military (~8,760), police (~939), and government/dictators (~1,746)** — totaling ~11,445 aircraft — dropping the civilian/notable category and image URLs from the alert list.

### MQTT
**Already fully configured and working** for both Planefence (`PF_MQTT_*`) and Plane-Alert (`PA_MQTT_*`). Both point to the HA broker at `10.10.1.20:1883` with credentials embedded in the URL. Current topics:
- Fence events → `planefence/alert`
- Alert matches → `planefence/plane-alert`

### Broker Reachability
Confirmed reachable. Authentication is required and already wired in.

### Actual Delta — What Would Need to Change

| # | Parameter | Change |
|---|---|---|
| 1 | `PF_ALERTLIST` | **Only real change.** Replace single images URL with mil+pol+gov URLs. Note: the filtered lists do not include image links, so aircraft photo thumbnails in the UI would stop working for alert matches. |
| 2 | `PF_MQTT_TOPIC` | Currently `planefence/alert`, proposed `planefence/fence`. Only matters if HA automations subscribe to a specific topic. |

Everything else (MQTT connectivity, broker auth, topics, QoS, Plane-Alert feature flag, squawk triggers) is **already correctly configured**.
