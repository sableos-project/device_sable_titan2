# SableOS device_sable_titan2

Status: **placeholder-only / N0 preparation**

This repository will hold the public Titan 2 device adaptation boundary for
SableOS. It is intentionally documentation-only at creation time.

```text
DEVICE=titan2
MILESTONE=N0
REPOSITORY_STATUS=PLACEHOLDER_ONLY
BUILD_IMAGE_PUBLIC=NO
FLASH_PUBLIC=NO
FIRST_SABLE_ARTIFACT=ABSENT
FIRST_SABLE_BOOT=NOT_RUN
PRODUCTION_REPRODUCIBILITY_CLAIM=NO
```

## Current purpose

This repository exists so public SableOS composition can refer to a stable device
adapter location before any Titan 2 build or deployment claim is made.

The first public work is to document:

- the exact stock firmware/vendor basis required for Titan 2 N0;
- the artifact decision between `gsi-system-image`, `generated-super-image`, and
  a bounded `system/product/system_ext` bundle;
- the deployment gate that must be satisfied before any E3/N0 flash attempt.

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

## Initial documents

```text
docs/STOCK_BASIS.md
    required stock firmware/vendor binding for a future N0 artifact

docs/ARTIFACT_DECISION.md
    decision record for gsi-system-image vs generated super image vs bounded bundle

docs/DEPLOYMENT_GATE.md
    prerequisites for first E3/N0 deployment
```

## Source-of-truth relationship

- `platform_manifest` records composition and artifact identity.
- `build` owns public build, signing, verification and deployment contracts.
- this repository owns Titan 2 device-adapter documentation and, later, public
  device adaptation files when qualified.
- the private integration repository may carry pre-public working skeletons until
  source, provenance, licensing and privacy review complete.
