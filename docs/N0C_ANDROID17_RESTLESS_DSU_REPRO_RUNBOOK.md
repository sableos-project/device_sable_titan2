# N0C Android 17 RestlessOS DSU and reproducibility runbook

Status: **research lane / not primary baseline**

This document records the SableOS Titan 2 / Titan 2 Elite Android 17
RestlessOS research plan. It is intentionally a DSU/reproducibility runbook,
not a flash authorization and not a baseline pivot away from Android 16 N0B.

```text
LANE=N0C_ANDROID17_RESTLESS_PUBLIC_ONLY_DSU_RESEARCH
PRIMARY_BASELINE=N0B_ANDROID16_PENDING_R7D_RESULT
DEVICE_SCOPE=titan2,titan2-elite
FLASH_AUTHORIZED=NO
DEVICE_CONTACT_REQUIRED_FOR_THIS_DOC=NO
PUBLIC_RELEASE=NO
SABLE_OVERLAY_INCLUDED=NO
TITAN_TOUCHPAD_DAEMON_INCLUDED=NO
```

## Decision posture

N0C exists because RestlessOS upstream is tracking Android 17 / GrapheneOS
faster than the Android 16 branch. That is a maintenance and reproducibility
reason, not a Titan hardware requirement.

Android 17 does not replace Android 16 as the SableOS Titan baseline until it
passes physical Titan 2 validation. R7D Android 16 remains the immediate build
lane.

## Candidate images

Primary Android 17 candidate:

```text
RESTLESSOS_TAG=17.0.0-202609271006
GRAPHENEOS_TAG=2026091900
OFFICIAL_IMAGE=RestlessOS-arm64-ab-17.0.0-202609271006.img.xz
OFFICIAL_IMAGE_XZ_SHA256=5e8dfeab2078362497b473696f7ff7acecb2b03be1722dcd69121dd15f4cc67d
VALIDATION_SCOPE=full G/P0/P1/P2 table
```

Control image:

```text
RESTLESSOS_TAG=17.0.0-202609222215
GRAPHENEOS_TAG=2026091900
OFFICIAL_IMAGE=RestlessOS-arm64-ab-17.0.0-202609222215.img.xz
OFFICIAL_IMAGE_XZ_SHA256=bd4590502726e83c6c0c8491a8cf2e016eb85666e9c19c0648194f79d81470fd
VALIDATION_SCOPE=G and P0 rows only
```

The SHA256 values are GitHub release asset digests for the compressed `.img.xz`
files. They prove the local download matches what was uploaded to GitHub. They
do not prove who built the image and are not a substitute for Sable source and
artifact reproducibility checks.

## Download and hash capture

```bash
mkdir -p /srv/data/sable-build/artifacts/titan2/n0c-restless-android17-official
cd /srv/data/sable-build/artifacts/titan2/n0c-restless-android17-official

# Download the official images manually or with gh/curl from the upstream release.
# Then verify the compressed asset digest.

echo "5e8dfeab2078362497b473696f7ff7acecb2b03be1722dcd69121dd15f4cc67d  RestlessOS-arm64-ab-17.0.0-202609271006.img.xz" \
  | sha256sum -c -

echo "bd4590502726e83c6c0c8491a8cf2e016eb85666e9c19c0648194f79d81470fd  RestlessOS-arm64-ab-17.0.0-202609222215.img.xz" \
  | sha256sum -c -

xz -dk RestlessOS-arm64-ab-17.0.0-202609271006.img.xz
xz -dk RestlessOS-arm64-ab-17.0.0-202609222215.img.xz

sha256sum RestlessOS-arm64-ab-17.0.0-202609271006.img \
  RestlessOS-arm64-ab-17.0.0-202609222215.img \
  | tee n0c-official-raw-image-sha256s.txt
```

If the upstream `.zsync` metadata is reachable, also capture the decompressed
image SHA-1 from the build host metadata:

```bash
curl -s https://build.chrisaw.io/RestlessOS-ab-17.0.0-202609271006/zsync/RestlessOS-arm64-ab-17.0.0-202609271006.img.zsync \
  | head -c 600 \
  | strings \
  | grep -E '^(Filename|MTime|Length|SHA-1):' \
  | tee -a n0c-official-raw-image-zsync.txt

sha1sum RestlessOS-arm64-ab-17.0.0-202609271006.img \
  | tee -a n0c-official-raw-image-zsync.txt
```

## Extract official image build identity

Do not assume the release tag suffix is the build number inside the image. Read
`BUILD_DATETIME` and `BUILD_NUMBER` from the official image.

```bash
cd /srv/data/sable-build/artifacts/titan2/n0c-restless-android17-official

file RestlessOS-arm64-ab-17.0.0-202609271006.img | tee n0c-official-image-file.txt

# EROFS example:
# fsck.erofs --extract=official-271006 RestlessOS-arm64-ab-17.0.0-202609271006.img

# ext4 example:
# mkdir -p official-271006
# sudo mount -o ro,loop RestlessOS-arm64-ab-17.0.0-202609271006.img official-271006

P=official-271006/system/build.prop
grep -E '^ro\.build\.(date\.utc|version\.incremental|id|tags|fingerprint)=' "$P" \
  | tee n0c-official-271006-build-props.txt

export BUILD_DATETIME="$(sed -n 's/^ro\.build\.date\.utc=//p' "$P")"
export BUILD_NUMBER="$(sed -n 's/^ro\.build\.version\.incremental=//p' "$P")"
printf 'BUILD_DATETIME=%s\nBUILD_NUMBER=%s\n' "$BUILD_DATETIME" "$BUILD_NUMBER" \
  | tee n0c-official-271006-repro-env.txt
```

Check whether `system`, `product`, and `system_ext` prop files agree on date and
incremental values. If they disagree in the official image, keep timestamp
differences on the comparison allow-list instead of forcing a false match.

## Source plan

Use the RestlessOS tag, not branch tip. Do not use scripts that silently resolve
the latest GrapheneOS tag for pinned reproducibility work.

```text
RESTLESSOS_REPO=sableos-project/treble_restlessos
UPSTREAM_REPO=cawilliamson/treble_restlessos
RESTLESSOS_TAG=17.0.0-202609271006
GRAPHENEOS_TAG=2026091900
PRODUCT=treble_arm64_bvN
VARIANT=userdebug for bring-up, user for reproduction/release comparison
```

Sanitize the private maintainer repository before sync:

```text
REMOVE_REMOTE=cawilliamson-priv
REMOVE_PROJECT=vendor/cawilliamson-priv
PRIVATE_SOURCE_ALLOWED=NO
```

Pin `vendor/apn` for reproducibility. For the 271006 candidate, use:

```text
vendor/apn=6e73ba90cc9438d1aefb4c323289b281b006a59a
```

For the 222215 control rebuild, use the older likely APN revision from the
control period:

```text
vendor/apn=ea228279c663bc9ac174188ee78b64912cf45273
```

The APN revision must be confirmed by comparing the official image
`apns-conf.xml` with the pinned source file. After confirmation, APN differences
are not allowed in reproducibility comparison.

## Patch tiers

Do not hard-code patch tiers. Read them from the pinned RestlessOS tag's own
workflow, then apply `debug-builds` for bring-up or `release-builds` for official
reproduction.

```bash
cd "$RESTLESSOS_DIR"
git checkout 17.0.0-202609271006
TIERS="$(sed -n 's/.*for tier in \(.*\); do.*/\1/p' .github/workflows/build.yml | head -1)"
printf 'RESTLESSOS_PATCH_TIERS=%s\n' "$TIERS"

cd "$SRC_DIR"
for tier in $TIERS debug-builds; do
  "$RESTLESSOS_DIR/patches/apply.sh" . "$tier"
done
```

Expected 271006 tier set:

```text
trebledroid rom personal
```

Older 222215 uses `trebledroid-staging` between `trebledroid` and `rom`.

## Build plan

Reproduction build first:

```bash
export BUILD_DATETIME="$BUILD_DATETIME"
export BUILD_NUMBER="$BUILD_NUMBER"
# If official build.prop includes eng.<user>.<host> style data, also export:
# export BUILD_USERNAME=...
# export BUILD_HOSTNAME=...

source build/envsetup.sh
lunch "treble_arm64_bvN-${ANDROID_VERSION_TAG}-user"
make systemimage -j"$(nproc --all)"
make target-files-package otatools -j"$(nproc --all)"
```

Research/bring-up build:

```bash
source build/envsetup.sh
lunch "treble_arm64_bvN-${ANDROID_VERSION_TAG}-userdebug"
make systemimage -j"$(nproc --all)"
make target-files-package otatools -j"$(nproc --all)"
```

## Image comparison policy

Compare extracted file trees, not raw `.img` bytes. Raw filesystem bytes can
differ because of image layout, inode ordering, compression, or hash tree salt.

Acceptable difference categories:

```text
- APK META-INF/signature blocks only
- APEX container/payload signatures and apex_pubkey only
- etc/selinux/plat_mac_permissions.xml certificate hex only
- etc/security/otacerts.zip certificate only
- TrebleApp.apk Gradle non-determinism if package name, versionCode and permissions match
- ro.build.tags and fingerprint/description fields that derive from signing state
- filesystem metadata, not extracted file contents
```

Not acceptable without investigation:

```text
- file present in one tree and missing in the other
- native binary or library byte differences
- .odex/.vdex/.art differences
- init .rc differences
- VINTF, linker config, permissions, sysconfig or priv-app XML differences
- SELinux policy differences other than plat_mac_permissions.xml certificate data
- overlay differences outside signature effects
- build.prop keys outside the signing/date allow-list
- APK entry differences outside META-INF
```

Comparison helper:

```bash
hashes() {
  (cd "$1" && find . -type f -print0 | sort -z | xargs -0 sha256sum) | sort -k2
}

join -j2 \
  <(hashes official-271006) \
  <(hashes ours-271006) \
  | awk '$2!=$3{print $1}' \
  > differing.txt

grep -v -E '\.(apk|apex|capex)$|/plat_mac_permissions\.xml$|/otacerts\.zip$|build\.prop$' differing.txt
```

After APN pin confirmation, `apns-conf.xml` is not allowed to differ.

## Signing policy

Use raw test keys only for the first unmodified reproduction artifact if needed.
From the first Sable N0C build onward, sign with `sable-dev` keys.

```text
sable-dev:
  used for N0C research artifacts and DSU tests after reproduction
  may live on the build machine
  may be rotated if lost or leaked

sable-release:
  generated early
  encrypted/offline
  not used until physical-flash gate and daily-use readiness
```

Generate one key for every certificate in the synced Android tree under:

```text
build/make/target/product/security/*.x509.pem
```

Also generate the AVB key and follow GrapheneOS release scripts for per-APEX
keys. Signing can introduce failures, so signed images must repeat validation.

## DSU test order

```text
1. Official 271006 image through DSU.
2. Official 222215 image through DSU for G/P0 control if needed.
3. Sable N0C unsigned/test-key image only if required to isolate signing.
4. Sable N0C sable-dev signed image.
```

DSU failure is evidence, not final proof that physical flash will fail. If DSU
fails and the artifact is still important, perform a separate physical-flash gate
only after restore path, stock firmware, vbmeta strategy, and wipe policy are
explicitly approved.

## Acceptance table

Android 17 becomes a candidate base only when every G and P0 item passes, and
every P1 failure either passes later or has a known cause that is plausibly
system-image-fixable rather than vendor-firmware-bound.

| ID | Tier | Feature | Pass condition |
|---|---|---|---|
| G1 | G | Boot | `sys.boot_completed=1` within 5 minutes, twice, no bootloop |
| G2 | G | Stability | 30 minutes use, no `system_server`, SurfaceFlinger, audio, camera, or radio crashes |
| G3 | G | Display/touch | Titan 2 display/touch geometry correct; touch works across all edges |
| G4 | G | SELinux baseline | enforcing; denials recorded and classified |
| K1 | P0 | Keyboard typing | every key produces expected character; on-screen keyboard hides while typing |
| K2 | P0 | Keyboard events | every key emits distinct `getevent -l` code |
| R1 | P0 | SIM detection | both SIMs detected with network names |
| R2 | P0 | Mobile data | data works on 4G and 5G where available |
| R3 | P0 | Voice calls | incoming/outgoing calls, two-way audio, proximity behavior |
| R4 | P0 | SMS | send/receive on each SIM |
| W1 | P0 | Wi-Fi | 2.4 GHz and 5 GHz connect and persist after reboot |
| A1 | P0 | Audio | speaker, earpiece, mic, USB-C headset work |
| P1 | P0 | Charging/USB | charging, MTP/file transfer, adb work |
| R5 | P1 | VoLTE/VoWiFi | IMS registered or failure classified; TrebleDroid MTK toggles tested |
| B1 | P1 | Bluetooth | headset music/call audio and reconnect |
| C1 | P1 | Camera | front/rear photo and 1080p video with audio |
| F1 | P1 | Fingerprint | enroll and at least 8/10 unlocks |
| S1 | P1 | Sensors | rotate, brightness, proximity respond |
| L1 | P1 | GPS | GPS-only fix within 2 minutes outdoors |
| Z1 | P1 | Sleep/wake | wake after 30 minutes screen off; alarm fires |
| N1 | P1 | NFC | tag read; tap-to-pay not expected |
| E1 | P2 | eSIM | document; expected risk |
| T1 | P2 | Programmable key | emits remappable key code |
| T2 | P2 | Rear screen | second display detection documented |
| T3 | P2 | Keyboard backlight/shortcuts/touch-scroll/IR | document state |
| V1 | P2 | Vibration | typing and notification haptics documented |

Physical-flash-only checks after DSU:

```text
FIRST_BOOT_AFTER_FASTBOOT_W=PASS_REQUIRED
ENCRYPTION_SURVIVES_REBOOT=PASS_REQUIRED
OVERNIGHT_BATTERY_DRAIN_RECORDED=YES
SIGNED_RELEASE_RETEST=REQUIRED
```

## Titan-specific input enablement is out of N0C baseline

Do not include `titan2-touchpadd`, custom keylayout remaps, `/dev/uinput` policy,
or rear-screen integration in the first N0C baseline image. Track them under N2
Titan input/display enablement after bootable baseline validation.

Required before integration:

```text
STOCK_KEYLAYOUT_EXTRACTED=YES
GETEVENT_MATRIX_CAPTURED=YES
SOURCE_OR_PREBUILT_PROVENANCE_REVIEWED=YES
GPLV3_DISTRIBUTION_OBLIGATIONS_RECORDED=YES
SELINUX_DENIALS_CAPTURED_FROM_REAL_DEVICE=YES
NO_PERMISSIVE_POLICY=YES
```
