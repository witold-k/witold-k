# Witold Kaminski

I build software systems and small, focused tools, mostly in Rust. My projects range from local AI experiments and document processing to build infrastructure, developer tooling, and low-level reusable libraries.

A recurring theme is to keep systems **small, explicit, inspectable, and locally controllable**: simple interfaces, limited dependencies, clear boundaries, and components that can be understood independently.

## Current projects

Two current lines of experimentation are deliberately separate: AI-assisted software engineering and local document/corpus processing. They are separate projects today, although the document-search capabilities are intended to become usable by `aiagents` later.

### AI-assisted software engineering

**[aiagents](https://github.com/witold-k/aiagents)** is an experimental local-first agentic runtime. `aifix` is one application/workflow built on it for AI-assisted software engineering: analysis, build/test/fix cycles, code and documentation generation, reviews, and release documentation.

#### Workflow

```mermaid
flowchart LR
    SRC[Source repository] --> ANALYZE[Analyze]
    ANALYZE --> CHANGE[Generate / modify]
    CHANGE --> VERIFY[Build / test / review]
    VERIFY --> RESULT[Result]
    VERIFY -->|problems found| ANALYZE
```

#### Repository dependencies

```mermaid
flowchart LR
    A[aiagents / aifix] -->|uses| FS[fsscanner]
    A -->|uses| SE[struct_extractors]
    A -.->|planned use| T[token_db]
    A -->|can use local LLM runtime| LL[llama.cpp]
    C[CIDE] -->|builds| LL
```

### Local document processing and search

This is a separate line of work around turning local documents into a searchable corpus and experimenting with classical numerical search methods, in particular latent representations based on SVD/LSA-style techniques.

- **[pdf_to_text](https://github.com/witold-k/pdf_to_text)** — corpus-preparation pipeline that turns PDFs into structured text and token data.
- **[token_db](https://github.com/witold-k/token_db)** — compact Rust token database with stable numeric IDs, frequency tracking, merging, and binary persistence.
- **[corpus_matrix](https://github.com/witold-k/corpus_matrix)** — constructs corpus-derived matrix representations for the document-search experiments.
- **[svdwrapper](https://github.com/witold-k/svdwrapper)** — experimental backend-independent dense SVD interface with CPU/LAPACK, CUDA/cuSOLVER, and Julia implementations. A planned use is dimensional reduction of matrix representations derived from the document corpus.

#### Data flow

```mermaid
flowchart TB
    PDF[PDF documents]

    subgraph PREP[Corpus preparation]
        direction LR
        CONVERT[convert / normalize] --> TOKENS[tokenize]
    end

    subgraph SEARCH[Search representation - planned]
        direction LR
        MATRIX[corpus_matrix] --> LSA[SVD / latent projection] --> LATENT[latent representation]
    end

    PDF --> CONVERT
    TOKENS --> MATRIX
    LATENT --> RETRIEVE[search / retrieval]
    RETRIEVE -.->|future capability| AGENT[aiagents]
```

`corpus_matrix` is the matrix-construction stage of this pipeline. The representation is intentionally open to experimentation: it may be a conventional term-document representation, but it may also encode word co-occurrence, for example by counting words that occur together within a sliding window. The planned SVD stage is intended to explore useful lower-dimensional representations rather than commit the project to one particular matrix construction.

Corpus preparation and matrix construction now have dedicated repositories. SVD/latent representation, retrieval, and integration with `aiagents` remain planned stages.

#### Repository dependencies

```mermaid
flowchart LR
    P[pdf_to_text] -->|uses| FS[fsscanner]
    P -->|uses| LX[simplelexer]
    P -->|uses| T[token_db]
    CM[corpus_matrix] -->|uses corpus data from| T
    SEARCH[document search] -->|matrix construction| CM
    SEARCH -.->|planned use| SVD[svdwrapper]
    A[aiagents] -.->|planned use of| SEARCH
```

## Build infrastructure

CIDE and its supporting repositories form a small build-infrastructure family. This diagram shows **repository/tool relationships** and the broad software groups represented by CIDE's module definitions, rather than singling out individual packages such as Neovim or tmux.

```mermaid
flowchart LR
    C[CIDE] -->|uses tools from| S[buildscripts]
    C -->|uses build systems from| B[buildsystems]
    B -->|uses tools from| S

    C -->|builds software from| G["module groups<br/>base · IDE · compression · crypto<br/>audio · graphics/images · documents<br/>input · interpreters · math · networking<br/>media · LLM / diffusion · ..."]
    C -->|builds| LL[llama.cpp]
    LL -.->|local LLM runtime for| A[aiagents]

    style C stroke-width:3px
```

- **[cide](https://github.com/witold-k/cide)** — modular build environment for building software independently of the host system. It uses the build systems supplied by `buildsystems` and utilities from `buildscripts`. Its module definitions cover groups such as base software, IDE/tools, compression, cryptography, audio, graphics/images, documents, input, interpreters, mathematics, networking, media, and increasingly AI-related software such as LLM and diffusion components. In particular, CIDE builds `llama.cpp`, providing a locally controlled LLM runtime that is relevant to `aiagents`.
- **[buildsystems](https://github.com/witold-k/buildsystems)** — provides build systems and related tooling needed to build CIDE; it in turn uses utilities from `buildscripts`.
- **[buildscripts](https://github.com/witold-k/buildscripts)** — shared utilities used by both CIDE and `buildsystems`, alongside other personal helpers for build workflows, version control, containers, and related tasks.

## Development environment

**[neovim-tmux-integration](https://github.com/witold-k/neovim-tmux-integration)** is a separate project: my personal day-to-day development environment built around a persistent Neovim server inside tmux, with LSP, debugging, Git integration, build-output handling, and language-specific tooling.

There is a practical connection to CIDE, but not a repository dependency: Neovim and tmux are two packages in CIDE's broader `ide` module group, while `neovim-tmux-integration` configures and combines those tools into the environment I actually work in.

## Rust building blocks

Several repositories are deliberately small libraries. Some started because I needed a particular primitive elsewhere; others are experiments in API design, ownership, compile-time modelling, or low-level iteration.

| Project | Purpose |
| --- | --- |
| **[fsscanner](https://github.com/witold-k/fsscanner)** | Directory-tree scanning and sequential/parallel file-processing pipelines |
| **[struct_extractors](https://github.com/witold-k/struct_extractors)** | Procedural macros for accessors, comparators, hashing wrappers, numeric behavior, and enum checks |
| **[simplelexer](https://github.com/witold-k/simplelexer)** | Small zero-copy lexers for assignment-oriented text and `${...}` expressions |
| **[lineariterator](https://github.com/witold-k/lineariterator)** | Strided and fixed-window iteration, including low-level pointer APIs and safe wrappers |
| **[simplefield](https://github.com/witold-k/simplefield)** | Compact 2D contiguous storage with compile-time row-major/column-major layout |
| **[unitscale](https://github.com/witold-k/unitscale)** | Zero-overhead numeric wrappers for compile-time SI decimal scaling and angle representation |

### Library dependencies

Every arrow below explicitly reads as **"uses"**.

```mermaid
flowchart LR
    SF[simplefield] -->|uses| LI[lineariterator]
    PDF[pdf_to_text] -->|uses| FS[fsscanner]
    PDF -->|uses| LX[simplelexer]
    PDF -->|uses| T[token_db]
    CM[corpus_matrix] -->|uses corpus data from| T
    A[aiagents] -->|uses| FS
    A -->|uses| SE[struct_extractors]
```

## What ties these projects together?

They are not intended to form one monolithic framework. The common thread is the way I like to explore software:

- build a real component to understand a problem;
- keep abstractions narrow and boundaries explicit;
- prefer inspectable mechanisms over unnecessary complexity;
- use Rust where ownership, performance, or strong compile-time modelling are useful;
- experiment across layers — from low-level iteration and numerical code to build infrastructure and AI workflows.

Most repositories are personal or experimental projects and are at different levels of maturity. Their individual READMEs describe the current status and limitations.

## Background

For a more conventional overview of my professional background, see **[my CV repository](https://github.com/witold-k/cv)**.