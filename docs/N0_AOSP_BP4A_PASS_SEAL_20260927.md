# Titan 2 N0-AOSP BP4A Pass Seal

Status: `PASS_WITH_PATCH_QUEUE`

This document records the device-facing meaning of the completed N0-AOSP / BP4A control build. It is a build-control result only, not a flash authorization and not a Titan 2 flashable ROM declaration.

## Canonical stage position

```text
CURRENT_STAGE=N0-AOSP
NEXT_STAGE=N0-B-TREBLE
N1_RESERVED_FOR=SABLEOS_PRODUCT_OVERLAY
N2_RESERVED_FOR=PHYSICAL_TITAN2_VALIDATION
```

## Result

```text
TARGET=generic_system_arm64-bp4a-userdebug
GOAL=systemimage
BUILD_ID=BP4A.251205.006
OUT_DIR=out_sable_t2_n0_aosp_gsi_bp4a
EVIDENCE=/srv/data/sable-build/evidence/titan2/n0-bp4a-rerun/20260927_113543
LUNCH_RC=0
BUILD_RC=0
SYSTEM_IMG_COUNT=2
N0_BP4A_SYSTEMIMAGE=PASS
SKIP_ABI_CHECKS_USED=true
DEVICE_CONTACT=NO
FLASH_AUTHORIZED=NO
ADB_COMMANDS_RUN=NO
FASTBOOT_COMMANDS_RUN=NO
```

## Artifact identity

```text
SYSTEM_IMG=/srv/data/sable-build/workspaces/titan2-n0-a16-20260926_221401/aosp-android16/out_sable_t2_n0_aosp_gsi_bp4a/target/product/mainline_arm64/system.img
SYSTEM_IMG_SIZE=1003M
SYSTEM_IMG_SHA256=16b3eac3bc7c304df19b1cf68031bceae65b425e8e382cadb36bfb924bac1e30
```

## Device interpretation

```text
N0_AOSP_PROVES=AOSP_BP4A_HOST_SOURCE_TOOLCHAIN_CONTROL
N0_AOSP_DOES_NOT_PROVE=TITAN2_BOOT_OR_RUNTIME_COMPATIBILITY
FLASHABLE_TITAN2_ROM=NO
```

N0-B-TREBLE is the next device-relevant stage. It should use the RestlessOS / TrebleDroid product hierarchy directly and a separate output directory. It should not manually graft Treble runtime patches into `generic_system_arm64`.

## Physical device gate

```text
DEVICE_CONTACT=NO
FLASH_AUTHORIZED=NO
PHYSICAL_TITAN2_VALIDATION=NOT_STARTED
```

Physical Titan 2 / Titan 2 Elite validation remains blocked until a qualified SableOS or Treble baseline artifact is explicitly selected and a separate validation plan is approved.
