# Titan 2 E3 / N0 deployment gate

Status: **closed**

This document defines the gate that must open before a first SableOS E3/N0
Titan 2 deployment is attempted.

```text
DEVICE=titan2
MILESTONE=N0
DEPLOYMENT_GATE=CLOSED
E3_DEPLOYMENT_AUTHORIZED=NO
FLASH_PUBLIC=NO
BUILD_IMAGE_PUBLIC=NO
```

## Gate prerequisites

Before deployment, all of the following must be true:

```text
public_composition_placeholder=MERGED
private_device_skeleton=MERGED
stock_basis_bound=YES
artifact_kind_decided=YES
first_sable_artifact_exists=YES
artifact_hash_recorded=YES
artifact_manifest_created=YES
restore_path_verified=YES
userdata_wipe_policy_explicit=YES
avb_strategy_explicit=YES
serial_targeting_required=YES
```

## Titan 2 flash model

Titan 2 must not inherit Pixel/Panther flashing assumptions. The N0/N1 flash
policy is recorded in [N0_N1_FLASH_PLAN_LEDGER.md](N0_N1_FLASH_PLAN_LEDGER.md)
and remains fail-closed.

```text
PIXEL_FASTBOOT_ASSUMPTIONS_ALLOWED=NO
PANTHER_ARTIFACT_FLASH_TO_TITAN2_ALLOWED=NO
FASTBOOT_BOOT=UNSUPPORTED
DSU=UNAVAILABLE_ON_TESTED_STOCK_BUILD
FASTBOOTD_MUST_BE_VERIFIED_BY_GETVAR_IS_USERSPACE_YES=YES
USERDATA_WIPE_IMPLICIT_ALLOWED=NO
WRITE_CAPABLE_FLASH_SCRIPT=NO
```

## Safety boundaries

The first E3/N0 deployment must not:

- silently wipe userdata;
- assume Panther preserved-data flashing applies;
- flash without a current stock restore plan;
- rely on ambiguous `adb` or `fastboot` target selection;
- relock the bootloader;
- claim production reproducibility;
- claim full device support from first boot alone.

## First deployment acceptance

The first deployment is successful only if it records at minimum:

```text
artifact_identity
stock_basis_identity
command_log
active_slot_before_after
boot_completed
adb_available
verified_boot_state
first_display_state
physical_keyboard_minimum_input
failure_or_restore_result
```

## Current gate state

```text
DEPLOYMENT_GATE=CLOSED
REASON=first_sable_artifact_absent_and_artifact_kind_undecided
```
