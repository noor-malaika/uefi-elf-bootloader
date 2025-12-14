# Development Container for UEFI ELF Bootloader

This devcontainer provides a complete development environment for building and testing the UEFI ELF bootloader.

## What's Included

- **x86_64-elf cross-compiler toolchain** (GCC 13.2.0 + Binutils 2.40)
- **QEMU** for testing the bootloader
- **OVMF** UEFI firmware for QEMU
- **GNU EFI** development libraries
- **mtools** for creating FAT filesystem images
- **Build tools** (Make, etc.)

## Quick Start

1. Open this repository in GitHub Codespaces or VS Code with Dev Containers extension
2. Wait for the container to build (first time only, takes ~10-15 minutes)
3. Once ready, run:
   ```bash
   ./run
   ```

This will build the bootloader and kernel, create a bootable disk image, and launch it in QEMU.

## Manual Build Steps

If you want to build manually:

```bash
# Build everything
cd src
make

# Run in QEMU
make emu

# Clean build artifacts
make clean
```

## Debugging

To debug the bootloader:

```bash
cd src
make debug
```

Then connect with GDB on port 1234.

## Environment Details

- Cross-compiler: `/usr/local/bin/x86_64-elf-gcc`
- OVMF firmware: `/usr/local/share/OVMF.fd`
- All tools are in PATH and ready to use
