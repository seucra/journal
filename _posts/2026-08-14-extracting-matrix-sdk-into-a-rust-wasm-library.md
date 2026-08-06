---
title: Extracting Matrix SDK into a Rust WASM Library
date: 2026-08-14 10:00:00 +0530
categories: [Rust, WebAssembly]
tags: [rust, wasm, matrix, architecture, libraries]
author: Shams Tabrez Ahmed
description: Why and how I extracted Vigilant's Matrix integration code into a reusable Rust -> WebAssembly library.
math: true
mermaid: true
---

## The Monolith Problem in Early Prototypes

When building an ambitious application, it is tempting to put all code into a single repository. In early iterations of **Vigilant**, the Rust Matrix SDK integration was tightly coupled with application-specific state handlers and UI glue code. 

As the project grew, this coupling created several friction points:
- Testing the Matrix SDK integration independently from application UI state was difficult.
- Changes to the frontend interface required touching deep backend Matrix sync loops.
- Reusing the Matrix integration in another web client or tool was impossible without copy-pasting code.

I realized that Matrix client integration is fundamentally an independent concern. That realization led to extracting the Rust integration code into a standalone, reusable library: **`matrix-sdk-bridge`**.

---

## Why WebAssembly for Matrix Client Logic?

The Matrix client ecosystem has robust native implementations, with [`matrix-sdk`](https://github.com/matrix-org/matrix-rust-sdk) in Rust being one of the most active and feature-complete SDKs available.

By compiling Rust to WebAssembly target `wasm32-unknown-unknown` via `wasm-bindgen`, we get several advantages:

1. **Memory Safety & Correctness**: Rust's type system ensures data race safety, strict lifetime checks, and zero-cost abstractions inside the browser environment.
2. **Upstream Alignment**: We leverage official, audited Matrix Rust SDK crates directly rather than reimplementing protocol handling in JavaScript.
3. **Clean Architectural Boundary**: WebAssembly exposes a clear ABI (Application Binary Interface). JavaScript handles UI rendering while WebAssembly handles protocol state, cryptographic sessions, and timeline synchronization.

---

## Designing the WASM Bridge Interface

Bridging Rust async types (`Future`, `Stream`, `tokio`/`wasm-bindgen-futures`) to JavaScript promises requires careful API design.

Here is a simplified architectural view of how data flows across the WASM boundary:

```
┌───────────────────────────────────────┐
│     JavaScript Application / UI       │
└───────────────────┬───────────────────┘
                    │  call async WASM methods (e.g. client.login())
                    ▼
┌───────────────────────────────────────┐
│     wasm-bindgen Export Layer         │
│  - JsValue serialization              │
│  - Future -> Promise conversion       │
└───────────────────┬───────────────────┘
                    │  Rust async calls
                    ▼
┌───────────────────────────────────────┐
│       matrix-sdk (Rust Crates)        │
│  - Room handling                      │
│  - Sync loops                         │
│  - Session state                      │
└───────────────────────────────────────┘
```

### Key Design Principles:

1. **Hide Upstream Complexity**: Upstream `matrix-sdk` exposes dozens of types (`Client`, `Room`, `Timeline`, `SyncResponse`). The bridge packages these into high-level, serializable JS structures.
2. **Explicit Event Passing**: Matrix live sync updates are passed across the WASM boundary using JS callbacks or event dispatchers.
3. **Graceful Error Handling**: Rust `Result<T, E>` types are converted to structured JavaScript errors rather than panicking inside the WebAssembly module.

---

## Lessons Learned

- **Decoupling Accelerates Iteration**: Once `matrix-sdk-bridge` was extracted, developing the frontend became significantly cleaner. The frontend team can mock or consume WASM methods without needing to touch Rust code.
- **WASM Memory Management Matters**: Passing large byte arrays or string structures continuously across the JS/WASM boundary can introduce garbage collection overhead. Using lightweight JSON or typed array structures minimizes serialization overhead.
- **Treat Internal Tooling as Open Source**: Even if a library starts as part of an internal project, structuring it like an open-source package forces cleaner abstractions, better documentation, and higher code quality.

In the next post, I will share the process of packaging this Rust WebAssembly bridge and publishing it to npm as `@seucra/matrix-sdk-bridge`.
