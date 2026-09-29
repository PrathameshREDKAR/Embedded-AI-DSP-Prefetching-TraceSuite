# Athena Compatibility Patch

This directory contains the exact Athena compatibility source file used for the final DSP trace-suite evaluation.

## Original Athena source

Repository:
https://github.com/CMU-SAFARI/Athena.git

Frozen commit used for this project:

1cf9be66d1e9449a040ea2dda944af36fdae3f28

The original Athena source tree was preserved unchanged.

See:

source_identity/ATHENA_FROZEN_SOURCE.txt
source_identity/ATHENA_SOURCE_MANIFEST_SHA256.txt

## Why the compatibility patch was required

During FIR trace replay, the original Athena build stalled because pseudo/special registers in the generated instruction trace could participate in register-dependency tracking.

The compatibility version modifies only the relevant register-dependency handling so that the intended pseudo-register filtering is applied.

No workload trace was regenerated because of this change.

The compatibility version was subsequently used consistently for the final DSP-suite evaluation.

## Compatibility source file

File:

compatibility_patch/ooo_cpu.cc

SHA-256:

3c3465f1efded7d602489ab4d42cbef644dd4ea528d390874c5fa114052d01cd

## Reproduction

1. Clone Athena.
2. Checkout the frozen commit:

   git checkout 1cf9be66d1e9449a040ea2dda944af36fdae3f28

3. Replace:

   src/ooo_cpu.cc

   with:

   compatibility_patch/ooo_cpu.cc

4. Build Athena using the normal Athena build procedure.

5. Verify the patched source:

   sha256sum src/ooo_cpu.cc

Expected SHA-256:

3c3465f1efded7d602489ab4d42cbef644dd4ea528d390874c5fa114052d01cd

## Validation

The included:

CP13_FIR_CLEAN_COMPATIBILITY_SMOKE.txt

records the clean compatibility smoke validation used during the project.

## Important

The patch is provided only to reproduce the trace-suite evaluation environment.

The upstream Athena repository remains the authoritative source for Athena itself.
