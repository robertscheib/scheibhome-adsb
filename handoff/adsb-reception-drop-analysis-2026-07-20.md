# ADS-B Reception Drop Analysis

**Date:** 2026-07-20
**Analyst:** Codex
**System:** DietPi ADS-B VM (`10.10.1.71`), ultrafeeder/graphs1090

## Result

The substantially lower maximum-aircraft count and related statistics in June
and July are caused by degraded receiver sensitivity or another RF-path
problem, not by a normal reduction in air traffic.

The transition occurred around May 22-25, 2026. Reception was healthy through
May 21, followed by an approximately two-day outage. When reception returned
around May 25, signal strength, aircraft counts, message rate, and range were
all substantially lower and have remained degraded.

## Quantitative comparison

The comparison uses archived graphs1090 RRD data. The healthy period is May
5-21, and the degraded period is May 25-July 20.

| Metric | Before May 22 | After May 25 | Change |
|---|---:|---:|---:|
| Maximum aircraft with positions | ~106 | ~29 | -73% |
| Average aircraft with positions | 56.5 | 12.4 | -78% |
| Maximum range | 153 NM | 69 NM | -55% |
| Average range | 129 NM | 43 NM | -67% |
| Average locally accepted message rate | 218/sec | 30/sec | -86% |
| Average signal level | -12.7 dBFS | -26.2 dBFS | ~13.5 dB weaker |
| Average peak signal level | -6.8 dBFS | -25.2 dBFS | ~18.4 dB weaker |
| Average noise level | -29.3 dBFS | -39.5 dBFS | ~10.2 dB lower |
| Average tuner gain | 38.0 dB | 49.9 dB | ~11.9 dB higher |

The graphs covering the preceding 180 days show the same abrupt, correlated
change in aircraft count, range, message rate, signal level, and noise floor.
That pattern is inconsistent with seasonality or ordinary daily traffic
variation.

## Key evidence

Automatic gain provides the strongest evidence of an RF-path problem:

- Before the failure, the receiver generally required about 38 dB of gain.
- After the failure, automatic gain moved to approximately the receiver's
  maximum, 49.6-50 dB.
- Despite applying roughly 12 dB more tuner gain, received signals remain
  approximately 13-18 dB weaker.

This suggests roughly 25-30 dB of additional signal loss ahead of, or within,
the SDR. A traffic reduction would reduce the aircraft count but would not
simultaneously lower the received signal and noise levels or force the tuner to
maximum gain.

## Most likely causes

In descending order of likelihood:

1. A loose, damaged, or water-contaminated antenna/coax connector.
2. A failed coax cable or antenna.
3. Loss of power or bias-tee operation for a powered LNA/filter, if one is
   installed.
4. Damage or failure in the RTL-SDR tuner or antenna input.
5. The SDR was reconnected to a different antenna or cable during the May
   22-25 outage.

The remaining short-range reception is compatible with a disconnected or
severely attenuated antenna path: nearby, strong transmitters can still be
received through a damaged path, a short piece of coax, or incidental pickup.

## Secondary findings

The currently running decoder also reports USB/sample-clock timing warnings:

- Repeated `SDR ppm out of specification` warnings.
- Occasional lost USB packets.
- A kernel-recorded RTL-SDR USB disconnect and reconnect on July 15.
- The VM marked its TSC clocksource unstable on July 13 and continued using
  `kvm-clock`.

These conditions can affect MLAT stability and cause intermittent sample loss,
but they do not explain the persistent 13-18 dB signal reduction or the
simultaneous range and noise-floor changes. They should be investigated after
the primary RF-path issue is isolated.

Other unrelated observations:

- The decoder is currently configured for RTL-SDR serial `1090` with a 10 PPM
  frequency correction.
- The receiver is visible to the VM as a Realtek RTL2838 device.
- Current NTP synchronization is healthy.
- Upstream feeder DNS/connection warnings affect delivery to those feeder
  endpoints, not locally measured aircraft, range, signal, or message-rate
  statistics.

## Recommended diagnostic sequence

1. Reseat the antenna connector at the RTL-SDR and every inline adapter.
2. Inspect outdoor and indoor coax connections for looseness, corrosion, water
   ingress, sharp bends, or physical damage.
3. If an LNA or powered filter is present, verify its power supply and bias-tee
   requirements.
4. Test the SDR with a known-good antenna and short coax cable.
5. If reception remains degraded, substitute a known-good RTL-SDR while using
   the existing antenna path.
6. After each physical change, compare the graphs1090 signal, range, message
   rate, aircraft count, noise, and gain graphs. A restored RF path should
   produce an immediate step toward the pre-May values, and automatic gain
   should fall below its current maximum.
7. Once RF reception is restored, investigate the Proxmox USB passthrough and
   VM clock/timing warnings separately to improve MLAT stability.

## Scope and changes

All investigation performed for this report was read-only. No live
configuration, container, receiver, or service state was changed.
