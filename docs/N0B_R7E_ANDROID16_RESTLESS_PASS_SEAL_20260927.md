# N0B R7E Android 16 Restless public-only pass seal

Status: **PASS / internal candidate only**

```text
DEVICE=titan2
DEVICE_FAMILY=titan2,titan2-elite
LANE=N0B_R7E_TREBLEAPP_DEXPREOPT_GATE_V2
ANDROID_RELEASE=16
ANDROID_VERSION_TAG=bp4a
BUILD_ID=BP4A.251205.006
RESULT=N0B_R7E_RESTLESS_NATIVE_PUBLIC_BUILD=PASS
R7E_STATUS=PASS
SYSTEM_IMAGE_COUNT=38
TARGET_FILES_COUNT=1
PUBLIC_RELEASE=NO
DEVICE_CONTACT_AUTHORIZED=NO
FLASH_AUTHORIZED=NO
ADB_COMMANDS_RUN=NO
FASTBOOT_COMMANDS_RUN=NO
```

## Evidence paths

```text
SRC_DIR=/srv/data/sable-build/workspaces/titan2-n0b-restless-native-android16-r7-seeded-public-skip-goodix-20260927_174816/treble_restlessos/src
OUT_DIR=/srv/data/sable-build/workspaces/titan2-n0b-restless-native-android16-r7-seeded-public-skip-goodix-20260927_174816/out_restless_native_android16_r7_seeded_public_skip_goodix
R7E_EVIDENCE=/srv/data/sable-build/evidence/titan2/n0b-restless-native-android16-r7e-trebleapp-dexpreopt-gate-v2/20260927_204843
SYSTEM_IMAGES_FILE=/srv/data/sable-build/evidence/titan2/n0b-restless-native-android16-r7e-trebleapp-dexpreopt-gate-v2/20260927_204843/system-images.tsv
TARGET_FILES_LIST=/srv/data/sable-build/evidence/titan2/n0b-restless-native-android16-r7e-trebleapp-dexpreopt-gate-v2/20260927_204843/target-files.tsv
IMAGE_SHA256=/srv/data/sable-build/evidence/titan2/n0b-restless-native-android16-r7e-trebleapp-dexpreopt-gate-v2/20260927_204843/image-sha256s.txt
```

## What passed

R7E completed the Android 16 RestlessOS public-only system-image lane after incremental remediation of the prior R6/R7/R7D blockers.

The build produced 38 image artifacts and one target-files artifact. This is a build qualification result only. It does not imply device boot, flash readiness, public release readiness or Sable overlay inclusion.

## Deviations from upstream/as-is RestlessOS

R7E is not an untouched upstream RestlessOS build. It is an internal Sable candidate with explicitly recorded deviations:

```text
GOODIX_PATCH_SKIPPED=YES
PLATFORM_TESTING_GATE=Android.bp_placeholder_from_R7D
TREBLEAPP_DEXPREOPT_DISABLED=YES
```

### Goodix staging patch skipped

The Android 16 public-only Restless lane previously failed in the `trebledroid-staging` tier on a Goodix patch with context drift. R7 proceeded by skipping that patch rather than manually porting it in the same lane.

This is acceptable for build qualification because the skipped patch is tracked as a deviation. It is not yet a runtime statement about Titan 2 touch, pen or Goodix-specific behavior.

### platform_testing Android.bp placeholder gate

R7B/R7C encountered `platform_testing` host-test/package aggregators that were not valid for this product-image build graph. R7D replaced `platform_testing/Android.bp` with a minimal package placeholder for this lane.

This is a build-graph gate only. It must not be treated as a runtime product change or as a general AOSP policy.

### TrebleApp dexpreopt disabled

R7D reached late Ninja packaging but failed while generating `TrebleApp_intermediates/dexpreopt.sh` due a `product_packages.txt` path validation issue. R7E disabled dexpreopt for `vendor/hardware_overlay/TrebleApp` only by adding `LOCAL_DEX_PREOPT := false` to the TrebleApp Android.mk.

This is a narrow prebuilt-app packaging remediation. It does not remove TrebleApp from the product image.

## Current promotion state

```text
N0B_ANDROID16_RESTLESS_INTERNAL_CANDIDATE=YES
FIRST_SABLE_ARTIFACT=NO
FIRST_SABLE_BOOT=NOT_RUN
PHYSICAL_TITAN2_VALIDATION=NOT_RUN
PHYSICAL_TITAN2_ELITE_VALIDATION=NOT_RUN
PUBLIC_BUILD_IMAGE=NO
PUBLIC_FLASH_PATH=NO
PRODUCTION_REPRODUCIBILITY_CLAIM=NO
```

## Required follow-up before any device action

Before this candidate can move toward device use, Sable must perform a separate artifact qualification and deployment review:

```text
SYSTEM_IMAGE_IDENTITY_CAPTURED=REQUIRED
TARGET_FILES_IDENTITY_CAPTURED=REQUIRED
SPARSE_OR_RAW_SYSTEM_IMAGE_VALIDATED=REQUIRED
LOGICAL_PARTITION_FIT_VALIDATED=REQUIRED
AVB_STRATEGY_EXPLICIT=REQUIRED
RESTORE_PATH_VERIFIED=REQUIRED
USERDATA_WIPE_POLICY_DECIDED=REQUIRED
DEVICE_BINDING_DECIDED=REQUIRED
```

No flash or ADB/fastboot action is authorized by this pass seal.

## Relationship to N0C Android 17

R7E keeps Android 16 Restless viable as the N0B internal candidate. It does not cancel N0C. Android 17 remains a parallel RestlessOS DSU/reproducibility research lane documented separately.

```text
PRIMARY_INTERNAL_CANDIDATE=N0B_ANDROID16_R7E
PARALLEL_RESEARCH=N0C_ANDROID17_RESTLESS_DSU_REPRO
BASELINE_PIVOT_TO_ANDROID17=NO
```
