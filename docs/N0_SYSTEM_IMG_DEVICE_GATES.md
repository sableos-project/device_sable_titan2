# N0 System Image Device Gates

STATUS=DEVICE_GATE_CONTRACT
TARGET_DEVICE=Titan 2
RELATED_FAMILY=Titan 2 Elite
BUILD_CODE_CHANGED=NO
DEVICE_CODE_CHANGED=NO
FLASH_ENABLEMENT=NO

## Purpose

This document is the Titan 2 device-repo gate companion to the SableOS Titan N0 system image feature matrix. It defines which system image surfaces may be included, which require Titan 2 hardware/profile validation, and which must stay disabled or device-specific.

```text
N0_SYSTEM_IMG_DEVICE_GATES=YES
TARGET_DEVICE=TITAN2
TITAN_FAMILY_BASE_PROFILE=YES
PROFILE_PASS_INHERITANCE=NO
RUNTIME_VALIDATION_REQUIRED=YES
FLASH_AUTHORIZED=NO
```

## Device build boundary

```text
N0_SOURCE_READY=YES
N0_BUILD_PREFLIGHT_REQUIRED=YES
SYSTEM_IMG_CAN_BE_BUILT=ONLY_AFTER_PREFLIGHT
BOOT_FLASH_AUTHORIZED=NO
FASTBOOT_FLASH_AUTHORIZED=NO
WRITE_CAPABLE_FLASH_SCRIPT=NO
DEVICE_MUTATION=NO
```

Device repo gates are allowed to describe intended package/product inclusion and runtime tests. They must not enable flashing or claim hardware pass before validation.

## Gate states

```text
REQUIRED=must be represented in product/system image plan
PROFILE_GATED=may be included but must be profile-controlled
HARDWARE_GATED=must not claim function until hardware/HAL/vendor path proves it
POLICY_GATED=must obey region/privacy/security policy
DISABLED=do not expose as enabled user feature
TITAN2_SPECIFIC=not inherited by Titan 2 Elite unless separately validated
```

## Titan 2 system image gate matrix

| Surface | Gate | Titan 2 expectation | Validation evidence required |
| --- | --- | --- | --- |
| SableLauncher / Sable Start HOME | REQUIRED | Present as intended HOME; Launcher3 not HOME; Quickstep retained for Recents. | package presence, default HOME resolution, Recents pass |
| Base quick bar | REQUIRED | Phone, Hub, Command, All Apps defaults; optional Camera slot where profile allows. | visual and key-shortcut proof |
| Global Command | REQUIRED | Local-first command/search entry from Start and supported surfaces. | local actions, provider-safe boundaries |
| Physical keyboard policy | REQUIRED | Visible focus, text-input-wins, onscreen fallback, Sym/Fn handling. | key event and text-entry tests |
| Titan key mapping | PROFILE_GATED | Programmable/PTT/Sym/Fn/letter/Space/backlight/gesture surfaces present. | key layout discovery and per-key behavior proof |
| App display compatibility | PROFILE_GATED | Settings UI and per-app state allowed; backend force behavior gated. | Settings paths, reset outside app, WindowManager validation before claims |
| Global appearance | REQUIRED | Settings authority and Sable app clients follow theme. | dark/light propagation proof |
| Quick Settings / shade | PROFILE_GATED | keyboard shade, profile-gated hardware tiles, redaction. | SystemUI visual and key navigation proof |
| Lockscreen | REQUIRED | keyboard PIN/password, fallback keyboard, redaction, emergency path. | unlock and emergency tests |
| Setup Wizard | REQUIRED | keyboard verification, Wi-Fi password, theme/privacy choices. | no setup dead-end proof |
| All Apps privacy summary | REQUIRED | app labels primary, privacy summary visible, package names not primary. | launcher/settings app-list proof |
| App security/privacy transparency | REQUIRED | Settings application detail exposes effective access and provenance classes. | Settings surface proof |
| Sable Network Manager | REQUIRED | Settings-owned all-network toggle; no duplicate Mobile Manager network policy. | authority check, no fake Wi-Fi/cellular split |
| Hub | REQUIRED | Provider-safe Priority/Messages/Email/People pivots. | bounded provider proof |
| Messages | REQUIRED | latest-anchored keyboard-first conversations; no custom transport claim. | open-at-latest and composer proof |
| Phone | REQUIRED | keyboard dialing and onscreen dialpad fallback. | dialing and DTMF proof |
| Contacts | REQUIRED | A-Z rail/search/source labels/provider-safe actions. | visible source labels and actions |
| Mail | REQUIRED | Mail owns accounts/protocols/storage; Hub consumes bounded snapshots only. | provider boundary proof |
| Calendar | REQUIRED | current/next event visible, keyboard-first agenda/detail. | now-view proof |
| Browser / WebView | REQUIRED | correct browser identity, WebView provider diagnostics, keyboard find/page mode. | Vanadium/Chrome label audit |
| Media / Gallery | REQUIRED | keyboard-first media/gallery with MediaStore boundary. | library/current-item proof |
| Camera | HARDWARE_GATED | Camera app/profile may exist; telephoto/pro/raw/high-res claims gated. | Camera HAL and same-scene quality tests |
| Files / Documents | REQUIRED | SAF/DocumentsUI-compatible files/docs/picker/share. | storage boundary proof |
| Utilities | REQUIRED | Notes, Recorder, Clock, Calculator/Converter, Tasks. | package/surface proof |
| Toolbox sensors | HARDWARE_GATED | Compass/level/pedometer/speedometer/etc gated by sensor availability. | SensorManager and runtime tests |
| IR Remote | HARDWARE_GATED | UI may be planned; enabled remote requires IR transmitter validation. | transmitter path proof |
| FM Radio | DISABLED | Do not expose enabled FM Radio until hardware/HAL/vendor path is proven. | HAL/vendor proof required before enablement |
| Private Space / Secure Vault | PROFILE_GATED | encrypted vault allowed; app profile isolation gated. | vault proof, no fake isolation claim |
| Dual Apps | PROFILE_GATED | clone/profile implementation first. | profile creation/install proof |
| App Lock | REQUIRED | user-selected locking surface with recovery path. | lock/unlock/reset proof |
| Freezer / App blocker | PROFILE_GATED | warnings for notifications/sync/background breakage. | no silent restriction proof |
| Student / Focus Mode | POLICY_GATED | time/app/network/site limits only if enforcement exists. | enforcement proof or no-op disclosure |
| Connectivity | HARDWARE_GATED | OTG/NFC/Cast/Tethering/Bluetooth/reverse charge gates. | per-feature hardware/HAL proof |
| eSIM / dual SIM | HARDWARE_GATED | EUICC/carrier-gated surfaces. | eSIM stack and carrier tests |
| Sub-screen | TITAN2_SPECIFIC | Titan 2 companion surface only; not Titan-family base. | Titan 2 runtime proof; no Elite inheritance |
| Call recorder | POLICY_GATED | default off, visible recording state, storage path, region policy gate. | policy and visible-state proof |
| Brand / string scrub | REQUIRED | Sable labels; no Graphene placeholder UI; Vanadium not shown as Chrome. | string/resource audit |

## Do-not-enable list for N0 without proof

```text
WRITE_CAPABLE_FLASH_SCRIPT=NO
SUBSCREEN_ELITE_INHERITANCE=NO
FM_RADIO_ENABLEMENT=NO
IR_REMOTE_TRANSMISSION_CLAIM_WITHOUT_PROOF=NO
NFC_PAYMENT_CLAIM_WITHOUT_HAL_AND_PROVIDER=NO
ESIM_CLAIM_WITHOUT_EUICC_STACK=NO
PER_APP_CELLULAR_WIFI_SPLIT=NO
CALL_RECORDING_AUTO_DEFAULT=NO
APP_DISPLAY_FORCE_BACKEND_CLAIM_WITHOUT_WINDOWMANAGER_PROOF=NO
CUSTOM_SMS_RCS_MMS_STACK=NO
CUSTOM_MAIL_DATABASE_READER_IN_HUB=NO
CUSTOM_CONTACTS_PROVIDER=NO
CUSTOM_MEDIASTORE_PROVIDER=NO
CUSTOM_FILESYSTEM_STACK=NO
```

## N0 preflight evidence expected from this repo

```text
DEVICE_REPO_PRESENT=YES
DEVICE_TREE_READONLY_PREFLIGHT=YES
PRODUCT_MAKEFILES_IDENTIFIED=YES
PRODUCT_PACKAGES_AUDITED=YES
DEVICE_OVERLAYS_AUDITED=YES
KEYLAYOUT_KEYCHARMAP_AUDITED=YES
PERMISSION_XML_AUDITED=YES
SEPOLICY_AUDITED_IF_PRESENT=YES
VENDOR_BLOBS_NOT_ASSUMED=YES
FLASH_AUTHORIZED=NO
```

## Acceptance criteria

```text
N0_SYSTEM_IMG_DEVICE_GATES=PASS
TITAN2_GATE_MATRIX=PASS
SYSTEM_IMG_FEATURE_MATRIX_COMPANION=PASS
APP_DISPLAY_COMPATIBILITY_GATE_INCLUDED=PASS
SUBSCREEN_TITAN2_SPECIFIC=PASS
FM_RADIO_DISABLED_UNTIL_PROVEN=PASS
NETWORK_MANAGER_DUPLICATION_BLOCKED=PASS
NO_FAKE_HARDWARE_SUCCESS=PASS
NO_FLASH_ENABLEMENT=PASS
BUILD_CODE_CHANGED=NO
DEVICE_CODE_CHANGED=NO
FLASH_ENABLEMENT=NO
```
