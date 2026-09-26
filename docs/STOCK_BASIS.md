# Titan 2 N0 stock basis

Status: **required before artifact binding**

This document records what must be bound before a Titan 2 N0 Sable artifact can
be described as device-specific.

```text
DEVICE=titan2
MILESTONE=N0
STOCK_BASIS_STATUS=PLACEHOLDER
FIRMWARE_COMMITTED=NO
PRIVATE_IDENTIFIERS_COMMITTED=NO
ARTIFACT_BOUND=NO
```

## Required stock basis fields

A future N0 artifact must identify the exact stock basis it expects:

```text
retail_variant=
region=
stock_build_display=
stock_incremental=
android_release=
security_patch=
bootloader_fastboot_product=
soc=
kernel_version=
vendor_api=
vndk=
active_slot_at_capture=
locked_or_unlocked_state=
```

## Required firmware/vendor binding

The N0 record must bind to hashes or private evidence references for:

```text
boot
init_boot
vendor_boot
dtbo
vbmeta
vbmeta_system
vbmeta_vendor
vendor
vendor_dlkm
odm
odm_dlkm
product if reused
system_ext if reused
super metadata
LP partition layout
```

Do not commit those images here. Commit only normalized, redacted conclusions and
hashes when publication-safe.

## Required restore proof before deployment

Before E3/N0 deployment is allowed, private evidence must show:

```text
stock_restore_source_available=YES
restore_critical_hashes_recorded=YES
fastbootd_available=YES
active_slot_recorded=YES
super_metadata_recorded=YES
avb_strategy_chosen=YES
known_good_reboot_to_stock=YES
userdata_wipe_policy_explicit=YES
```

## Current status

```text
TITAN2_STOCK_BASIS=PLACEHOLDER
FIRST_SABLE_ARTIFACT=ABSENT
FIRST_SABLE_BOOT=NOT_RUN
E3_DEPLOYMENT_AUTHORIZED=NO
```
