# Bouffalo macOS ARM64 Toolchain

Native Apple Silicon (arm64) build of the Xuantie GNU Toolchain for Bouffalo Lab chips (BL616, BL808, etc.).

Upstream: <https://github.com/XUANTIE-RV/xuantie-gnu-toolchain> (successor of `T-head-Semi/xuantie-gnu-toolchain`).
Pinned upstream ref for reproducible builds: **`V3.0.1`** (`c66309b74edd581125a8165f07296c85d3fa71c9`, branch `xuantie-gnu-toolchain`).
See `.github/workflows/build.yml` (`XUANTIE_REF`) and `LICENSE.note`.

Why native? Rosetta 2 is on a deprecation path (expected to remain through macOS 26/27). An arm64 toolchain is future-proof.

## Download

Go to [Releases](../../releases) and download `xuantie-gnu-toolchain-macos-arm64.tar.gz` (plus `.sha256`).

Verify:

```bash
shasum -a 256 -c xuantie-gnu-toolchain-macos-arm64.tar.gz.sha256
```

## Install

```bash
sudo mkdir -p /opt/riscv-toolchain/xuantie
sudo tar -xzf xuantie-gnu-toolchain-macos-arm64.tar.gz -C /opt/riscv-toolchain/xuantie --strip-components=1
export PATH=/opt/riscv-toolchain/xuantie/bin:$PATH
riscv64-unknown-elf-gcc --version
file $(which riscv64-unknown-elf-gcc)  # should say arm64
```

Add to `~/.zshrc`:

```bash
export PATH=/opt/riscv-toolchain/xuantie/bin:$PATH
```

## Use with Bouffalo SDK

In your project `Makefile`:

```makefile
CROSS_COMPILE ?= /opt/riscv-toolchain/xuantie/bin/riscv64-unknown-elf-
```

In `bouffalo_sdk/cmake/bflb_flash.cmake`, set:

```cmake
set(BL_FW_POST_PROC ${BL_SDK_BASE}/tools/bflb_tools/bflb_fw_post_proc/bflb_fw_post_proc-macos)
```

> **Note:** `bflb_fw_post_proc-macos` in the SDK is currently x86_64. If it hasn't been updated to arm64, you'll need to build it from source or continue using Rosetta for that one helper until a native build is available.

## Build your own release

This repo builds on a native `macos-14` (M1) GitHub Actions runner:

```bash
git tag v1.0.0
git push origin v1.0.0
```

Wait ~1–2 hours, then check Releases. You can also trigger manually via Actions → `workflow_dispatch`.

To update the toolchain later, bump `XUANTIE_REF` in `.github/workflows/build.yml` to the new stable tag (check <https://github.com/XUANTIE-RV/xuantie-gnu-toolchain/releases>), commit, and push a new `v*` tag.

To include GDB, set `ENABLE_GDB: 'true'` in `.github/workflows/build.yml` (default is `'false'` for a faster build).

## Troubleshooting

- `newlib` failure is the usual suspect on macOS. See the commented Pine64 patch line in `build.yml`.
- The workflow intentionally inits only the `binutils`/`gcc`/`newlib` submodules (`+gdb` when enabled). A full `--recursive` checkout is broken at `V3.0.1` (stale `dejagnu` ref upstream) and pulls unneeded `llvm`/`qemu`/`glibc` (~6.65 GB full clone).
- `M linux-headers/...netfilter/...` case-collision warnings on macOS runners are harmless for newlib builds (case-insensitive APFS; those headers are linux-only).
- If `macos-14` is retired, try `macos-15` (also arm64).

## Licensing

See `LICENSE.note`. The toolchain is GPL-licensed; redistributed binaries must comply (source offer / link to pinned upstream commit).
