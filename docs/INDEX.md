# Build Documentation Index

This directory documents whole-system build orchestration and toolchain infrastructure.

## Architecture
- [Build system architecture 0.1](BUILD_SYSTEM_ARCHITECTURE_0.1.md) — draft design for dependency graphs, target-aware builds, reproducibility, CI and artifact handoff.
- [Frenit build integration 0.1](FRENIT_BUILD_INTEGRATION_0.1.md) — target-aware Frenit artifact build, provenance, ABI metadata and image handoff.

## CI and release compliance
- [Release dependency license gate](../.github/workflows/release-dependency-license-gate.yml) — reusable/tag-triggered strict cross-repository Policy 0.2 gate; currently blocks on unknown coverage and unapproved build-time dependencies.

## Whole-system product direction
- [Wicofe system delivery and installation direction 0.1](https://github.com/Waizusora/Wicofe-Docs/blob/main/SYSTEM_DELIVERY_AND_INSTALLATION_DIRECTION_0.1.md) — planned flexible stable deliveries and out-of-band critical security releases; not a completed release pipeline.

## Scope
- Cross-repository build graph and orchestration.
- Pinned compiler, linker, bootloader, and image toolchains.
- x86-64 and AArch64 cross-compilation.
- Emulator/hardware test launch helpers.
- Reproducibility metadata and artifact handoff.

## Ownership
Each component repository owns its local build and test definitions. Wicofe-Build coordinates full-system builds and produces validated inputs for [Wicofe-Image](https://github.com/Waizusora/Wicofe-Image). It does not own image composition, SDK distribution, canonical ABI definitions, or component-local implementation.

## Reproducibility
Pin tools and source revisions, isolate generated artifacts, verify downloads, and make failures diagnosable. Never download and execute unverified tools during release builds.

- [Central documentation index](https://github.com/Waizusora/Wicofe-Docs/blob/main/INDEX.md)
- [Repository documentation map](https://github.com/Waizusora/Wicofe-Docs/blob/main/REPOSITORY_DOCUMENTATION_MAP.md)
