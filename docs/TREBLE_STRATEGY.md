# Titan 2 Treble strategy

Status: **strategy accepted / artifact absent**

## Decision

Titan 2 enters SableOS through the Treble portability lane, not the Pixel
reference lane.

```text
DEVICE=titan2
MILESTONE=N0_A16
ANDROID_RELEASE=16
PLATFORM_SDK=36
ARTIFACT_KIND=gsi-system-image
FIRST_SUBSTRATE=AOSP16_CLEAN_GSI
RESTLESSOS_ROLE=REFERENCE_AND_FUTURE_FORK
FIRST_SABLE_ARTIFACT=ABSENT
FIRST_SABLE_BOOT=NOT_RUN
FLASH_PUBLIC=NO
```

## Why not latest Pixel first

Titan-family vendor firmware can lag Pixel releases. That lag is treated as a
product and compatibility constraint. Titan 2 N0 should prove stock
vendor/kernel/firmware compatibility before chasing a newer Pixel Android
baseline.

## Why AOSP16 first

The first Titan 2 experiment should isolate the smallest useful variable:

```text
Can stock Titan 2 vendor/kernel/firmware accept a clean Android 16 ARM64 GSI
system image with Sable userspace integration layered incrementally?
```

AOSP16 clean GSI comes before RestlessOS because it reduces policy and hardening
variables during first boot.

## RestlessOS role

RestlessOS is valuable as:

- a Treble/GSI compatibility reference;
- a future Sable upstream-tracking fork;
- a comparison point after clean AOSP16 boot/build evidence exists.

RestlessOS is not:

- the first boot dependency;
- a source of prebuilt Sable release artifacts;
- a source of inherited GrapheneOS/RestlessOS security claims;
- Titan-specific deployment policy.

Planned common fork:

```text
sableos-project/treble_restlessos
```

## Deployment boundary

A successful build does not authorize flash. E3/N0 deployment remains closed
until artifact preflight proves:

```text
SPARSE_OR_RAW_SYSTEM_IMAGE_VALID=YES
LOGICAL_PARTITION_FIT_VALIDATED=YES
AVB_STRATEGY_EXPLICIT=YES
RESTORE_PATH_VERIFIED=YES
USERDATA_WIPE_POLICY_DECIDED=YES
DEVICE_SERIAL_BOUND=YES
```
