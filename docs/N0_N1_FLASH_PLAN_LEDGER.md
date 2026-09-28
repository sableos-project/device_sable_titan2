# Titan 2 N0/N1 flash plan ledger

Status: **public deployment boundary / fail closed**

This document records the public Titan 2 N0/N1 flash-policy boundary. It does
not enable public build-image, public flashing or a write-capable deployment
path.

```text
DEVICE=titan2
MILESTONE=N0_N1
TITAN2_N0_N1_FLASH_PLAN_LEDGER=YES
DEPLOYMENT_GATE=CLOSED
FLASH_PUBLIC=NO
BUILD_IMAGE_PUBLIC=NO
WRITE_CAPABLE_FLASH_SCRIPT=NO
PRODUCTION_REPRODUCIBILITY_CLAIM=NO
```

## Non-Pixel flash model

Titan 2 deployment must not inherit Pixel/Panther flashing assumptions.

```text
PIXEL_FASTBOOT_ASSUMPTIONS_ALLOWED=NO
PANTHER_ARTIFACT_FLASH_TO_TITAN2_ALLOWED=NO
TREBLE_PORTABILITY_LANE=YES
FASTBOOT_BOOT=UNSUPPORTED
DSU=UNAVAILABLE_ON_TESTED_STOCK_BUILD
FASTBOOTD=SUPPORTED
FASTBOOTD_MUST_BE_VERIFIED_BY_GETVAR_IS_USERSPACE_YES=YES
DYNAMIC_PARTITIONS=true
VIRTUAL_AB=true
RECOVERY_PATH=vendor_boot_recovery_vendor_ramdisk
```

## Pre-write requirements

No public or private write-capable script should be created until the reviewed
SableOS-side and Titan research-side records contain current values for:

```text
RESTORE_REPORT=REQUIRED
BOOTLOADER_PREFLIGHT=REQUIRED
FASTBOOTD_PREFLIGHT=REQUIRED
SNAPSHOT_UPDATE_STATUS=none
ARTIFACT_SHA256=REQUIRED
ARTIFACT_LOGICAL_BYTES=REQUIRED
SYSTEM_CURRENT_BYTES=REQUIRED
LP_ACTION=direct-flash|reviewed-resize
AVB_ACTION=REQUIRED
USERDATA_POLICY=preserve|explicit-wipe-approved
EXPLICIT_TITAN_SERIAL=REQUIRED
ACTIVE_SLOT=REQUIRED
RESTORE_PATH_VERIFIED=REQUIRED
```

## Userdata and recovery

```text
USERDATA_WIPE_IMPLICIT_ALLOWED=NO
USERDATA_WIPE_REQUIRES_EXPLICIT_APPROVAL=YES
BOUNDED_STOCK_RECOVERY_PATH_REQUIRED=YES
```

## N0 and N1 relationship

N0 is the first controlled userspace bring-up milestone. N1 must not skip the
Titan-specific flash gates because N0 has booted once.

```text
N0_MINIMUM_BOOT_GATE_REQUIRED=YES
N1_REQUIRES_N0_EVIDENCE=YES
N1_REUSES_TITAN_FLASH_GATES=YES
N1_SKIP_AVB_LP_USERDATA_REVIEW_ALLOWED=NO
```

## Current state

```text
FIRST_SABLE_BOOT=NOT_RUN
PUBLIC_FLASH_PATH=NO
DEPLOYMENT_GATE=CLOSED
```
