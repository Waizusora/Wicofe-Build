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

## Status

Initial repository baseline; no complete end-to-end OS build pipeline is claimed.

## License

Original project code targets MPL-2.0; bundled tools and dependencies retain their own licenses.
