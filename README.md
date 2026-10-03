# SableOS device_sable_titan2

Status: **public Titan device boundary / active canonical engineering is N1D/C3B; public build still fail-closed — 2026-10-02**

This repository holds the public Titan 2 device adaptation boundary for SableOS.
It remains documentation-only until the first Titan 2 artifact is built,
verified and explicitly promoted into public build tooling.

```text
DEVICE=titan2
HISTORICAL_PUBLIC_MILESTONE=N0_A16
ACTIVE_CANONICAL_ENGINEERING_MILESTONE=N1D_C3B
REPOSITORY_STATUS=DEVICE_BOUNDARY_AND_EVIDENCE
ACTIVE_CANONICAL_ARTIFACT_KIND=Sable-composed systemimage engineering build
BUILD_IMAGE_PUBLIC=NO
FLASH_PUBLIC=NO
FIRST_PUBLIC_SABLE_ARTIFACT=ABSENT
PRIVATE_C3B_E3_STATUS=BUILD_RUNNING_NOT_YET_SEALED
FIRST_PUBLIC_SABLE_BOOT=NOT_RUN
PRODUCTION_REPRODUCIBILITY_CLAIM=NO
```

## Current C3B checkpoint

Current private canonical state:

```text
C3B_E1_SYSTEMIMAGE=PASS
C3B_E2_SOURCE_ADMISSION=PASS
C3B_E3_BUILD_SOURCE=caf98dde723d07a071d95aaa1ef27d578d3208d8
C3B_E3_STATUS=BUILD_RUNNING_NOT_YET_SEALED
C3B_RUNTIME_PATCH_ALLOWLIST_COUNT=0
```

The E3 run passed its pre-build compatibility/product/module gates before
starting the long fresh systemimage build. No public artifact or physical boot
claim is made from that fact.

Next canonical phases are E4 artifact/deployment readiness, E5 first physical
C3B boot, E6 runtime baseline qualification and E7 evidence-driven compatibility.

## Current purpose

This repository records the public device boundary and preserved Titan 2
bring-up evidence.

The earlier N0/AOSP-first documents remain useful historical strategy/evidence,
but current canonical engineering is the private N1D/C3B lane:

- Graphene/AOSP-derived Android 16 base;
- minimal Treble scaffold;
- compatibility-peel admission rather than the full RestlessOS runtime stack;
- `systemimage` engineering target before product/runtime/release admission;
- public build/flash/signing remain closed until separately qualified.

RestlessOS/TrebleDroid remains a compatibility reference and known-fix inventory,
not the Sable product/security baseline.

## Current product/design bindings

This device repository does not own common HOME, IME or Camera UX policy.

Current common authority is in `sableos-project/platform_sable`:

```text
SABLE_FIRST_PARTY_HOME=Launcher3QuickStep hosting Sable Start
THIRD_PARTY_HOME_SELECTION_ALLOWED=YES
SABLE_FIRST_PARTY_IME=SableKeyboard
THIRD_PARTY_IME_SELECTION_ALLOWED=YES
SABLE_CAMERA_DIRECTION=Camera Control Deck
PASTIERA_086_ROLE=behavior/product reference only
```

The Camera Control Deck is an interaction requirement, not device-camera
capability evidence. Titan 2 must still prove key delivery, AF/AE behavior,
focus-point geometry, orientation and HAL capability on hardware.

Sable Hub is also common product semantics rather than a Titan-specific fork.
Titan 2 integration must preserve the Panther Hub V1 Connected Apps contract:
generic package+user discovery/configuration, Android notification/conversation
ingestion, source-authorized RemoteInput reply, Open-app fallback and bounded
local derived history. WhatsApp, Signal, Telegram and LinkedIn are
compatibility/evidence targets, not a device-level hard-coded allowlist.
Sable Messages work must not replace or weaken Sable Hub.

The normative public contract is
`sableos-project/platform_sable/docs/SABLE_HUB_PORTABILITY_CONTRACT.md`.

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

docs/N0B_R7E_ANDROID16_RESTLESS_PASS_SEAL_20260927.md
    Android 16 RestlessOS public-only R7E build pass seal and deviations

docs/N0C_ANDROID17_RESTLESS_DSU_REPRO_RUNBOOK.md
    Android 17 RestlessOS public-only DSU/reproducibility research runbook

docs/RESTLESSOS_TITAN2_BUILD_NOTES.md
    R6/R7/R7D RestlessOS lessons for Titan 2 and Titan 2 Elite GSI builds

docs/DEVELOPER_PROFILE_TOOLS.md
    Work Profile policy for Sable Terminal and Sable SSH developer/operator tools

docs/DEPLOYMENT_GATE.md
    historical deployment prerequisites; current deployment readiness is E4 after E3 seal
```

## Source-of-truth relationship

- `platform_manifest` records current composition and artifact identity; its old N0 placeholder is historical once superseded by N1D/C3B authority.
- `build` owns public build, signing, verification and deployment contracts.
- this repository owns Titan 2 device-adapter documentation and, later, public
  device adaptation files when qualified.
- `sableos-project/treble_restlessos`, once created, will own the common
  RestlessOS/Treble fork boundary, not Titan-specific deployment policy.
- the private integration repository is the current N1D/C3B engineering and product-integration authority until qualified pieces are published;
- organization-level current-policy pointers live in `sableos-project/.github` so historical device evidence is not mistaken for current product architecture.
