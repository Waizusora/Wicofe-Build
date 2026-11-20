# Frenit Build Integration 0.1

- **Status:** Proposed build integration; no native Wicofe Frenit artifact is verified.
- **Owner:** Wicofe-Build for whole-system orchestration; Frenit for component-local source/build/tests.
- **Component:** [Frenit — Free Init](https://github.com/Waizusora/Frenit).
- **Cross-component contract:** [WIC-SPEC-BOOT-FRENIT-001](https://github.com/Waizusora/Wicofe-Spec/blob/main/boot/FRENIT_INTEGRATION_CONTRACT_0.1.md).
- **Date:** 2026-11-18

## 1. Purpose

Define how the Frenit Rust userspace component will participate in a Wicofe system build. This is a build boundary, not a claim that the whole-system pipeline or native PID 1 is operational.

## 2. Component ownership

- Frenit owns its Cargo workspace, Rust source, local tests, package/executable naming and component release metadata.
- Wicofe-Build pins Frenit source revisions and toolchains, schedules target builds, records artifacts and validates build-graph dependencies.
- Wicofe-ABI owns public userspace ABI definitions.
- Wicofe-Image stages validated artifacts and manifest metadata into the root filesystem.
- Wicofe-Test owns cross-repository conformance and boot integration suites.

## 3. Target matrix

The initial Wicofe architecture targets are x86-64 and AArch64. A target is supported only when the Frenit binary, runtime dependencies, required ABI and kernel primitives are all available and validated for that target.

Host builds validate portable logic only. Linux musl or Android Bionic builds are not native Wicofe ABI conformance and must not be shipped as native Wicofe target artifacts.

## 4. Build graph node

A Frenit build node must record:

- immutable `Waizusora/Frenit` commit;
- pinned Rust toolchain, target triple, profile and Cargo feature set;
- direct/transitive dependency provenance and license evidence;
- required Wicofe-ABI revision/capability baseline;
- artifact type, architecture, hash and reproducible build metadata;
- unit-test, target-build and integration evidence, reported separately.

The canonical Cargo package and executable are named `frenit`. Do not infer a successful native target build from repository existence or a host test pass.

## 5. Artifact handoff

Wicofe-Build passes immutable, verified artifacts and metadata to Wicofe-Image. It does not define an installed executable path, root filesystem layout, init boot argument or service configuration schema. Those decisions must be made by the owning system-layout and integration specifications.

## 6. CI requirements

Separate host unit tests, native-target compilation, ABI checks, image integration, emulator boot tests and hardware tests. Report each architecture and evidence level independently. A successful Rust cross-compile is not proof of native PID 1 behavior or bootability.

## 7. Release gate

A release candidate requires pinned sources/toolchain, verified dependency licenses, reproducible artifact metadata, target-specific tests and a passing cross-component boot contract. Frenit must not be selected as production PID 1 until all mandatory kernel/ABI gates pass.
