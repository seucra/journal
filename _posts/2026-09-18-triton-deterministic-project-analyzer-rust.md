---
title: "Triton: Building a Deterministic Project Analyzer and Artifact Engine in Rust"
date: 2026-09-18 10:00:00 +0530
categories: [Systems Programming, Rust]
tags: [rust, systems, cli, sqlite, rayon, blake3, performance]
author: Shams Tabrez Ahmed
description: Designing a multi-crate Rust workspace to deterministically detect project roots, establish nearest-ancestor file membership, and classify build artifacts without heuristic guesswork.
math: true
mermaid: true
---

> **Project & Source Reference:**  
> • **Engineering Hub:** [seucra.tech/projects/triton ↗](https://seucra.tech/projects/triton/)  
> • **Local Workspace:** `product/Triton` (Rust 2021 Edition)  
> • **Related Skills:** [Systems Programming ↗](https://seucra.tech/skills/systems-programming/) · [Rust ↗](https://seucra.tech/skills/rust/)
{: .prompt-tip }

When traversing large developer filesystems with hundreds of repositories, nested workspaces, and gigabytes of build artifacts (`target/`, `node_modules/`, `__pycache__/`, `.cache/`), tools often struggle with ambiguity:
- Which repository root owns a nested configuration file?
- Which directories are transient compiled artifacts versus source code?
- How do we derive relationships and deduplication states reliably without re-scanning or corrupting data?

**Triton** is a multi-crate Rust systems project I built to explore deterministic filesystem intelligence, concurrent directory traversal, and persistent artifact indexing.

---

## 1. Multi-Crate Architecture

Triton is organized as a modular Rust 2021 workspace divided across distinct responsibility layers:

```
product/Triton/
├── Cargo.toml                  # Workspace manifest
├── crates/
│   ├── triton-core/            # Core domain types, policies, scan options
│   ├── triton-engine/          # SQLite persistence, Rayon traversal, project analysis
│   └── triton-cli/             # CLI commands, argument parsing (clap), reporting
```

### The Invariant: Separation of Concerns
1. **`triton-core`**: Holds pure domain types (`ScanPolicy`, `FsObject`, `ProjectRoot`, `SimilarityDomain`) without heavy I/O dependencies.
2. **`triton-engine`**: Implements SQLite-backed storage (`rusqlite`), multi-threaded hashing (`blake3`, `xxhash`), parallel traversal (`rayon`), and deterministic project detection.
3. **`triton-cli`**: Exposes structured commands (`scan`, `delta`, `json`) with sub-second execution.

---

## 2. Marker-Based Deterministic Project Detection

Rather than parsing diverse configuration syntaxes (which introduces parser failures and performance penalties), project detection in Triton is **strictly marker-based with deterministic priority resolution**:

| Priority | Marker File | Inferred Project Type |
| :--- | :--- | :--- |
| **1** | `Cargo.toml` | `Rust/Cargo` |
| **2** | `package.json` | `Node.js/JavaScript/TypeScript` |
| **3** | `pyproject.toml` | `Python` |
| **4** | `requirements.txt` | `Python` |
| **5** | `go.mod` | `Go` |
| **6** | `pom.xml` | `Maven/Java` |
| **7** | `build.gradle.kts` | `Gradle` |
| **8** | `build.gradle` | `Gradle` |
| **9** | `CMakeLists.txt` | `C/C++` |
| **10** | `Makefile` | `Make` |
| **11** | `.git` | `Git project root` |

---

## 3. Nearest-Ancestor-Root Semantics for Nested Workspaces

A common challenge in monorepos is nested hierarchies (e.g. a Rust backend root containing a `frontend/package.json` sub-project). 

Triton solves this deterministically through **Nearest-Ancestor Prefix Matching**:

```text
/workspace/Cargo.toml                  ──► Registered as Root A (Rust/Cargo)
/workspace/frontend/package.json       ──► Registered as Root B (Node.js)

/workspace/src/main.rs                 ──► Belongs to Root A
/workspace/frontend/src/App.tsx        ──► Belongs strictly to Root B
```

### Membership Rules:
- **Nearest Ancestor Wins**: A path $P$ belongs exclusively to project root $R$ where $P.\text{starts\_with}(R)$ has the maximum path component count.
- **Zero Ambiguity**: A file is assigned to at most one project. `App.tsx` belongs to `frontend`, never to the outer workspace.
- **Outside Paths**: Files outside any detected project boundary are indexed in general filesystem records but omitted from `project_members`.

---

## 4. Artifact & Member Role Classification

Once project boundaries are established, files receive deterministic role classifications:
- **`Source`**: Code files (`.rs`, `.py`, `.ts`, `.c`, `.go`).
- **`Configuration`**: Manifests (`Cargo.toml`, `package.json`, `.env`).
- **`Documentation`**: Markdown files, licenses, change logs.
- **`Generated`**: Build outputs (`target/`, `dist/`, `build/`, `__pycache__/`, binaries).
- **`Dependency`**: Vendored packages (`node_modules/`, `vendor/`, `Pods/`).

### Evidence-Backed Classification
Generated artifact classifications store explicit matching criteria (e.g. `cargo_target_dir`, `node_modules_dir`, `compiled_binary_extension`), ensuring that downstream cleanup tools act on verifiable evidence rather than guesswork.

---

## 5. Performance & Persistence

- **BLAKE3 & SQLite Indexing**: File observations are hashed with BLAKE3 and indexed into SQLite with WAL mode (`Write-Ahead Logging`), enabling millisecond diffing across repeated scans.
- **Rayon Parallelism**: Work-stealing thread pools accelerate multi-disk directory walks while honoring `.gitignore` rules.

---

## Summary & What I Learned

Building Triton solidified several systems concepts:
1. **Designing for Invariants**: Establishing strict longest-prefix membership rules eliminated edge cases with nested monorepos.
2. **Multi-Crate Discipline**: Isolating core domain types from SQLite and CLI dependencies made testing fast and decoupled.
3. **Rust Concurrency**: Combining Rayon parallel iterators with thread-safe SQLite transaction handling provided significant throughput improvements over single-threaded traversal.
