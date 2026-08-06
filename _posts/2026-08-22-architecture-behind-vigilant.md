---
title: The System Architecture Behind Vigilant
date: 2026-08-22 10:00:00 +0530
categories: [Architecture, Systems]
tags: [vigilant, architecture, rust, backend, matrix, infrastructure]
author: Shams Tabrez Ahmed
description: Deep dive into the architecture, component boundaries, state management, and self-hosted infrastructure powering Vigilant.
math: true
mermaid: true
---

## Introduction

System architecture is the art of deciding **where boundaries live**. In early software projects, boundaries are often blurry—database queries bleed into UI components, and state management gets scattered across event handlers.

In **Vigilant**, designing clear component boundaries was the central engineering challenge. 

This post details the full architectural breakdown of Vigilant: from the self-hosted Matrix Synapse infrastructure and PostgreSQL database to the Rust WASM client bridge and frontend integration.

---

## High-Level System Topology

Vigilant operates across three distinct layers:
1. **Infrastructure Layer** (Self-Hosted Services)
2. **Integration Layer** (Rust / WASM Bridge)
3. **Application & UI Layer** (Client Frontend)

```
┌────────────────────────────────────────────────────────────────────────┐
│                        INFRASTRUCTURE LAYER                            │
│                                                                        │
│   ┌───────────────┐     ┌────────────────┐     ┌──────────────────┐    │
│   │ Nginx Proxy   │ ──► │ Matrix Synapse │ ──► │ PostgreSQL DB    │    │
│   │ (SSL / HTTPS) │     │ (Homeserver)   │     │ (User/Room State)│    │
│   └───────────────┘     └───────┬────────┘     └──────────────────┘    │
│                                 │                                      │
│                                 ▼                                      │
│                         ┌───────────────┐                              │
│                         │ MinIO Storage │                              │
│                         │ (Media / Docs)│                              │
│                         └───────────────┘                              │
└─────────────────────────────────┬──────────────────────────────────────┘
                                  │ HTTPS / Matrix Client-Server API
                                  ▼
┌────────────────────────────────────────────────────────────────────────┐
│                         INTEGRATION LAYER                              │
│                                                                        │
│   ┌────────────────────────────────────────────────────────────────┐   │
│   │               @seucra/matrix-sdk-bridge (WASM)                 │   │
│   │   - Rust Async Runtime (tokio / wasm-bindgen-futures)          │   │
│   │   - matrix-sdk Client & Session Manager                        │   │
│   │   - Timeline & Event Stream Processing                         │   │
│   └────────────────────────────────┬───────────────────────────────┘   │
└────────────────────────────────────┼───────────────────────────────────┘
                                     │ JS API Calls & Event Callbacks
                                     ▼
┌────────────────────────────────────────────────────────────────────────┐
│                      APPLICATION & UI LAYER                            │
│                                                                        │
│   ┌────────────────────────────────────────────────────────────────┐   │
│   │                   Client Frontend (TS / Web)                   │   │
│   │   - Room Views & Active Timeline State                         │   │
│   │   - User Settings & Auth Forms                                 │   │
│   └────────────────────────────────────────────────────────────────┘   │
└────────────────────────────────────────────────────────────────────────┘
```

---

## Layer Breakdown & Responsibilities

### 1. Infrastructure Layer
- **Matrix Synapse**: Implements the official Matrix Client-Server API. Handles user registration, authentication, room creation, event authorization graphs, and message distribution.
- **PostgreSQL**: Stores structured metadata (room state, membership, account data, events).
- **MinIO**: S3-compatible object storage handling avatar images, file attachments, and media uploads.
- **Nginx**: Reverse proxy handling TLS termination, CORS headers, and rate limiting.

### 2. Integration Layer (`@seucra/matrix-sdk-bridge`)
Written in Rust and compiled to WebAssembly, this layer acts as the single source of truth for client-side state:
- Maintains active Matrix session tokens and encryption keys.
- Operates sync loops listening for incoming room events.
- Handles timeline pagination and message history loading.

### 3. Application Layer
The frontend remains lightweight. It does not manipulate Matrix raw JSON directly; instead, it consumes high-level TypeScript interfaces exposed by the WASM bridge.

---

## Key Architectural Decisions

### Decision 1: Single Source of Truth for Session State
Session state lives exclusively inside the Rust WebAssembly module. The frontend cannot bypass the WASM module to make direct HTTP calls to Synapse. This guarantees that session state, cryptographic keys, and token refreshing are strictly governed by Rust code.

### Decision 2: Decoupled Containerized Infrastructure
Running Synapse, PostgreSQL, MinIO, and Nginx inside Docker Compose containers ensures local development mirrors production deployment accurately. Environment variables and volume mounts keep configuration reproducible.

---

## Lessons Learned

Building Vigilant reinforced that **great architecture is about restraint**. By offloading protocol complexity to Matrix Synapse and isolating client state in a Rust WASM bridge, the codebase remains modular, testable, and interview-defensible.
