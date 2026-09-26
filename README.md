# Bouffalo Toolchain Releases

Pre-built Xuantie GNU Toolchain binaries for Bouffalo Lab chips (BL616, BL808, etc.).

Upstream: <https://github.com/XUANTIE-RV/xuantie-gnu-toolchain>  
Pinned ref: **`V3.0.1`**

> **This repo is for downloading pre-built binaries only.** There is nothing useful to clone here.

---

## Available Platforms

| Platform | File |
|---|---|
| macOS 26 (Tahoe) · Apple Silicon | `xuantie-gnu-toolchain-macos-26-arm64.tar.gz` |
| Linux x86_64 (Ubuntu 22.04) | `xuantie-gnu-toolchain-linux-x86_64.tar.gz` |
| Linux ARM64 (Ubuntu 24.04) | `xuantie-gnu-toolchain-linux-arm64.tar.gz` |
| Windows x86_64 | `xuantie-gnu-toolchain-windows-x86_64.tar.gz` |

---

## Download & Verify

Go to [Releases](../../releases) and grab the tarball for your platform plus its `.sha256` file.

**macOS / Linux:**
```bash
shasum -a 256 -c xuantie-gnu-toolchain-macos-26-arm64.tar.gz.sha256
```

**Windows (PowerShell):**
```powershell
Get-FileHash xuantie-gnu-toolchain-windows-x86_64.tar.gz -Algorithm SHA256
```

---

## Install

### macOS / Linux

```bash
sudo mkdir -p /opt/xuantie
sudo tar -xzf xuantie-gnu-toolchain-macos-26-arm64.tar.gz -C /opt/xuantie
export PATH=/opt/xuantie/bin:$PATH
riscv64-unknown-elf-gcc --version
```

Add to `~/.zshrc` (macOS) or `~/.bashrc` (Linux) to make it permanent:

```bash
export PATH=/opt/xuantie/bin:$PATH
```

### Windows

Extract the `.tar.gz` (e.g. with [7-Zip](https://www.7-zip.org/)) to a folder such as `C:\xuantie`, then add `C:\xuantie\bin` to your `PATH`.

---

## Use with Bouffalo SDK

Set `CROSS_COMPILE` to the toolchain prefix, e.g. in your `Makefile`:

```makefile
CROSS_COMPILE ?= /opt/xuantie/bin/riscv64-unknown-elf-
```

---

## Licensing

The toolchain is GPL-licensed. See `LICENSE.note` for details and the link to the pinned upstream source.
