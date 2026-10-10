# Project implementation progress

_Last reviewed: 2026-10-10. This is a qualitative overview based on repository documentation and visible implemented functionality, not a release certification or a substitute for tests._

This page covers publicly presented reusable libraries **and** larger software tools/projects. It complements the [main project overview](README.md), which describes purposes, data flows and dependencies.

## Status definitions

Implementation progress and reliability are **different dimensions**. A library can be feature-complete without being production-ready, and an actively developed project may already be useful.

| Status | Meaning |
| --- | --- |
| **Planned** | Concept or repository exists; no substantial implementation yet. |
| **Raw** | Early prototype or incomplete building blocks; not a dependable end-to-end implementation. |
| **In progress** | Meaningful implementation exists, but key parts, integration or hardening are unfinished. |
| **Experimentally usable** | Working functionality is available for experiments or controlled personal use; limitations remain. |
| **Feature-complete (scope)** | The current *explicitly limited* intended feature set is implemented; does not imply reliability or a stable API. |
| **Production-ready** | Defined supported scope, sufficient validation, reliable error handling, maintained compatibility and reproducible builds for the intended deployment. Requires positive evidence; not inferred from a working README or passing unit tests. |
| **On hold / archived** | Maintenance/activity qualifier, **not** a maturity ranking. It may apply alongside any implementation status. |

When a status is uncertain, the entry deliberately makes a conservative assessment. **Nothing is labeled production-ready without a documented production-readiness assessment.** Statuses are an editorial snapshot, not a claim that CI/builds were run for this page.

## Reusable Rust libraries

| Library | Current status | Notes / remaining limitations |
| --- | --- | --- |
| [threadpool](https://github.com/witold-k/threadpool) | **Feature-complete (scope)** | Small fixed worker pool with tests/CI; documented panic, blocking and unbounded-queue limitations. Not independently certified production-ready. |
| [fsscanner](https://github.com/witold-k/fsscanner) | **Feature-complete (scope)** | Recursive scanning, symlink protection, filtering and sequential/parallel processing implemented; existing users. Production-readiness not separately verified. |
| [struct_extractors](https://github.com/witold-k/struct_extractors) | **Feature-complete (scope)** | Focused procedural macro families implemented and tested; public API stability beyond this scope not independently checked. |
| [simplelexer](https://github.com/witold-k/simplelexer) | **Experimentally usable** | Quote and variable lexers, editable chunks implemented; README describes an API-hardening pass. |
| [lineariterator](https://github.com/witold-k/lineariterator) | **Experimentally usable** | Strided/pointer and safe window interfaces implemented; explicitly experimental, with unsafe caller invariants for raw APIs. |
| [simplefield](https://github.com/witold-k/simplefield) | **Feature-complete (scope)** | Row-/column-major contiguous 2D storage and views implemented; not a general tensor library. |
| [unitscale](https://github.com/witold-k/unitscale) | **Feature-complete (scope)** | Compile-time decimal scaling and angle wrappers implemented; intentionally does not model physical dimensions. |
| [token_db](https://github.com/witold-k/token_db) | **Feature-complete (scope)** | Stable token IDs, counts, merges and versioned binary storage implemented; ordered document streams intentionally out of scope. |
| [retrieval](https://github.com/witold-k/retrieval) | **Experimentally usable (matrix construction)** | Renamed from `corpus_matrix`. Count and PPMI co-occurrence matrix builders are documented and implemented; broader retrieval/search functionality remains experimental and is not declared complete. No SVD dependency for search. |
| [svd_wrapper](https://github.com/witold-k/svd_wrapper) | **In progress** | Standalone numerical library, not part of the current retrieval/search pipeline; CPU, CUDA and Julia dense-SVD backends implemented/tested; consistency and consolidation ongoing. OpenCL planned, ROCm placeholder. Explicitly not production-ready. |
| [ngram_token_lemma_tokenizer](https://github.com/witold-k/ngram_token_lemma_tokenizer) | **Raw** | Initial project skeleton exists (currently only a minimal `main.rs` under `src`); token/lemma n-gram transformation remains to be implemented. |
| [symbol_to_source_resolver](https://github.com/witold-k/symbol_to_source_resolver) | **Experimentally usable · in progress** | Initial Cargo/Rust, Maven/Java, and `compile_commands.json` C/C++ source lookups implemented. Rust uses `syn` with Tree-sitter fallback; Java and C/C++ use tolerant Tree-sitter parsing. Project-local C/C++ headers are scanned. User-reported build and 11 integration tests passed before the final Clippy-only correction; real-world accuracy, external headers, macros and compiler-level resolution remain unverified. Future `aiagents` integration is planned. |
| [logicequation](https://github.com/witold-k/logicequation) | **Experimentally usable · on hold** | Symbolic Boolean/bit-vector graph library; experimental, with original use case inactive. |
| [sequencetransform](https://github.com/witold-k/sequencetransform) | **Experimentally usable · archived** | Implements sequence transforms and ordinal-pattern ideas; original project stopped, archive rather than actively maintained library. |

## Applications, pipelines and build/development tools

| Project | Current status | Notes / remaining limitations |
| --- | --- | --- |
| [aiagents](https://github.com/witold-k/aiagents) | **Experimentally usable · in progress** | Working local agent runtime and workflows including Git-based release documentation. Standalone `aifix_runtest` executes eight Cargo/CMake/Meson/Maven repair fixtures; a recorded local Devstral run passed 8/8 in 227.05 s total fix/build time. Results and per-fixture logs are generated; [documented results](https://github.com/witold-k/aiagents/blob/master/runtests/README.md). Semantic API invariants, repeatability across runs, broader test coverage, and model comparisons are not yet established. |
| [pdf_to_text_wrapper](https://github.com/witold-k/pdf_to_text_wrapper) | **Experimentally usable** | PDF preprocessing orchestrator using MinerU/GROBID and specialized parsing/token components; relies on installed external backends. |
| [lemmatizer_wrapper](https://github.com/witold-k/lemmatizer_wrapper) | **Experimentally usable · in progress** | spaCy-backed per-file token/lemma output works; README still describes directory processing and integration as incomplete. |
| [cide](https://github.com/witold-k/cide) | **Experimentally usable · in progress** | **NOT USABE OUTSIDE MY ENV** Author-environment build infrastructure; fresh checkout cannot reproduce build without externally managed crosstool-ng. |
| [buildsystems](https://github.com/witold-k/buildsystems) | **In progress** | **NOT USABE OUTSIDE MY ENV** Build-system collection for CIDE; same external toolchain/non-portability constraint. |
| [buildscripts](https://github.com/witold-k/buildscripts) | **Experimentally usable · maintenance needed** | **NOT USABE OUTSIDE MY ENV** Used for existing personal build workflows; cleanup and some bug fixes remain. |
| [neovim-tmux-integration](https://github.com/witold-k/neovim-tmux-integration) | **Experimentally usable** | Daily personal editor/tmux configuration; environment-specific rather than general distribution. |


## Maintenance guidelines

- Update a project's row when functionality or intended scope meaningfully changes, preferably in the same PR.
- Prefer each project's README and actual tests/CI as evidence; do not confuse an intended design with an implemented feature.
- Mark projects **feature-complete** only relative to their stated scope, and **production-ready** only after reviewing supported environments, tests, failure modes, CI/reproducibility and maintenance expectations.
- Keep activity qualifiers (in progress, on hold, archived) separate from technical maturity where possible.
- If evidence is missing or contradictory, write the uncertainty in the Notes column rather than guessing.
