# README Requirements

This document records the structural and presentation requirements for the profile `README.md`.

## Purpose

The profile README should present the repositories as a set of understandable project areas rather than as a flat repository list.

It should be representative of the actual projects and their relationships. Repository relationships must be derived from what the projects really do; thematic similarity must not be presented as a technical dependency.

## Project areas

### AI-assisted software engineering

The `aiagents` / `aifix` line is its own project area.

It must be presented separately from the local document-processing/search work. Shared libraries may appear in both dependency diagrams where appropriate, but that does not make the two project areas one system.

### Local document processing and search

The document-processing/search line is a separate project area around `pdf_to_text`, corpus preparation, `token_db`, search experiments, and numerical SVD/LSA experiments.

The intended future data path includes a classical **LSA (Latent Semantic Analysis)** stage based on **SVD (Singular Value Decomposition)**. `svdwrapper` is intended to provide the SVD functionality for this part of the pipeline.

The README should therefore show LSA/SVD as part of the planned direction of the document-search project while clearly marking it as planned until it exists in the implementation.

Do not merge this conceptually with `aiagents` / `aifix` merely because both may use some of the same supporting libraries.

### Build infrastructure

The actual relationships between the build repositories must be represented correctly:

- `CIDE` uses `buildsystems` for building.
- `CIDE` uses tools from `buildscripts`.
- `buildsystems` also uses tools from `buildscripts`.
- CIDE builds many software packages, including Neovim and tmux.

Avoid vague categories such as "software stacks" when they obscure these concrete relationships.

### Development environment

`neovim-tmux-integration` is its own project and must not be presented as part of the CIDE/build repository dependency structure.

There is a practical relationship: CIDE can build Neovim and tmux, while `neovim-tmux-integration` configures and combines Neovim and tmux into the personal development environment.

This practical relationship is not a repository dependency.

## Diagrams

### Separate flows from dependencies

Do not mix signal/data/workflow flow and repository dependencies in one diagram.

Use separate diagrams for:

1. **data, signal, or workflow flow** — what information or processing moves from one stage to the next;
2. **repository/component dependencies** — which project uses which other project or library.

A reader should never have to infer whether an arrow means "data flows to", "depends on", "uses", or "builds".

### Dependency arrows

Dependency relationships must be explicit in the arrow label.

The preferred convention is:

```text
consumer -->|uses| dependency
```

For example:

```text
pdf_to_text -->|uses| fsscanner
simplefield -->|uses| lineariterator
```

Use more specific labels where useful, for example:

```text
CIDE -->|uses tools from| buildscripts
CIDE -->|uses build systems from| buildsystems
CIDE -->|builds| Neovim
```

The direction alone must never be relied upon to communicate the meaning.

### Flow diagrams

Flow diagrams should describe the actual processing direction and should use stage names rather than library dependencies where possible.

For the document-processing/search project, the intended conceptual flow is along the lines of:

```text
PDF documents
    -> PDF conversion
    -> normalized text
    -> tokenization / corpus data
    -> term-document representation
    -> LSA / SVD projection          [planned]
    -> latent-space representation   [planned]
    -> search / retrieval             [planned]
```

The SVD/LSA stage is not merely an unrelated numerical experiment: it is intended to become part of the document-search data path. Until implemented, the README must visually and textually distinguish these stages as **planned**.

Repository dependencies such as `pdf_to_text` using `fsscanner`, `simplelexer`, or `token_db`, and the planned search implementation using `svdwrapper`, belong in a separate dependency diagram.

### Mermaid

Mermaid diagrams are encouraged when they make relationships easier to understand, but clarity is more important than having a diagram.

Diagrams should remain small enough to understand at a glance and should not combine unrelated project areas merely to create one global architecture picture.

## Accuracy

Before adding or changing a relationship in the profile README, inspect the relevant repository and its README/source as necessary.

Distinguish between:

- current dependencies;
- planned/future dependencies;
- conceptual relationships;
- data/workflow flow;
- software that a project builds;
- independent projects that happen to use the same software.

Planned relationships must be labelled as planned/future rather than shown as existing dependencies.

## Presentation

The README should help a visitor quickly understand:

- what the main project areas are;
- which repositories belong to each area;
- what each repository is for;
- how repositories technically depend on one another;
- how information or work flows through a project where that is useful;
- which projects are independent despite practical or thematic connections.

Prefer concrete descriptions over broad labels. Keep the profile readable and representative rather than exhaustive.
