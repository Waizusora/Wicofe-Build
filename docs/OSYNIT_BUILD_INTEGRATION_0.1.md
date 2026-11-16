# OSynit Build Integration 0.1

- **Status:** Proposed build integration
- **Owner:** Wicofe-Build for orchestration; OSynit for component-local build
- **Date:** 2026-11-16

## 1. Purpose

Define how the OSynit Rust userspace component participates in a Wicofe system build. This is a build contract, not a claim that the whole-system pipeline or native PID 1 is currently operational.

## 2. Component ownership

- OSynit owns its Cargo workspace, Rust source, local tests, executable and component release metadata.
- Wicofe-Build pins the OSynit source commit and toolchain, schedules the component build, records artifacts, and validates dependency edges.
- Wicofe-ABI owns public userspace ABI definitions.
- Wicofe-Image stages the validated executable and system configuration into the root filesystem.
- Wicofe-Tests owns cross-repository conformance and boot integration suites.

## 3. Target matrix

The initial Wicofe architecture targets are x86-64 and AArch64. A target is supported only when the OSynit binary, its runtime dependencies, the required ABI, and kernel primitives are all available and validated for that target.

Do not treat Android Bionic or Linux musl builds as native Wicofe ABI conformance. Host builds are for logic/unit tests unless explicitly identified as target artifacts.

## 4. Build graph node

The OSynit build node should declare:
- immutable repository revision;
- Rust toolchain and target profile;
- Cargo features and configuration;
- target ABI revision/capability baseline;
- direct and transitive dependency provenance/license records;
- executable artifact path, type, architecture and hash;
- test evidence and build logs.

Do not infer build success from the existence of a repository or README.

## 5. Artifact handoff

The expected system executable name is `osynit`; the project name is **OSynit**. The staged path is `/usr/sbin/osynit`, subject to the final root filesystem policy.

Wicofe-Build passes immutable, verified artifacts and metadata to Wicofe-Image. It does not own the image's filesystem tree or boot layout.

## 6. CI requirements

Separate host unit tests, target compilation, ABI checks, integration tests, emulator boot tests, and hardware tests. Report each architecture and evidence level independently. A successful Rust cross-compile is not proof of native PID 1 behavior or bootability.

## 7. Release gate

A release candidate requires pinned sources/toolchain, verified dependency licenses, reproducible artifact metadata, target-specific tests, and a passing cross-component boot contract. Until these gates exist, this document remains a proposed integration.
