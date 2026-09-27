# Titan 2 N0-AOSP and N0-B-TREBLE Tracking

This device repository tracks the Titan 2 / Titan 2 Elite implications of the staged GSI build plan. The detailed cross-project ledger lives in `aimindseye/sableos`; this file records the device-facing interpretation.

## Current status

```text
CURRENT_STAGE=N0-AOSP
CURRENT_TARGET=generic_system_arm64-bp4a-userdebug
CURRENT_GOAL=systemimage
CURRENT_OUT_DIR=out_sable_t2_n0_aosp_gsi_bp4a
DEVICE_CONTACT=NO
FLASH_AUTHORIZED=NO
PHYSICAL_TITAN2_VALIDATION=NOT_STARTED
```

`N0-AOSP` is a build-control stage only. It is not the Titan 2 flashable target.

## Canonical stage interpretation for Titan 2

The RestlessOS / TrebleDroid baseline is `N0-B-TREBLE`, not `N1-TREBLE`.

| Stage | Titan 2 meaning | Expected result |
| --- | --- | --- |
| `N0-AOSP` | Proves Android 16 / BP4A AOSP host/source/toolchain can build `system.img` | Build control artifact only |
| `N0-B-TREBLE` | First hardware-capable GSI baseline using RestlessOS / TrebleDroid product hierarchy | Candidate generic Treble system image |
| `N1-SABLE` | SableOS product overlay inheriting from the Treble baseline | SableOS GSI candidate |
| `N2-TITAN2` | Physical Titan 2 / Titan 2 Elite validation | Device evidence and remediation list |

## Why generic_system_arm64 is not the Titan 2 target

`generic_system_arm64-bp4a-userdebug` is intentionally used as the N0-AOSP control build. It does not include the TrebleDroid / RestlessOS hardware compatibility layer required for MediaTek Treble devices.

Do not treat the N0-AOSP artifact as a flashable Titan 2 ROM.

## N0-B-TREBLE handoff requirements

Before starting device-relevant baseline validation, identify and record:

```text
RESTLESS_TREBLE_PRODUCT_NAME=<pending>
RESTLESS_TREBLE_PRODUCT_MAKEFILE=<pending>
N0_B_OUT_DIR=out_sable_t2_n0b_treble_gsi
DEVICE_CONTACT=NO
FLASH_AUTHORIZED=NO
```

The N0-B build should use the RestlessOS / TrebleDroid product hierarchy directly. Do not manually graft Treble runtime patches into `generic_system_arm64`.

## N1-SABLE handoff requirements

The SableOS product overlay should be introduced after N0-B-TREBLE is understood.

Expected overlay ownership:

```text
Titan 2 keyboard maps: device/sable/gsi or vendor/sable overlay
Square display overlays: device/sable/gsi or vendor/sable overlay
Sable packages/defaults: vendor/sable and Sable product makefile
Artifact allowlists: Sable product makefile only
AOSP build/make base files: no Sable product ownership
```

## Physical device gate

No physical device contact or flash attempt is authorized by this tracking update.

```text
ADB_COMMANDS_RUN=NO
FASTBOOT_COMMANDS_RUN=NO
FLASH_AUTHORIZED=NO
```

Physical Titan 2 validation begins only after a qualified N1-SABLE artifact exists and a separate validation plan is approved.
