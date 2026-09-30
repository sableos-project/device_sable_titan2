# N1C first-write postmortem — 2026-09-30

Status: **device recovered / N1C retry blocked**

This device-repo record captures the Titan 2-specific outcome of the first write-capable N1C test and the gates required before any future device-side attempt.

```text
TRACK=N1C_ANDROID16_HYBRID_SUPER
DEVICE=titan2
FIRST_WRITE_SCOPE=super_only
N1C_FIRST_WRITE_RESULT=FLASHED_BUT_BOOT_FAILED
STOCK_SUPER_RESTORE=PASS
TEE14_BOOT_AVB_RESTORE=PASS
FACTORY_RESET=REQUIRED_AND_PERFORMED_FROM_RECOVERY
DEVICE_RECOVERED=YES
N1C_RETRY_AUTHORIZED=NO
FLASH_AUTHORIZED_BY_THIS_DOC=NO
```

## Device-specific outcome

The reviewed N1C sparse `super` image was accepted by Titan 2 fastbootd and written successfully, but the device did not return to Android/ADB after reboot. The successful recovery path was:

```text
RESTORE_STOCK_SUPER=PASS
RESTORE_TEE14_BOOT_INIT_BOOT_VENDOR_BOOT_DTBO_VBMETA_SET=PASS
RECOVERY_FACTORY_RESET=REQUIRED_AND_PERFORMED
BOOT_TO_UNIHERTZ_SETUP=PASS
```

## Evidence paths on ai-g732

```text
N1C_WRITE_EVID=/srv/data/sable-build/evidence/titan2/n1c-first-write-super-test-r1-20260930_200458
STOCK_SUPER_ARTIFACT_EVID=/srv/data/sable-build/evidence/titan2/n1c-stock-super-restore-artifact-r1c-20260930_200124
STOCK_SUPER_LIVE_EVID=/srv/data/sable-build/evidence/titan2/n1c-stock-super-restore-live-20260930_202932
TEE14_BOOT_AVB_EVID=/srv/data/sable-build/evidence/titan2/n1c-restore-tee14-boot-avb-direct-20260930_204335
STOCK_RECOVERY_AFTER_FACTORY_RESET_EVID=/srv/data/sable-build/evidence/titan2/n1c-stock-recovery-after-factory-reset-20260930_205351
FAILURE_ANALYSIS_LEDGER_EVID=/srv/data/sable-build/evidence/titan2/n1c-first-write-failure-analysis-ledger-r1-20260930_205520
```

## Recovered stock state

```text
sys.boot_completed=1
ro.product.device=Titan_2
ro.product.model=Titan 2
ro.product.manufacturer=Unihertz
ro.build.fingerprint=Unihertz/Titan_2/Titan_2:16/BP2A.250605.031.A3/V01.00.14:user/release-keys
ro.vendor.build.fingerprint=Unihertz/Titan_2/Titan_2:14/UP1A.231005.007/V01.00.14:user/release-keys
ro.boot.slot_suffix=_a
ro.boot.dynamic_partitions=true
ro.virtual_ab.enabled=true
ro.treble.enabled=true
ro.boot.verifiedbootstate=orange
ro.boot.flash.locked=0
ro.build.version.release=16
ro.build.version.incremental=V01.00.14
```

## Device repo stop rule

```text
RETRY_SAME_N1C_ARTIFACT=NO
FLASH_NEW_N1C_ARTIFACT=NO_UNTIL_OFFLINE_ROOT_CAUSE_ANALYSIS
USERDATA_PRESERVE_ASSUMPTION_FOR_NEXT_FIRST_BOOT=INVALIDATED
FACTORY_RESET_REQUIREMENT_FOR_ANY_FUTURE_N1C_TEST=REVIEW_REQUIRED
PRODUCTION_READY_CLAIM=NO
CELLULAR_CLAIM=NO
HARDWARE_PARITY_CLAIM=NO
```

## Device bring-up script rules

```text
NO_SET_EUO_PIPEFAIL_IN_DEVICE_OR_RECOVERY_SCRIPTS=YES
NO_STDOUT_LOGGING_FROM_FUNCTIONS_USED_IN_COMMAND_SUBSTITUTION=YES
NO_HARDCODED_HOST_TOOL_WITHOUT_RESOLVER_FALLBACK=YES
NO_WRITE_CAPABLE_SCRIPT_WITHOUT_VERIFIED_RESTORE_PATH=YES
NO_WRITE_CAPABLE_SCRIPT_WITHOUT_DRY_RUN_TRANSCRIPT=YES
```

## Required before any N1D candidate

```text
KNOWN_BOOTING_RESTLESSOS_GSI_BASELINE=REQUIRED
OFFLINE_DIFF_RESTLESSOS_OR_LINEAGE23_VS_N1C=REQUIRED
FSTAB_AND_METADATA_ENCRYPTION_REVIEW=REQUIRED
INIT_AND_FIRST_STAGE_MOUNT_REVIEW=REQUIRED
VINTF_AND_VENDOR_API_REVIEW=REQUIRED
AVB_CHAIN_REVIEW=REQUIRED
DATA_FORMAT_EXPECTATION_REVIEW=REQUIRED
```

## Option C status

Option C remains structurally useful but is blocked as implemented. A future Option C candidate must be derived from a known-booting Titan 2 GSI baseline or prove equivalent boot/data compatibility before another write-capable run.

```text
OPTION_C_STATUS=OPEN_BUT_BLOCKED_PENDING_OFFLINE_DIFF
RESTLESSOS_GSI_BASELINE=REQUIRED_CONTROL_CASE
NEXT_FLASH_AUTHORIZED=NO
```