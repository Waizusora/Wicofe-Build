# Build Documentation Index

This directory documents whole-system build orchestration and toolchain infrastructure.

## Scope
- Cross-repository build graph and orchestration.
- Pinned compiler, linker, bootloader, and image toolchains.
- x86-64 and AArch64 cross-compilation.
- Emulator/hardware test launch helpers.
- Reproducibility metadata and artifact handoff.

## Ownership
Each component repository owns its local build and test definitions. Wicofe-Build coordinates full-system builds and produces inputs for [Wicofe-Image](https://github.com/Waizusora/Wicofe-Image). It must not duplicate component-specific build logic without a documented reason.

## Reproducibility
Pin tools and source revisions, isolate generated artifacts, verify downloads, and make failures diagnosable. Never download and execute unverified tools during release builds.

- [Central documentation index](https://github.com/Waizusora/Wicofe-Docs/blob/main/INDEX.md)
- [Repository documentation map](https://github.com/Waizusora/Wicofe-Docs/blob/main/REPOSITORY_DOCUMENTATION_MAP.md)
