# Embedded AI/DSP Prefetching Trace Suite

This repository contains the frozen artifact package for an embedded AI/DSP hardware-prefetching study using Athena/ChampSim-style trace-driven simulation.

## Goal

The project evaluates multiple hardware prefetchers on the exact same frozen DSP traces to study how workload memory behavior affects prefetching effectiveness.

Workloads:
- FFT
- FIR
- 2D Convolution
- Matrix Multiplication
- IIR

## Athena

Upstream repository: https://github.com/CMU-SAFARI/Athena.git

Frozen commit:
1cf9be66d1e9449a040ea2dda944af36fdae3f28

See the athena/ directory for the frozen source identity and compatibility source used in the final experiments.

## Trace format

Each trace is a raw sequence of fixed 64-byte dynamic instruction records.

Layout:
- uint64 instruction pointer at byte 0
- uint8 branch flag at byte 8
- uint8 branch taken at byte 9
- destination registers beginning at byte 10
- source registers beginning at byte 12
- destination memory addresses beginning at byte 16
- source memory addresses beginning at byte 32

Sanity check: raw trace size / 64 = dynamic instruction record count.

## Final trace suite

| Workload | Repetitions | Trace records |
|---|---:|---:|
| FFT | 100 | 69,607,705 |
| FIR | 13 | 68,105,642 |
| Convolution | 3 | 65,426,935 |
| MatMul | 2 | 101,321,253 |
| IIR | 14 | 62,390,447 |

All traces exceed the 60 million instruction simulation window: 10M warmup + 50M measured.

## Final IPC results

| Workload | NOPREF | IPCP | BERTI | PYTHIA |
|---|---:|---:|---:|---:|
| FFT | 1.58277 | 1.60032 | 1.63517 | 1.63849 |
| FIR | 0.96770 | 0.96817 | 0.98347 | 0.97738 |
| Convolution | 1.57119 | 1.74082 | 1.74230 | 1.66645 |
| MatMul | 0.67875 | 0.99799 | 0.99688 | 0.68020 |
| IIR | 0.30975 | 0.51909 | 0.51239 | 0.51182 |

## Improvement over no prefetching

| Workload | IPCP | BERTI | PYTHIA |
|---|---:|---:|---:|
| FFT | +1.109% | +3.311% | +3.521% |
| FIR | +0.049% | +1.630% | +1.000% |
| Convolution | +10.796% | +10.891% | +6.063% |
| MatMul | +47.034% | +46.870% | +0.214% |
| IIR | +67.583% | +65.420% | +65.237% |

Improvement formula:
100 * (NOPREF_cycles / PREFETCHER_cycles - 1)

## Main observation

Prefetcher effectiveness is strongly workload-dependent. Improvement ranges from nearly zero to more than 65 percent, and no single prefetcher is best across every workload.

## Trace generation

Intel Pin was used for dynamic binary instrumentation.
The custom tracer source is:
tracer/athena_tracer.cpp

Intel Pin itself is not included in this repository.

## Checksums

Trace hashes:
checksums/SHA256SUMS.txt

Source hashes:
checksums/SOURCE_SHA256SUMS.txt

## Athena compatibility

The exact compatibility source used for the final suite is:
athena/compatibility_patch/ooo_cpu.cc

SHA-256:
3c3465f1efded7d602489ab4d42cbef644dd4ea528d390874c5fa114052d01cd

See athena/README.md for details.

## Repository contents

- athena/ - Athena source identity and compatibility file
- checksums/ - frozen SHA-256 manifests
- results/ - final validation and result files
- tracer/ - custom Intel Pin tracer source
- traces/ - five final compressed traces
- workloads/ - final DSP workload source files

## Status

- trace generation complete
- trace validation complete
- DSP suite frozen
- NOPREF/IPCP/BERTI/PYTHIA evaluation complete
- reproducibility package prepared

Future adaptive or phase-aware prefetching work will be considered only if supported by further evidence.
