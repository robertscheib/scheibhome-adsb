# Dongle 2 VHF ACARS Test — 2026-07-24

## Setup

- SDR: orange Dongle 2 (`0bda:2832`, duplicate serial `978`)
- Proxmox physical USB path: `1-13.4.3`
- Antenna: VHF antenna supplied by Robert
- Gain:
  - VDL2: 40 dB
  - Legacy ACARS: decoder AGC (`-10`)
- PPM correction: 0
- Output remained local; no messages were forwarded upstream.

## VDL2 test

The SDR-Enthusiasts `dumpvdl2` container monitored these channels
simultaneously:

- 136.650 MHz
- 136.700 MHz
- 136.800 MHz
- 136.975 MHz

The decoder ran for exactly five measured minutes. It tuned successfully at
1.26 Msps, used a 346 kHz bandwidth, and reported no USB or decoder errors.

Result: **0 decoded VDL2 records**.

## Legacy ACARS test

The SDR-Enthusiasts `acarsdec` container monitored:

- 130.025 MHz
- 130.450 MHz
- 131.125 MHz
- 131.550 MHz

It tuned successfully at 1.584 Msps with a 1549 kHz bandwidth and decoded all
four channels simultaneously.

- Initial antenna position: approximately two minutes, 0 records.
- Robert then moved the antenna.
- Moved position: approximately 2 minutes 31 seconds, 0 records.

Result: **0 decoded legacy ACARS records**.

## Interpretation

Both decoder stacks and the USB device initialized normally, so the result
does not indicate a software or USB failure. Short ACARS/VDL2 windows can be
quiet, but receiving no frames on either protocol across the tested channels
makes the VHF antenna, cable, connector, position, or frequency correction the
next area to validate.

The most useful next diagnostic is a continuous known VHF signal, such as a
local NOAA weather-radio transmitter or airport ATIS/AWOS. Successful
reception would validate the VHF RF path before another longer ACARS test.

## Cleanup

- Temporary `dumpvdl2` and `acarsdec` containers were removed.
- Dongle 2's temporary Proxmox passthrough was removed.
- VM 102 retains only the blue production USB mapping.
- Production Ultrafeeder remained healthy.
- Dongle 2 remains physically connected to the repositioned VHF antenna but
  is not passed through to the ADS-B VM.
