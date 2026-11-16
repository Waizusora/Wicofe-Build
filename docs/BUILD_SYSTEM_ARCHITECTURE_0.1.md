# Wicofe Build System Architecture 0.1

**Status:** Draft proposal. This document defines whole-system build orchestration; it does not claim that a complete end-to-end build pipeline exists.

## 1. Purpose

Wicofe-Build coordinates source revisions and component build outputs into validated system-build inputs for Wicofe-Image. It provides a reproducible dependency graph, target-aware orchestration, integration checks, and build provenance.

The repository is an orchestrator, not a replacement for component build systems.

## 2. Ownership boundaries

| Repository | Canonical responsibility |
|---|---|
| Wicofe-Build | Whole-system build graph, orchestration, pinned integration environment, cross-repository build metadata, integration build |
| Wicofe-SDK | Developer-facing compiler/toolchain distribution, target profiles, sysroots, SDK packaging |
| Wicofe-Kernel | Kernel source and kernel-local build, link, boot validation |
| Wicofe-ABI | Canonical syscall and userspace ABI definitions |
| Wicofe-Tests | Cross-repository contract, integration, system and hardware test suites |
| Wicofe-Image | Filesystem tree, package selection, bootable disk/VM image construction and image validation |
| Wicofe-ThirdParty | Upstream provenance, license and compliance records |
| Wicofe-Ports | Third-party build recipes and integration patches |
| Component repositories | Their source, local build definitions, unit tests and component artifacts |

Wicofe-Build may invoke these repositories' documented interfaces, but must not fork their canonical specifications or silently duplicate their implementation logic. Image assembly is handed off to Wicofe-Image; Build supplies a manifest and validated artifacts.

## 3. Build graph and dependency model

Represent a system build as a directed acyclic graph (DAG). Each node declares:
- stable component identifier and source repository;
- immutable source commit or release reference;
- target architecture/profile;
- toolchain and build-environment identity;
- declared input artifacts and dependency edges;
- exact build command/interface;
- output paths, types and hashes;
- environment variables and configuration inputs;
- cacheability and side-effect policy.

Reject cycles, missing inputs, ambiguous dependency versions, undeclared host tools, and mutable source references in release mode. Separate build-time dependencies from target-runtime dependencies.

The graph should support partial builds while preserving dependency correctness. A changed component invalidates its downstream dependants according to declared inputs, not timestamps alone.

## 4. Target profiles

Initial system targets are:
- x86-64;
- AArch64.

A target profile must explicitly identify architecture, CPU baseline, ABI revision, object/executable format, linker configuration, endianness, pointer width, feature set, and runtime assumptions. Do not infer target support merely because a compiler accepts a target flag.

Host tools execute on the build host; target artifacts execute on Wicofe. Record these as separate artifact classes. Cross-build outputs must never be accidentally linked against host headers or libraries.

Use the canonical target profile and sysroot supplied by Wicofe-SDK when available. Until the SDK freezes target triples and ABI details, keep profile identifiers versioned and explicitly provisional.

## 5. Toolchain and dependency resolution

Pin compiler, linker, assembler, boot tooling, generators, and other build tools by version and provenance. The build environment must record:
- tool name, version and binary/source digest;
- origin and license record;
- patches and configuration;
- host platform requirements;
- target profile compatibility.

Resolve dependencies from declared, versioned sources. Release builds should support offline operation after inputs are provisioned. Network retrieval must verify integrity and must not execute untrusted content without isolation and review.

Do not treat a successful dependency download or license label as approval. Verify the exact component and transitive dependency records through Wicofe-ThirdParty.

## 6. Build stages

A proposed orchestration pipeline:

1. **Preflight:** validate repository revisions, working-tree policy, target profile, toolchain availability and required inputs.
2. **Resolve:** construct and validate the dependency DAG and lock all source/tool versions.
3. **Prepare:** provision isolated build directories, sysroots, environment and caches.
4. **Compile:** invoke component-owned build interfaces for host tools and target artifacts.
5. **Integrate:** validate ABI/version compatibility and assemble a component artifact manifest.
6. **Test:** run component checks and selected Wicofe-Tests contract/integration suites.
7. **Handoff:** provide Wicofe-Image with immutable artifacts, package selection metadata, hashes and provenance.
8. **Attest:** retain build logs, test evidence, toolchain inventory and final manifest.

A stage may be skipped only when its required outputs are already available and verified against the current graph.

## 7. Reproducibility and caching

A reproducible build should derive outputs from declared source, toolchain, configuration and environment inputs. Normalize timestamps, locale, timezone, filesystem ordering, archive metadata and other known nondeterministic inputs where practical.

Cache keys must include all output-affecting inputs, including source revisions, dependency hashes, target profile, compiler/linker versions, build flags, generated configuration and relevant environment identity. Never reuse a cache entry solely because a path or filename matches.

Provide a clean-build mode that bypasses caches. Compare independent clean builds and report byte-for-byte reproducibility status separately from functional correctness.

## 8. Artifact contract and image handoff

Every artifact must have a machine-readable manifest containing:
- artifact ID, type, target architecture and ABI compatibility;
- producing repository and source commit;
- build graph node and configuration digest;
- toolchain identity;
- dependencies and license/provenance references;
- content hash and size;
- validation status and linked test evidence.

Wicofe-Build does not decide the final partition layout, filesystem image format, bootable disk layout, or release image signing policy. It hands verified inputs to Wicofe-Image under an explicit versioned interface.

## 9. CI and validation

CI should execute the same declared graph and commands as local/release builds. Separate:
- formatting and static checks;
- component compile checks;
- cross-compilation;
- ABI/contract checks;
- integration tests;
- emulator boot tests;
- physical hardware tests;
- reproducibility and provenance audits.

Report each architecture and validation level independently. A successful compile is not evidence of bootability; emulator success is not physical hardware validation. Preserve logs and exact source/toolchain revisions for every result.

## 10. Security and failure handling

Run builds with least privilege in isolated workspaces. Treat build scripts, generators, compiler plugins and dependency hooks as executable supply-chain inputs. Prevent path traversal, output-directory escape, unsafe symlink handling and accidental writes outside the workspace.

Failures must identify the graph node, command, target, relevant inputs and log location. Do not continue downstream after a required dependency fails. Cleanup must not delete user-provided inputs or shared caches without explicit policy.

## 11. Licensing

Original Wicofe-Build code targets MPL-2.0. Build outputs and bundled dependencies retain their applicable upstream licenses and notices. The build manifest should link to exact third-party records; do not infer compatibility from repository-level labels alone.

## 12. Testing requirements

- DAG cycle, missing-node and invalid-edge tests.
- Immutable source/toolchain lock validation.
- Host/target artifact separation and sysroot contamination tests.
- Cache invalidation and clean-build equivalence tests.
- Reproducibility checks on independent workers.
- Failure propagation and interrupted-build recovery tests.
- Artifact manifest schema and hash verification.
- ABI compatibility and integration test invocation.
- Handoff contract tests with Wicofe-Image.
- Supply-chain provenance and license inventory checks.

## 13. Open decisions

- Build graph and manifest serialization format.
- Orchestration implementation language and CLI contract.
- Exact component build interfaces and versioning.
- Target profile identifiers and ABI lock strategy.
- Toolchain provisioning and offline mirror policy.
- Cache backend, trust model and retention.
- CI runner architecture and emulator matrix.
- Artifact signing and attestation format.
- Release branch/tag and reproducibility policy.

## 14. Completion criteria

A production pipeline requires a versioned DAG/manifest contract, pinned toolchains, successful clean builds for each claimed architecture, host/target isolation, tested cache invalidation, reproducibility evidence, integration tests, verified image handoff, and retained provenance. Until these exist, this document remains a design proposal.
