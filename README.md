# Wicofe-Build

Build orchestration and toolchain infrastructure for Wicofe OS.

## Scope

- Cross-repository build orchestration.
- Pinned compiler, linker, and boot toolchain definitions.
- Cross-compilation for x86-64 and AArch64.
- Emulator and hardware test launch helpers.
- Reproducible build metadata and artifact handoff.

## Design principles

Build steps should be deterministic, documented, and scriptable. Pin tool versions, record source revisions, isolate generated artifacts, and make failures diagnosable. Do not download unverified build tools at execution time.

## Boundaries

Individual repositories own their local build definitions and tests. Wicofe-Build coordinates whole-system builds and produces inputs for Wicofe-Image.

## Frenit artifact integration

[Frenit](https://github.com/Waizusora/Frenit) is the canonical init-system artifact. Wicofe-Build owns whole-system orchestration, not Frenit's source or CLI. A future target build must pin the Frenit commit, target triple, Rust toolchain, dependency graph and artifact hash, then publish ABI compatibility metadata. This pipeline is planned; no native Wicofe Frenit artifact handoff is currently verified. Follow [WIC-SPEC-BOOT-FRENIT-001](https://github.com/Waizusora/Wicofe-Specs/blob/main/boot/FRENIT_INTEGRATION_CONTRACT_0.1.md).

## Status

Initial repository baseline; no complete end-to-end OS build pipeline is claimed. A reusable release dependency-license gate is available at `.github/workflows/release-dependency-license-gate.yml`; it invokes the strict 29-repository auditor from Wicofe-Tools. It is not a complete build/release pipeline and currently blocks until unresolved repository coverage and dependency approvals are recorded.

## License

Original project code targets MPL-2.0; bundled tools and dependencies retain their own licenses.

## Documentation

See [docs/INDEX.md](docs/INDEX.md) for this repository's technical documentation. The [central repository documentation map](https://github.com/Waizusora/Wicofe-Docs/blob/main/REPOSITORY_DOCUMENTATION_MAP.md) identifies canonical ownership and cross-repository boundaries.

## Wicofe-wide principles

Wicofe is an independent Unix-like, POSIX-compatible operating system and software ecosystem, designed as an alternative to established and widely adopted software ecosystems and technologies, including GNU. Native components must use Wicofe interfaces and must not require Linux libc or GNU compatibility layers. Prefer permissive licenses; avoid GPL/AGPL, and consider LGPL only when no suitable permissive alternative exists. Reuse or fork upstream software when implementing it from scratch would be disproportionately difficult. See the [canonical project principles](https://github.com/Waizusora/Wicofe-Docs/blob/main/PROJECT_PRINCIPLES.md).
