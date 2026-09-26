# Titan 2 N0 artifact decision

Status: **undecided / experiment required**

Titan 2 N0 must not inherit the Panther artifact contract by assumption. Panther
R9 uses a qualified target-files/full-image path. Titan 2 N0 may need a different
artifact kind bound to the stock kernel/vendor/ODM/firmware basis.

```text
DEVICE=titan2
MILESTONE=N0
ARTIFACT_KIND=UNDECIDED
BUILD_IMAGE_PUBLIC=NO
FIRST_SABLE_ARTIFACT=ABSENT
FIRST_SABLE_BOOT=NOT_RUN
```

## Candidate artifact kinds

### 1. `gsi-system-image`

Use if the safest first experiment is a bounded system image using stock kernel,
vendor, ODM and firmware partitions.

Required evidence:

```text
Treble/VNDK compatibility checked
system partition sizing known
AVB handling explicit
userdata wipe requirement known
stock restore path proven
```

### 2. `generated-super-image`

Use only if the first Sable userspace must be deployed through a generated super
image and logical-partition sizing is fully understood.

Required evidence:

```text
LP metadata captured
super free-space and group constraints known
COW/snapshot state understood
restore-critical stock super path available
fastbootd flashing path validated
```

### 3. bounded `system/product/system_ext` bundle

Use if Sable needs a bounded multi-partition userspace bundle while preserving
stock vendor/ODM/firmware.

Required evidence:

```text
partition set justified
per-partition size constraints known
per-partition AVB impact known
product/system_ext ownership reviewed
rollback and restore path documented
```

## Decision rule

Do not choose the artifact kind from community recipes alone. Choose from actual
Titan 2 evidence:

```text
LP metadata
super layout
AVB topology
restore sources
userdata wipe behavior
first-boot service/HAL comparison
```

## Current decision

```text
ARTIFACT_KIND_DECISION=DEFERRED_TO_EXPERIMENT
PUBLIC_BUILD_IMAGE=FAIL_CLOSED
PUBLIC_FLASH=FAIL_CLOSED
```
