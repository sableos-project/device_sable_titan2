# SableOS device_sable_titan2

Status: **N0_A16 strategy accepted / public build still fail-closed**

This repository holds the public Titan 2 device adaptation boundary for SableOS.
It remains documentation-only until the first Titan 2 artifact is built,
verified and explicitly promoted into public build tooling.

```text
DEVICE=titan2
MILESTONE=N0_A16
REPOSITORY_STATUS=STRATEGY_DOCUMENTED
ARTIFACT_KIND=gsi-system-image candidate
BUILD_IMAGE_PUBLIC=NO
FLASH_PUBLIC=NO
FIRST_SABLE_ARTIFACT=ABSENT
FIRST_SABLE_BOOT=NOT_RUN
PRODUCTION_REPRODUCIBILITY_CLAIM=NO
```

## Current purpose

This repository records the public device boundary for Titan 2:

- exact stock firmware/vendor basis required for Titan 2 N0_A16;
- clean AOSP Android 16 ARM64 GSI as the first system-image substrate;
- RestlessOS as reference/future fork, not the first boot dependency;
- deployment gates that must remain closed before any E3/N0 flash attempt.

## Hard boundaries

This repository must not contain:

- stock firmware;
- OTA packages;
- partition images;
- proprietary blobs extracted from a device;
- device serials, IMEI/MEID/ICCID values or other private identifiers;
- production signing keys or credentials;
- flash scripts that imply an approved deployment path.

## Public build posture

Titan 2 public build-image and flash paths remain fail-closed in
`sableos-project/build`. This repository does not override that posture.

## Documents

```text
docs/STOCK_BASIS.md
    required stock firmware/vendor binding for a future N0 artifact

docs/ARTIFACT_DECISION.md
    decision record for gsi-system-image vs generated super image vs bounded bundle

docs/TREBLE_STRATEGY.md
    Titan 2 Treble portability strategy and RestlessOS role

docs/N0C_ANDROID17_RESTLESS_DSU_REPRO_RUNBOOK.md
    Android 17 RestlessOS public-only DSU/reproducibility research runbook

docs/RESTLESSOS_TITAN2_BUILD_NOTES.md
    R6/R7/R7D RestlessOS lessons for Titan 2 and Titan 2 Elite GSI builds

docs/DEVELOPER_PROFILE_TOOLS.md
    Work Profile policy for Sable Terminal and Sable SSH developer/operator tools

docs/DEPLOYMENT_GATE.md
    prerequisites for first E3/N0 deployment
```

## Source-of-truth relationship

- `platform_manifest` records composition and artifact identity.
- `build` owns public build, signing, verification and deployment contracts.
- this repository owns Titan 2 device-adapter documentation and, later, public
  device adaptation files when qualified.
- `sableos-project/treble_restlessos`, once created, will own the common
  RestlessOS/Treble fork boundary, not Titan-specific deployment policy.
- the private integration repository may carry pre-public working skeletons until
  source, provenance, licensing and privacy review complete.
