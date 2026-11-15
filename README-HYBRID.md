# MeshCore Hybrid for XIAO ESP32-S3 + Wio-SX1262

Experimental firmware that combines the MeshCore repeater and Room Server on
one Seeed XIAO ESP32-S3 with Wio-SX1262.

## Current version

`v1.18.0-hybrid-test7-rxdiag`

Based on upstream MeshCore commit `75b04ef`.

## Features

- Repeater and Room Server active at the same time
- Separate identities, ACL files, shared secrets, and names for both roles
- Current MeshCore v1.18 routing, scoped flooding, region handling, and path logic
- `discover.neighbors` support
- Packet, radio, and core statistics available through local and remote admin CLI
- Room system posts through `room.post <text>`
- ESP32-S3 at 80 MHz
- Light sleep with LoRa DIO1 wake-up
- 30-minute local advert interval
- Wi-Fi OTA removed; Bluetooth is not started
- Power saving defaults to off for reliable continuous repeater reception
- 22 dBm transmit power and boosted receive gain retained
- Complete 8 MB merged image layout: bootloader `0x0000`, partition table
  `0x8000`, OTA metadata `0xE000`, application `0x10000`

## Build

Install PlatformIO, then run from the repository root:

```sh
pio run -e Xiao_S3_WIO_repeater_room_hybrid -t mergebin
```

Generated files:

```text
.pio/build/Xiao_S3_WIO_repeater_room_hybrid/firmware.bin
.pio/build/Xiao_S3_WIO_repeater_room_hybrid/firmware-merged.bin
```

## Flash

Application-only update that preserves settings:

```sh
esptool --chip esp32s3 --port /dev/cu.usbmodem101 --baud 460800 \
  write-flash 0x10000 \
  .pio/build/Xiao_S3_WIO_repeater_room_hybrid/firmware.bin
```

Clean installation that erases all settings and keys:

```sh
esptool --chip esp32s3 --port /dev/cu.usbmodem101 erase-flash
esptool --chip esp32s3 --port /dev/cu.usbmodem101 --baud 460800 \
  write-flash 0x0 \
  .pio/build/Xiao_S3_WIO_repeater_room_hybrid/firmware-merged.bin
```

## Name commands

```text
get name
get repeater.name
get room.name
set repeater.name DE-NW-PB-Lieth-G1
set room.name DE-NW-PB-Lieth-G1-R
set name DE-NW-PB-Lieth-G1
```

`set name <base>` sets the repeater to `<base>` and the Room Server to
`<base>-R`. Individual commands change only the selected identity. Names are
stored persistently and advertised immediately after a change.

## Useful commands

```text
get role
get radio
get repeat
powersaving
discover.neighbors
neighbors
room.post Test message
stats-core
stats-radio
stats-packets
```

## Security

The PlatformIO environment contains development defaults for the admin and
Room passwords. Change both before deploying an internet-published build or a
node in an untrusted mesh. Never commit exported private keys, device backups,
or `/prefs.json` files.

Private-key commands are deliberately limited to the local serial console.

## Status

This is experimental firmware. The build and ESP32-S3 image headers have been
validated, but long-term radio, power-saving, and combined Repeater/Room
operation must be tested on real hardware.

## Upstream and license

Based on [meshcore-dev/MeshCore](https://github.com/meshcore-dev/MeshCore).
The original project and these modifications use the repository's MIT license.
