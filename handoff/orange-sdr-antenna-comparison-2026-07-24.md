# Orange SDR and Antenna Comparison — 2026-07-24

## Purpose

Compare the old ADS-B Exchange orange RTL-SDR with its replacement. Each
dongle was connected to a new antenna of the same model and decoded 1090 MHz
traffic simultaneously.

## Hardware identity

The permanent test labels are:

| Dongle | Antenna | Description | Proxmox USB path | USB ID | Serial |
|---|---|---|---|---|---|
| 1 | A | Old orange SDR with new matching-model antenna | `1-13.4.1` | `0bda:2832` | `978` |
| 2 | B | Replacement orange SDR used in the first comparison, with matching-model antenna | `1-13.4.3` | `0bda:2832` | `978` |
| 3 | C | Third orange SDR with different antenna | `1-13.1.2` | `0bda:2832` | `978` |

All three orange dongles have duplicate serial numbers, so path-specific
Proxmox passthrough is used for testing. No EEPROM serials were changed.

## Method

- Both receivers ran the same `readsb` build.
- Both used 49.6 dB fixed gain and zero PPM correction.
- Both started together and ran for approximately 7 minutes 57 seconds.
- Aircraft data was sampled every 30 seconds.
- The blue production 1090 receiver remained online as a traffic control.
- The orange antennas were approximately two inches apart on a shared metal
  baking sheet, using their magnetic bases.

## Results

| Metric | Old pair | New pair | New-pair difference |
|---|---:|---:|---:|
| Raw messages | 131,771 | 174,768 | +32.6% |
| Approximate messages/second | 276.2 | 366.4 | +32.6% |
| Unique aircraft observed through final complete sample | 101 | 122 | +20.8% |
| Maximum observed range | 78.9 nmi | 104.3 nmi | +32.2% |
| Mean snapshot RSSI | -19.65 dBFS | -19.04 dBFS | 0.61 dB stronger |
| Decoder/USB/tuner errors | 0 | 0 | Equal |

At cleanup, the old pair had 65 active aircraft and the new pair had 77.

## Interpretation

The old orange SDR is functional. With the new antenna it immediately decoded
substantial 1090 traffic and produced no tuner or USB errors. This overturns
the earlier tentative bad-dongle diagnosis and points to the prior antenna or
RF path as the cause of its zero-message tests.

In this physical arrangement, the new dongle/antenna pair materially
outperformed the old pair. The result does not yet isolate dongle performance
from antenna position or individual antenna variation. Two-inch antenna
spacing is about 0.19 wavelength at 1090 MHz, close enough for strong mutual
coupling and pattern distortion.

The recommended follow-up is a crossover run: leave both antennas and cables
in the same positions, swap only the dongles between them, and repeat the same
test. A performance advantage that follows the new dongle indicates a dongle
difference; an advantage that remains at the same antenna position indicates
antenna, cable, placement, or coupling bias.

## Cleanup state

- Both temporary test decoders were removed.
- Both temporary Proxmox USB passthrough mappings were removed.
- VM 102 retains only its production blue 1090 USB mapping.
- The production Ultrafeeder remained healthy throughout.
- `dump978` remains intentionally stopped while the orange hardware is being
  tested with 1090 antennas.

## Three-pair comparison on separate ground planes

Dongles 1, 2, and 3 were tested simultaneously with Antennas A, B, and C,
respectively. Each magnetic-base antenna was placed on its own metal baking
sheet. The pairs occupied the same general positions used in the earlier run.

The measurement interval was exactly five minutes. Counters accumulated before
the first measurement sample were subtracted from the message totals.

| Metric | 1+A | 2+B | 3+C |
|---|---:|---:|---:|
| Messages in five minutes | 120,601 | 112,395 | 177,292 |
| Messages/second | 402.0 | 374.6 | 591.0 |
| Unique aircraft | 107 | 101 | 147 |
| Unique positioned aircraft | 96 | 87 | 127 |
| Maximum observed range | 114.9 nmi | 120.9 nmi | 162.4 nmi |
| Mean snapshot RSSI | -19.52 dBFS | -19.94 dBFS | -15.57 dBFS |
| Decoder/USB/tuner errors | 0 | 0 | 0 |

Pair 1+A decoded 7.3% more messages than 2+B. Pair 3+C decoded 47.0%
more than 1+A and 57.7% more than 2+B. Antenna C's substantially stronger
RSSI is consistent with the much higher message rate and range.

The separate ground planes materially changed the 1+A versus 2+B result:
1+A slightly outperformed 2+B, whereas 2+B led by 32.6% when the two antennas
were two inches apart on a shared sheet. This confirms that the first
comparison was strongly affected by antenna placement or mutual coupling.

The test compares fixed dongle/antenna pairs. Rotating dongles among Antennas
A, B, and C is still required to isolate dongle performance from antenna
performance.

## Rotation run 2

The antennas were rotated while their separate baking-sheet ground planes and
general test positions were retained:

- Dongle 1 moved from Antenna A to B.
- Dongle 2 moved from Antenna B to C.
- Dongle 3 moved from Antenna C to A.

The measurement interval was exactly five minutes with the same decoder
software and settings as the first three-pair test.

| Metric | 1+B | 2+C | 3+A |
|---|---:|---:|---:|
| Messages in five minutes | 103,638 | 140,867 | 96,357 |
| Messages/second | 345.5 | 469.6 | 321.2 |
| Unique aircraft | 100 | 127 | 98 |
| Unique positioned aircraft | 78 | 102 | 78 |
| Maximum observed range | 111.8 nmi | 128.4 nmi | 108.0 nmi |
| Mean snapshot RSSI | -20.60 dBFS | -16.38 dBFS | -18.66 dBFS |
| Decoder/USB/tuner errors | 0 | 0 | 0 |

Pair 2+C decoded 35.9% more messages than 1+B and 46.2% more than 3+A.
Pair 1+B decoded 7.6% more than 3+A.

The performance advantage followed Antenna C from Dongle 3 in run 1 to
Dongle 2 in run 2. Antenna C accounted for 43.2% of all three receivers'
messages in run 1 and 41.3% in run 2. Antennas A and B each remained near
28-30% of their run totals. This is strong evidence that Antenna C, rather
than Dongle 3, caused the large initial advantage.

One final rotation (`1+C`, `2+A`, `3+B`) will put every dongle on every
antenna once and complete the crossover matrix needed to compare dongles
independently of antennas.

## Rotation run 3

The final rotation placed Antenna C on Dongle 1, Antenna A on Dongle 2, and
Antenna B on Dongle 3. The exact five-minute method and decoder settings were
unchanged.

| Metric | 1+C | 2+A | 3+B |
|---|---:|---:|---:|
| Messages in five minutes | 155,835 | 95,841 | 108,235 |
| Messages/second | 519.5 | 319.5 | 360.8 |
| Unique aircraft | 144 | 97 | 113 |
| Unique positioned aircraft | 122 | 78 | 96 |
| Maximum observed range | 116.5 nmi | 95.9 nmi | 106.8 nmi |
| Mean snapshot RSSI | -15.65 dBFS | -17.92 dBFS | -18.97 dBFS |
| Decoder/USB/tuner errors | 0 | 0 | 0 |

Antenna C led for the third consecutive run, this time on Dongle 1. This
completes the crossover matrix and confirms that Antenna C's advantage is
independent of which orange dongle receives through it.

## Completed crossover analysis

### Raw message matrix

| Dongle | Antenna A | Antenna B | Antenna C |
|---|---:|---:|---:|
| 1 | 120,601 | 103,638 | 155,835 |
| 2 | 95,841 | 112,395 | 140,867 |
| 3 | 96,357 | 108,235 | 177,292 |

Each dongle used each antenna once, and each antenna appeared once in each
run. Geometric means reduce the effect of different overall traffic volumes
between the three five-minute windows.

### Dongle comparison, balanced across antennas and runs

| Dongle | Geometric-mean messages | Geometric-mean unique aircraft |
|---|---:|---:|
| 1 | 124,885 | 115.5 |
| 2 | 114,913 | 107.6 |
| 3 | 122,738 | 112.2 |

Dongle 1 led Dongle 3 by 1.7% and Dongle 2 by 8.7% in adjusted message
volume. Dongle 3 led Dongle 2 by 6.8%. These are relatively small differences
compared with the antenna effect, and all three dongles completed every run
without tuner, USB, or decoder errors.

**Dongle verdict:** Dongle 1 is healthy. It is statistically comparable to
Dongle 3 and was the best of the three by the balanced message metric. The
earlier zero-message behavior was caused by the old RF path, not Dongle 1.

### Antenna comparison, balanced across dongles and runs

| Antenna | Geometric-mean messages | Geometric-mean unique aircraft | Mean RSSI |
|---|---:|---:|---:|
| A | 103,656 | 100.6 | -18.70 dBFS |
| B | 108,030 | 104.5 | -19.84 dBFS |
| C | 157,297 | 139.0 | -15.87 dBFS |

Antenna C produced 51.7% more adjusted messages than A and 45.6% more than B.
B produced 4.2% more than A. C also detected approximately 33-38% more unique
aircraft and had substantially stronger average signals.

**Antenna verdict:** C is the clear winner. A and B are broadly similar, with
B holding a small message-volume advantage. The removed original antenna,
cable, or connector was defective or severely attenuated; the testing does
not isolate which component within that old RF path failed.

## Blue production dongle follow-up

Known-good Antennas B and C were connected in turn to the filtered blue
production 1090 dongle without changing the production decoder configuration.

| Pair | Duration | Messages | Messages/second | Unique aircraft | Maximum range | Mean RSSI |
|---|---:|---:|---:|---:|---:|---:|
| Blue+B | 5 minutes | 13,728 | 45.8 | 19 | 27.4 nmi | -30.28 dBFS |
| Blue+C | 2 minutes | 4,181 | 34.8 | 13 | 28.4 nmi | -31.35 dBFS |

The blue+C test was stopped early at Robert's request because it had already
reproduced the same severe reception deficit seen with B. For comparison,
Antenna B delivered approximately 345-361 messages/second on orange dongles,
and Antenna C delivered approximately 470-591 messages/second. The production
blue receiver was at its maximum 49.6 dB gain and did not log USB errors.

**Blue-dongle verdict:** The blue dongle or its immediate RF input path is
faulty. Swapping from its original antenna to two independently proven
antennas did not restore reception. Likely internal faults include its SMA
input connection, filter, LNA, or tuner path.

The blue production decoder remained running throughout. At the end of
testing, the blue dongle was physically connected to Antenna C.

## UAT confirmation with 1+C

Dongle 1 and Antenna C were also tested at 978 MHz for exactly five minutes
using an isolated `dump978` container:

- 8,843 messages during the measured interval, or 29.5 messages/second.
- Up to 60 aircraft entries were present during the run.
- The final live snapshot contained one direct UAT ADS-B aircraft and 33
  recent TIS-B rebroadcast tracks.

This confirms that Dongle 1 and Antenna C receive UAT successfully. The old
zero-UAT result was caused by the removed RF path, not Dongle 1 or the UAT
software configuration. The temporary UAT decoder and USB passthrough were
removed after the test; persistent `dump978` remains stopped.

### UAT test after repositioning 1+C

After Dongle 1 and Antenna C were moved to a new physical position, the same
isolated UAT test was repeated for exactly two minutes:

| Metric | Prior position | Repositioned |
|---|---:|---:|
| Messages/second | 29.5 | 0.47 |
| Messages in measured interval | 8,843 in 5 min | 57 in 2 min |
| Maximum simultaneous recent aircraft | At least 34 in final snapshot | 2 |
| Direct UAT aircraft | Present | Up to 2 |
| TIS-B tracks | 33 in final snapshot | 0 |

The repositioning reduced the UAT message rate by approximately 98.4% and
eliminated reception of the TIS-B ground-station traffic. Because the dongle,
antenna, gain, PPM, decoder, and VM path were unchanged, the new physical
location or antenna orientation is unsuitable for UAT reception. Restoring
the former position is recommended.
