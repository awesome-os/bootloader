> [!NOTE] Consolidated into the unified Holo v11 OS monorepo at https://github.com/awesome-os/awesome-os

# Awesome-OS Bootloader & Zero-Flicker Splash Suite (`awesome-os/bootloader`)

Sovereign multi-bootloader implementations and deterministic UEFI GOP / Limine / SimpleDRM splash screen assets for **Holo-Linux `7.3.6`** (`B8G_ABI_MAGIC_V3=0x33564742`, `b8g_abi.ko`, `scx_holo`).

## Why This Repo Exists

Boot splash screens break when bootloaders, UEFI GOP, and Linux `SimpleDRM` disagree on pixel format, BMP compression, or console handoff. This repository solves that with one deterministic pipeline:

| Directory | Purpose |
| :--- | :--- |
| [`limine/limine.conf`](limine/limine.conf) | **Limine `v11/v12`** dual-slot (`slot-a` / `slot-b`) A/B rollback config with native `24-bit` BMP wallpaper, zero-flicker desktop boot, and serial diagnostic/recovery entries. |
| [`uefi-gop-splash/`](uefi-gop-splash/) | Minimal **C11 UEFI GOP & Linux `SimpleDRM` framebuffer splash blitter** (`holo_splash.c`) that blits uncompressed 24-bit BMPs into any `BGRA8`/`RGBA8` linear framebuffer with zero flicker. |
| [`systemd-boot/`](systemd-boot/) | Drop-in `loader.conf` + dual-slot `entries/awesome-os-slot-{a,b}.conf` for existing UEFI ESPs. |
| [`ipxe/boot.ipxe`](ipxe/boot.ipxe) | Stateless HTTPS iPXE netboot into RAM `tmpfs` `v11` CAS (`b8g.abi=v3 scx_holo=1`). |
| [`assets/`](assets/) | Deterministic **Lumi + Awesome-OS** splash generator (`generate-splash.mts`) producing guaranteed uncompressed `24-bit BI_RGB` BMPs (`1920x1080`, `1024x768`, `640x480`) + vector `splash.svg`. |

## Quick Verify

```powershell
# Regenerate deterministic splash BMPs & SVG
node assets/generate-splash.mts

# Run bootloader & splash asset verification tests
node --test bootloader.test.mts

# Compile & run C11 framebuffer splash blitter self-test
zig cc -std=c11 -Wall -Wextra -Werror uefi-gop-splash/holo_splash.c uefi-gop-splash/holo_splash_test.c -o holo_splash_test.exe
./holo_splash_test.exe
```
