# RestlessOS Titan 2 / Titan 2 Elite build notes

Status: **reference document / living build notes**

This document records current SableOS experience using RestlessOS as a Treble
GSI substrate for Titan 2 and Titan 2 Elite work. It is meant to preserve the
R6/R7/R7B/R7C/R7D lessons so SableOS and other developers do not repeat the
same dead ends.

```text
DEVICE_SCOPE=titan2,titan2-elite
PUBLIC_BUILD_STATUS=NO
FLASH_AUTHORIZED=NO
DEVICE_CONTACT_AUTHORIZED=NO
CURRENT_PRIMARY_LANE=N0B_ANDROID16_RESTLESS_PUBLIC_ONLY_PENDING_R7D
PARALLEL_RESEARCH_LANE=N0C_ANDROID17_RESTLESS_PUBLIC_ONLY_DSU_RESEARCH
```

## High-level conclusion

RestlessOS is useful for Titan-family GSI work, but it should be treated as an
upstream compatibility substrate, not a drop-in SableOS product.

For Titan work:

```text
- keep source pins explicit;
- remove private maintainer dependencies;
- avoid branch-tip builds for reproducibility;
- avoid manual framework ports unless physical evidence proves necessity;
- do not inherit Titan-specific daemons before baseline boot is proven;
- do not claim GrapheneOS/RestlessOS security properties for Sable images.
```

## Android 16 lane summary

Android 16 was selected first because Titan 2 / Titan 2 Elite vendor firmware is
currently Android 16-facing for Sable planning, and because matching vendor age
reduces runtime uncertainty during first bring-up.

SableOS Android 16 Restless path:

```text
N0B_R6=Restless-native Android 16 public-only sync and patch attempt
N0B_R7=Seeded public-only retry, skip only Goodix context-drift patch
N0B_R7B=Gate one broken platform_testing metric package
N0B_R7C=Gate platform_testing host_tests entries
N0B_R7D=Gate platform_testing Android.bp for product image build
```

## R6: public-only source sync succeeded, staging failed on Goodix

R6 proved the important source hygiene points:

```text
GRAPHENEOS_TAG=2026061602
AOSP_REVISION=android-16.0.0_r4
ANDROID_MAJOR=16
PRIVATE_SOURCE_REMOVED=vendor/cawilliamson-priv
PUBLIC_SOURCE_SYNC=PASS
PATCH_TIER_trebledroid=PASS
PATCH_TIER_trebledroid-staging=FAIL
```

The first Android 16 Restless blocker was:

```text
PATCH=trebledroid-staging/platform_device_phh_treble/0002-guard-goodix-sysfs-write-behind-existence-check.patch
CLASSIFICATION=CONTEXT_DRIFT_TARGET_STILL_PRESENT
RECOMMENDATION=DO_NOT_MANUAL_PORT_IN_R6
```

Interpretation:

```text
- Goodix target still exists.
- Patch was not already applied.
- Failure was context drift, not proof that Restless is unusable.
- R6 should be preserved as evidence, not manually repaired in place.
```

## R7: seed from R6, skip only Goodix, reach build

R7 was intentionally seeded from R6 instead of cold-syncing again. The seed copy
preserved R6 evidence while reusing local repo object data.

R7 policy:

```text
R6_MUTATION=NO
ANDROID_16_PINNED=YES
PRIVATE_SOURCE_REMOVED=YES
SKIP_ONLY_GOODIX=YES
MANUAL_PORT_AUTHORIZED=NO
STOP_ON_NEXT_FAILURE=YES
```

R7 progressed past the patch-queue decision point. The next blocker was Soong
build-graph validation in `platform_testing/Android.bp`, not a Restless patch
queue failure:

```text
MODULE=continuous_instrumentation_metric_tests
PATH_OUTSIDE_DIRECTORY=out/.../host/linux-x86/bin/perfetto_trace_processor_shell
FAILURE_CLASS=SOONG_PATH_VALIDATION_TEST_MODULE
RUNTIME_IMAGE_FAILURE=NO
```

Interpretation:

```text
- R7 proved the Goodix skip lets the Android 16 patch stack reach build.
- The failure was in continuous instrumentation test packaging.
- It did not indicate a runtime product image incompatibility.
```

## R7B and R7C: one-by-one platform_testing edits were insufficient

R7B gated one broken `platform_testing` metric test package. The next build
failed on another `platform_testing` package:

```text
R7B_FAILURE_MODULE=continuous_native_tests
PATH_OUTSIDE_DIRECTORY=out/.../host/linux-x86/framework/net-tests-utils-host-common.jar
```

R7C gated `host_tests` entries inside `platform_testing/Android.bp` test_package
blocks, but the same `continuous_native_tests` aggregator still resolved the
host jar and failed Soong validation.

Interpretation:

```text
- The problem is broader than one `host_tests` field.
- platform_testing test_package aggregators are not valid in this product image build graph.
- Continue gating test-only aggregation, not runtime modules.
```

## R7D: platform_testing Android.bp gate

R7D replaces `platform_testing/Android.bp` with a placeholder inside the R7
workspace only. This is a test aggregation gate, not a runtime product change.

R7D policy:

```text
MUTATION_SCOPE=R7_WORKSPACE_ONLY
CHANGE=Replace platform_testing/Android.bp with placeholder for product image build
RUNTIME_IMAGE_INTENT=NO_RUNTIME_PRODUCT_CHANGE
DEVICE_CONTACT_AUTHORIZED=NO
FLASH_AUTHORIZED=NO
```

R7D status at the time this document was written:

```text
R7D_STATUS=RUNNING_OR_PENDING_USER_RESULT
```

When the final R7D result is available, update this section with:

```text
R7D_RESTLESS_NATIVE_PUBLIC_BUILD_RC=<value>
MAKE_SYSTEMIMAGE_RC=<value>
MAKE_TARGETFILES_OTATOOLS_RC=<value>
SYSTEM_IMAGE_COUNT=<value>
TARGET_FILES_COUNT=<value>
R7D_STATUS=<PASS|FAIL_*>
```

## Android 17 N0C research lane

Android 17 is worth pursuing in parallel because RestlessOS upstream has moved
forward, but it must not replace Android 16 as primary Titan baseline until it
passes physical Titan 2 validation.

N0C pinning plan:

```text
PRIMARY_RESTLESSOS_TAG=17.0.0-202609271006
CONTROL_RESTLESSOS_TAG=17.0.0-202609222215
GRAPHENEOS_TAG=2026091900
PRODUCT=treble_arm64_bvN
```

Use `docs/N0C_ANDROID17_RESTLESS_DSU_REPRO_RUNBOOK.md` for the full DSU,
reproducibility, signing and validation plan.

## Private maintainer dependency

RestlessOS manifests include a private maintainer repository:

```text
remote=cawilliamson-priv
project=vendor/cawilliamson-priv
```

Sable public lanes must remove this dependency before sync.

Known evidence:

```text
- Android 16 R6 public-only sync passed after removing the private source.
- Android 17 still requires the same public-only sanitation.
- Known references appear to be signing-stage related, but absence of runtime use is evidence, not proof.
```

Required Sable posture:

```text
PRIVATE_SOURCE_ALLOWED=NO
PRIVATE_SIGNING_KEYS_ALLOWED=NO
PUBLIC_BUILD_REPRO_COMPARISON=REQUIRED
```

For Android 17, compare Sable-built image trees against official upstream image
trees to confirm that private dependency removal did not remove runtime files,
permissions, overlays, SELinux policy, VINTF data, APEX modules, or privileged
apps.

## Patch tier handling

Do not hard-code tiers across RestlessOS tags.

Android 17 tier layout changed across tags:

```text
17.0.0-202609222215:
  trebledroid
  trebledroid-staging
  rom
  personal
  release-builds/debug-builds

17.0.0-202609261212 and newer:
  trebledroid
  rom
  personal
  release-builds/debug-builds
```

Always read patch tiers from the pinned tag's own build workflow.

## platform_testing guidance

`platform_testing` failures observed in the Android 16 R7 path were test-package
aggregation failures in Soong graph generation. They should not be treated as
Titan runtime failures.

Observed symptoms:

```text
Path is outside directory: out/.../perfetto_trace_processor_shell
Path is outside directory: out/.../net-tests-utils-host-common.jar
```

Recommended handling:

```text
- Capture evidence first.
- Confirm failure is isolated to platform_testing test packages.
- Gate test-only aggregators in the research workspace.
- Do not patch frameworks/base or runtime services for platform_testing-only failures.
- Record every deviation before promoting an image candidate.
```

## Titan-specific features are not baseline dependencies

Titan-specific input/display features are important, but they belong after a
bootable baseline has been proven.

Potential future work:

```text
N2_TITAN_INPUT_ENABLEMENT:
  - extract stock keylayout files from Titan 2 firmware/device
  - capture getevent matrix for every physical key
  - classify Sym/Fn/Alt behavior on stock and GSI
  - evaluate titan2-touchpadd source/prebuilt provenance and GPLv3 obligations
  - add /dev/uinput SELinux only after real denials are collected
  - validate rear display and programmable key behavior
```

Do not include the following in the first N0C baseline:

```text
- titan2-touchpadd binary
- custom uinput daemon SELinux policy
- custom Sym/Fn/Alt framework overlay
- stock keylayout remaps
- rear-screen integration
```

## MediaTek BPF/networking caution

RestlessOS/TrebleDroid work may expose MediaTek BPF or network-policy issues.
Do not patch a boot image or kernel blindly.

Capture first:

```bash
adb shell uname -r
adb shell ls -la /sys/fs/bpf /sys/fs/bpf/net_shared 2>/dev/null
adb logcat -b all -d | grep -Ei 'bpf|netd|Firewall|firewall'
adb shell dmesg | grep -i bpf
```

Only consider MediaTek BPF patching after physical evidence shows the known
kernel-side failure mode.

## Developer notes for future contributors

If building RestlessOS for Titan 2 / Titan 2 Elite:

```text
1. Choose a pinned RestlessOS tag.
2. Choose the matching GrapheneOS tag.
3. Remove private source dependencies.
4. Pin drifting external manifests such as vendor/apn.
5. Read patch tiers from the pinned tag.
6. Build treble_arm64_bvN, not generic aosp_arm64.
7. Preserve repo manifest -r output.
8. Compare extracted image trees, not raw image hashes.
9. Use DSU before physical flash.
10. Treat Titan keyboard/touchpad/rear-screen as post-baseline enablement.
```

## Non-goals

This document does not authorize:

```text
- flashing Titan 2 or Titan 2 Elite;
- relocking bootloader;
- publishing SableOS Titan images;
- distributing proprietary stock firmware or blobs;
- storing production signing keys;
- claiming GrapheneOS-equivalent security properties;
- integrating GPLv3 prebuilts without license/provenance review.
```
