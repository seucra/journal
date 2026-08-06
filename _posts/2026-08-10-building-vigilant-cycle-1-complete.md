---
title: Building Vigilant — Cycle 1 Complete
date: 2026-08-10 10:00:00 +0530
categories: [Projects, Matrix]
tags: [rust, matrix, backend, architecture, vigilant]
author: Shams Tabrez Ahmed
description: Reflections on finishing Cycle 1 of Vigilant's backend, adopting Matrix Synapse, and choosing architectural focus over protocol reinvention.
math: true
mermaid: true
---

## Introduction

Building a modern secure communication application is one of the most effective ways to understand backend systems. However, it is also a project where it is easy to get lost in the wrong problem.

When I first conceived **CipherLink**—the project that eventually evolved into **Vigilant**—my goal was simple: write a complete messaging application from scratch, including the protocol, authentication, server storage, and encryption. 

It did not take long to realize that building a custom protocol from scratch is a massive undertaking. Designing key management, device synchronization, offline message delivery, and federation requires years of protocol engineering and security auditing. Attempting to build all of that alone meant spending 90% of my time reinventing established cryptography and messaging infrastructure, leaving almost no time for application architecture, backend robustness, or client integration.

Near the end of B.Tech Semester 4, I made a major architectural pivot: **adopt the open Matrix protocol, build on top of Matrix Synapse, and implement the application logic in Rust compiled to WebAssembly.**

With **Cycle 1 of Vigilant’s backend now complete**, this post reflects on what was accomplished, why this architecture was chosen, and what I learned along the way.

---

## What is Vigilant?

Vigilant is a secure communication platform built on top of the Matrix ecosystem. 

Rather than treating Matrix as just a third-party API, Vigilant uses a self-hosted Matrix stack (Synapse, PostgreSQL, MinIO, Nginx) as its underlying persistence and synchronization engine, while relying on a custom Rust backend and WebAssembly bridge for client state, authentication, room management, and timeline handling.

```
┌────────────────────────────────────────────────────────┐
│                   Vigilant Frontend                    │
└───────────────────────────┬────────────────────────────┘
                            │ (TypeScript / JS)
                            ▼
┌────────────────────────────────────────────────────────┐
│            @seucra/matrix-sdk-bridge (WASM)            │
└───────────────────────────┬────────────────────────────┘
                            │ (Rust matrix-sdk)
                            ▼
┌────────────────────────────────────────────────────────┐
│                 Matrix Synapse Engine                  │
│       ┌──────────────┐          ┌──────────────┐       │
│       │  PostgreSQL  │          │    MinIO     │       │
│       └──────────────┘          └──────────────┘       │
└────────────────────────────────────────────────────────┘
```

---

## Cycle 1 Accomplishments

During Cycle 1, the primary objective was establishing a rock-solid backend foundation that could reliably interact with Matrix Synapse. 

Key backend milestones completed in Cycle 1:

1. **Authentication & Session Persistence**: Implementing login, registration, homeserver discovery, and secure token storage across page reloads.
2. **Room Management & Discovery**: Joining public rooms, creating direct message channels, and mapping Matrix room states to application views.
3. **Timeline Loading & Message History**: Fetching room timelines, listening to live sync events, and handling message pagination tokens.
4. **WASM Bridge Extraction**: Separating the Rust integration logic into a standalone library published as `@seucra/matrix-sdk-bridge` on npm.

---

## Key Lessons & Architectural Trade-offs

### 1. Build on Proven Standards
Choosing Matrix over a custom protocol was the single best decision in this project. It shifted my effort from *reinventing the wheel* to *learning how real distributed communication engines work*. Matrix Synapse handles event graph ordering, federation, and persistence, allowing me to focus on Rust async state management and client integration.

### 2. Rust for Client Integration
Writing the core logic in Rust using `matrix-sdk` provided strong memory safety guarantees and compile-time correctness. Upstream Matrix SDK updates can be integrated directly, and compiling to WebAssembly (`wasm32-unknown-unknown`) ensures high performance in the browser.

### 3. Read Upstream Source Code
Documentation for emerging SDKs is often sparse. I frequently had to dive directly into the Rust `matrix-sdk` source code to understand event handlers, sync loops, and sliding sync behaviors. Reading primary source code is a muscle every backend engineer must develop.

---

## What’s Next for Cycle 2

Now that Cycle 1 backend milestones are solid, the focus shifts toward:
- Refining frontend integration with my project collaborator.
- Hardening timeline pagination and offline event caching.
- Expanding documentation and integration tests for `@seucra/matrix-sdk-bridge`.

*Building Vigilant has proven that engineering depth comes from understanding system boundaries, making deliberate trade-offs, and shipping defensible work.*
