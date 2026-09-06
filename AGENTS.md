# AOCL-DLP Agent Guide

AOCL-DLP (Deep Learning Primitives) is a C/C++ CMake library of optimized low-precision GEMM and batch GEMM routines for AMD CPUs.

## Repository Layout

- `include/aocl_dlp/` — public C API headers (`aocl_dlp.h`, `aocl_dlp_types.h`).
- `src/` — implementation: reference, JIT, and optimized x86 kernels (AVX2/AVX512/AVX512_VNNI/AVX512_BF16/AVX512_FP16).
- `classic/` — legacy/reference implementations.
- `tests/` — unit and integration tests.
- `bench/` — micro-benchmarks.
- `examples/` — usage examples for each supported data-type combination.
- `cmake/` — CMake modules and platform detection.
- `scripts/` — helper scripts for building and testing.

## Build Commands

Standard CMake workflow:

```bash
cmake -S . -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build -j$(nproc)
```

Preset builds (see `CMakePresets.json`):

```bash
cmake --preset release
cmake --build --preset release
```

Install locally:

```bash
cmake --install build --prefix install
```

## Test Commands

```bash
ctest --test-dir build --output-on-failure
# or directly
./build/tests/aocl_dlp_test
```

## Lint / Code Style

- C/C++ formatting uses the project `.clang-format` if present; run `git diff --check` before committing.
- Prefer `snake_case` for C API symbols and `AOCL_DLP_` prefixed macros.
- Update `BUILD.md` and `INSTALL.md` when adding new data-type variants or build options.

## Key Conventions

- Data type suffix format: `<input_a><input_b><accum><output>` (e.g. `bf16bf16f32of32`).
- Pre/post operations are opt-in via the `aocl_post_op` / `aocl_pre_op` structures.
- Threading is OpenMP-based; set `OMP_NUM_THREADS` to control parallelism.

## Common Gotchas

- `u8s4s32os32` only exposes `reorder` and `get_reorder_buf_size` APIs, not full GEMM.
- Some INT8 paths require symmetric quantization; use the `_sym_quant` variants when applicable.
- AVX512_FP16 support requires a compatible Zen4+ or similar CPU.
