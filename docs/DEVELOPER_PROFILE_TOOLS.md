# Developer profile tools

Status: **policy documented / implementation absent**

This document records the SableOS product decision for secure terminal and SSH
capabilities on Titan 2, Titan 2 Elite and the wider SableOS handheld profile.
It is documentation-only. It does not add applications, packages, privileged
services, device policy controller code, Work Profile provisioning code, flash
steps or release artifacts.

```text
DECISION_AREA=developer_operator_tools
DEFAULT_PERSONAL_PROFILE_APPS=NO
WORK_PROFILE_DEFAULT=YES
SABLE_TERMINAL_IMPLEMENTED=NO
SABLE_SSH_IMPLEMENTED=NO
TERMUX_FORK_DECIDED=NO
TERMIUS_BUNDLE_DECIDED=NO
DEVICE_CONTACT_AUTHORIZED=NO
FLASH_AUTHORIZED=NO
PUBLIC_RELEASE=NO
```

## Product decision

SableOS should support secure developer and operator workflows, but these tools
should not be bundled into the default personal profile.

The preferred product shape is:

```text
Developer Work Profile
    Sable Terminal
        Termux-like Linux CLI environment
        policy-wrapped, Sable-signed and repository-pinned

    Sable SSH
        Termius-like SSH client
        local-first, cloudless by default and host-key strict
```

This keeps powerful tools available on keyboard-first devices without making the
main personal profile a broad remote-access or shell environment by default.

## Rationale

Titan 2 and Titan 2 Elite are unusually useful as small operator/developer
handhelds because they combine:

- physical keyboard input;
- square display constraints that benefit from text-first tools;
- mobile networking;
- potential secure Work Profile separation;
- SableOS privacy/security positioning.

The same features that make terminal and SSH useful also increase risk. A shell,
package manager, SSH agent, port forwarder or remote host database can become a
high-value target. The default boundary is therefore a separate Developer Work
Profile with explicit controls.

## Sable Terminal

Sable Terminal is the SableOS placeholder name for a Termux-like Linux command
line environment.

Target properties:

```text
INSTALL_LOCATION=Developer_Work_Profile
DEFAULT_PERSONAL_PROFILE_INSTALL=NO
SABLE_SIGNED=YES
PACKAGE_REPOSITORY_PINNED=YES
PLAY_STORE_DEPENDENCY=NO
ROOT_REQUIRED=NO
BROAD_SHARED_STORAGE_DEFAULT=NO
CONTACTS_SMS_PHONE_LOCATION_PERMISSIONS_DEFAULT=NO
BACKGROUND_DAEMONS_DEFAULT=NO
CROSS_PROFILE_CLIPBOARD_DEFAULT=NO
```

Expected capabilities:

- local shell and scripting;
- package-managed developer tools after repository review;
- git and source inspection;
- ssh/scp/rsync client workflows;
- optional ADB/fastboot tooling only if later policy explicitly approves it;
- visible network/background execution state.

Default denials:

- root access;
- unrestricted shared storage;
- privileged Android APIs;
- silent background listeners;
- cross-profile clipboard sharing;
- contacts, SMS, phone and location permissions;
- package repositories that are not pinned or reviewed by Sable.

### Termux relationship

Termux remains the best public model for a Linux CLI environment on Android.
Sable Terminal may be:

```text
OPTION_A=Termux-compatible policy wrapper
OPTION_B=Termux fork with GPLv3 compliance
OPTION_C=smaller Sable shell environment with curated package set
```

No option is selected by this document. Before choosing Option B, Sable must
complete source, license, package repository and redistribution review.

## Sable SSH

Sable SSH is the SableOS placeholder name for a local-first SSH client and SSH
key workflow.

Target properties:

```text
INSTALL_LOCATION=Developer_Work_Profile
DEFAULT_PERSONAL_PROFILE_INSTALL=NO
CLOUD_ACCOUNT_REQUIRED=NO
CLOUD_SYNC_DEFAULT=NO
HOST_KEY_PINNING=YES
KNOWN_HOSTS_WARNINGS=YES
LOCAL_ENCRYPTED_HOST_DATABASE=YES
HARDWARE_BACKED_KEY_STORAGE_WHERE_POSSIBLE=YES
VISIBLE_ACTIVE_SESSION_NOTIFICATION=YES
PORT_FORWARDING_EXPLICIT_PROMPT=YES
```

Required behavior:

- generate Ed25519 keys;
- import/export keys only by explicit user action;
- preserve passphrase support;
- warn on host key changes;
- show active connections and forwarding state;
- avoid required vendor cloud sync;
- support local backup/export policy controlled by Sable Settings.

### Termius relationship

Termius is a useful comparison point for polished SSH workflows, but Sable should
not require a cloud account or cloud-vault trust model for the default SSH client.
Users may install third-party SSH clients by policy, but the Sable default should
be local-first.

### ConnectBot relationship

ConnectBot is a useful open-source reference for an Android-native SSH client.
Sable may evaluate:

```text
OPTION_A=ConnectBot-derived SSH app
OPTION_B=ConnectBot-inspired Sable SSH implementation
OPTION_C=separate Sable SSH app using another reviewed SSH stack
```

No implementation option is selected by this document.

## Sable Settings surface

A future Sable Settings surface should make the boundary visible:

```text
Settings > Security & Privacy > Developer Tools

- Enable Developer Work Profile
- Install Sable Terminal
- Install Sable SSH
- Allow SSH agent
- Allow SSH port forwarding
- Allow local network access
- Allow cross-profile clipboard
- Export/import SSH keys
- Reset developer profile
```

The default state should keep these tools disabled or absent until the user
enables the Developer Work Profile.

## Work Profile policy

Baseline defaults:

```text
PERSONAL_PROFILE_TERMINAL=ABSENT
PERSONAL_PROFILE_SSH=ABSENT
DEVELOPER_PROFILE_REQUIRED=YES
CROSS_PROFILE_FILE_ACCESS=DENY_BY_DEFAULT
CROSS_PROFILE_CLIPBOARD=DENY_BY_DEFAULT
CROSS_PROFILE_CONTACT_ACCESS=DENY_BY_DEFAULT
NETWORK_ACCESS_VISIBLE=YES
RESET_DEVELOPER_PROFILE_SUPPORTED=YES
```

Sable should treat the Developer Work Profile as disposable and resettable. A
future device policy controller may support profile wipe without touching the
personal profile.

## Package and repository policy

For terminal package ecosystems:

```text
PACKAGE_INDEX_PINNED=YES
REPOSITORY_METADATA_CAPTURED=YES
MIRROR_SOURCE_RECORDED=YES
NETWORK_INSTALL_LOGGED=YES
REPRODUCIBILITY_TARGET=YES
```

Sable should not silently inherit third-party package repository freshness,
availability or trust claims. Repository choice must be explicit.

## Key and credential policy

For SSH:

```text
PRIVATE_KEYS_APP_PRIVATE=YES
KEY_EXPORT_EXPLICIT=YES
KEY_IMPORT_EXPLICIT=YES
PASSPHRASE_SUPPORTED=YES
HOST_KEY_CHANGES_BLOCK_OR_WARN=YES
CLOUD_SYNC_DEFAULT=NO
```

Hardware-backed or Android Keystore-backed protection should be used where it is
compatible with SSH signing and user recovery expectations. If Keystore-backed
keys cannot be exported, the UI must make that clear before key generation.

## Non-goals for first implementation

The first Sable developer-tool implementation should not include:

- root shell;
- bundled offensive security tooling;
- silent SSH agent forwarding;
- default port listeners;
- cross-profile shared home directory;
- automatic cloud sync;
- shared private keys between personal and work profiles;
- Sable release keys, production credentials or signing material.

## Titan-specific implications

Titan 2 and Titan 2 Elite can benefit from this decision because physical
keyboard entry makes shell and SSH workflows practical. However, this document
does not depend on Titan-specific keyboard/touchpad enablement.

Titan-specific work remains separate:

```text
TITAN_KEYLAYOUT_EXTRACTION=N2_DEVICE_VALIDATION
TITAN_TOUCHPAD_DAEMON=N2_DEVICE_VALIDATION
TITAN_SHORTCUT_KEYS=N2_DEVICE_VALIDATION
SABLE_TERMINAL_BASELINE=N1_OR_LATER_PRODUCT_DECISION
SABLE_SSH_BASELINE=N1_OR_LATER_PRODUCT_DECISION
```

## Promotion gates

Before either app is promoted into a SableOS build, the following must be true:

```text
SOURCE_PROVENANCE_REVIEW=PASS
LICENSE_REVIEW=PASS
PACKAGE_REPOSITORY_POLICY=PASS
WORK_PROFILE_POLICY=PASS
PERMISSION_REVIEW=PASS
NETWORK_BEHAVIOR_REVIEW=PASS
KEY_STORAGE_REVIEW=PASS
BACKUP_EXPORT_POLICY=PASS
UI_REVIEW=PASS
SECURITY_REVIEW=PASS
```

For any forked or redistributed third-party project, Sable must also publish
required source, notices and license obligations before public release.

## Current decision summary

```text
SABLE_SHOULD_SUPPORT_DEVELOPER_TOOLS=YES
DEFAULT_PERSONAL_PROFILE_BUNDLE=NO
PREFERRED_BOUNDARY=Developer_Work_Profile
SABLE_TERMINAL=PLANNED_CONCEPT
SABLE_SSH=PLANNED_CONCEPT
TERMUX_STOCK_PREINSTALL=NO
TERMIUS_DEFAULT_BUNDLE=NO
CONNECTBOT_REFERENCE=YES
IMPLEMENTATION_STAGE=NOT_STARTED
```
