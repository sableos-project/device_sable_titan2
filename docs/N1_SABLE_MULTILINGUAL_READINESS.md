# Titan 2 N1-SABLE Multilingual Readiness

This device-facing note records the Titan 2 / Titan 2 Elite implications of the N1-SABLE multilingual readiness decision.

## Decision

```text
N1_MULTILINGUAL_READINESS_REQUIRED=YES
FULL_LANGUAGE_PACKS_REQUIRED_IN_N1=NO
KEYBOARD_ONLY_MULTILINGUAL=NOT_ACCEPTABLE
UI_AND_KEYBOARD_LANGUAGE_ALIGNMENT=REQUIRED
```

The physical keyboard and the Sable UI must be planned together. Titan 2 should not gain multiple hardware keyboard layouts while Sable first-party UI remains hardcoded or English-only by architecture.

## Titan-specific interpretation

```text
N0-B-TREBLE=inherit baseline Android/RestlessOS language behavior
N1-SABLE=define SableOS locale + keyboard architecture
N2-TITAN2=validate real hardware behavior after qualified artifact exists
```

N1 is not required to ship full translations for many languages. N1 is required to keep the product translation-safe and input-method-safe.

## Physical keyboard split

Titan keyboard support must split these responsibilities:

```text
.kl=hardware scan-code mapping
.kcm=character/dead-key mapping
IME=composition, transliteration, prediction, and complex-script input
```

Latin-script layouts may start with `.kl` / `.kcm` support. Complex scripts require an IME plan, not just a keymap.

## Required N1 gate

The Sable overlay work should introduce or stage:

```text
N1_SABLE_LOCALE_KEYBOARD_CONTRACT
```

Expected checks:

```text
HARDCODED_STRING_SCAN=PASS
RESOURCE_LOCALE_STRUCTURE=PASS
PHYSICAL_KEYBOARD_LAYOUT_REGISTRY=PASS
FONT_FALLBACK_POLICY=PASS
RTL_SAFETY_REVIEW=PASS
IME_POLICY_DOCUMENTED=PASS
DEVICE_CONTACT=NO_UNLESS_N2_VALIDATION_APPROVED
```

## Device validation handoff

N2-TITAN2 should validate:

```text
hardware key behavior
layout switching
Android locale switching
Sable UI truncation/overflow behavior
font fallback on selected non-English samples
IME behavior for any complex-script support included in the image
```

No device contact or flash attempt is authorized by this document.
