---
title: Publishing My First Rust npm Package
date: 2026-08-18 10:00:00 +0530
categories: [Rust, WebAssembly]
tags: [rust, npm, wasm, webassembly, publishing]
author: Shams Tabrez Ahmed
description: Step-by-step experience publishing @seucra/matrix-sdk-bridge from Rust to WebAssembly and onto the npm registry.
math: true
mermaid: true
---

## Introduction

Publishing a package to npm is a common milestone for JavaScript developers. However, publishing an npm package **written in Rust and compiled to WebAssembly** introduces a completely different set of tooling, cross-compilation target considerations, and TypeScript definition generation.

Recently, I published [`@seucra/matrix-sdk-bridge`](https://www.npmjs.com/package/@seucra/matrix-sdk-bridge)—the extracted WebAssembly wrapper around `matrix-sdk` used by Vigilant. 

This post documents the exact workflow, build scripts, pitfalls, and lessons learned during the publishing process.

---

## Tooling Stack

To build and package Rust code for WebAssembly, I used:
- **`wasm-pack`**: The official CLI tool for building and packaging Rust-generated WebAssembly for npm.
- **`wasm-bindgen`**: The core library facilitating high-level interactions between Rust and JavaScript.
- **TypeScript**: Auto-generated `.d.ts` definition files ensuring strong typing for JS consumers.

```
┌──────────────┐     wasm-pack build     ┌────────────────────────┐
│  Rust Source │ ───────────────────────►│  WebAssembly Binary    │
│  (src/lib.rs)│   --target web          │  (.wasm + .js + .d.ts) │
└──────────────┘                         └───────────┬────────────┘
                                                     │
                                                     │ npm publish
                                                     ▼
                                         ┌────────────────────────┐
                                         │ npm Registry Package   │
                                         │ @seucra/matrix-sdk-b.. │
                                         └────────────────────────┘
```

---

## Step-by-Step Publishing Workflow

### 1. Configuring `Cargo.toml`
The Rust crate must be configured as a dynamic system library (`cdylib`):

```toml
[package]
name = "matrix-sdk-bridge"
version = "0.1.0"
edition = "2021"

[lib]
crate-type = ["cdylib", "rlib"]

[dependencies]
wasm-bindgen = "0.2"
wasm-bindgen-futures = "0.4"
matrix-sdk = { version = "0.7", default-features = false, features = ["e2e-encryption"] }
serde = { version = "1.0", features = ["derive"] }
serde-wasm-bindgen = "0.6"
```

### 2. Building for Web Targets
Building the compiled WASM artifacts for browser environments:

```bash
wasm-pack build --target web --scope seucra
```

This generates a `pkg/` folder containing:
- `matrix_sdk_bridge_bg.wasm`: The compiled WebAssembly bytecode.
- `matrix_sdk_bridge.js`: The JS glue code initializing and exposing WASM functions.
- `matrix_sdk_bridge.d.ts`: TypeScript typings for all `#[wasm_bindgen]` functions.
- `package.json`: Configured package manifest under scope `@seucra`.

### 3. Publishing to npm
After logging in with `npm login`, publishing the scoped package:

```bash
cd pkg
npm publish --access public
```

---

## Lessons & Gotchas

1. **Target Selection (`--target web` vs `--target bundler`)**:
   `--target web` generates ES module outputs using native `fetch()` to load `.wasm` files. This avoids complex Webpack/Vite loader configurations for consumers.
2. **TypeScript Generation is Automatic**:
   `wasm-bindgen` automatically generates TypeScript `.d.ts` files for annotated Rust structs and functions. Ensuring Rust doc comments are clean means your published npm package gets instant IntelliSense documentation!
3. **Automate Builds with Scripts**:
   Always script your build and test flow to prevent publishing stale WASM binaries.

---

## Conclusion

Publishing `@seucra/matrix-sdk-bridge` was a major milestone in my Rust learning journey. It proved that Rust and WebAssembly can bridge the gap between low-level systems programming and modern browser applications seamlessly.
