---
title: Matrix Timeline Pagination Design
date: 2026-08-26 10:00:00 +0530
categories: [Matrix, Rust]
tags: [matrix, pagination, rust, WASM, algorithms, backend]
author: Shams Tabrez Ahmed
description: Understanding room timeline loading, pagination tokens, sliding sync, and state ordering in Matrix clients.
math: true
mermaid: true
---

## Introduction

In chat applications, loading message history seems trivial on the surface: scroll up, fetch previous messages, and prepended them to the view.

In distributed protocols like **Matrix**, timeline pagination is significantly more complex. Matrix rooms are directed acyclic graphs (DAGs) of signed events. Messages can arrive out of order, state events (like user joins or topic changes) interrupt message streams, and pagination requires tracking forward and backward tokens across sync streams.

While implementing timeline loading for **Vigilant** inside our Rust WebAssembly bridge, I had to study how Matrix timeline pagination works under the hood.

This post explains the mechanics of Matrix timeline pagination and how we implemented it in Rust.

---

## How Matrix Pagination Works

In Matrix, messages and room events are returned in sync streams. Each room maintains a timeline timeline vector flanked by pagination tokens:

```
           BACKWARD PAGINATION                          FORWARD SYNC
  ◄──────────────────────────────────           ────────────────────────►

  [Oldest Event] ◄── [Prev Token] ◄── [Timeline Events] ──► [Next Token] ──► [Live Events]
```

When a client opens a room:
1. **Initial Sync**: The server returns the most recent batch of events along with a `prev_batch` token representing the boundary for older history.
2. **Back-Pagination Request**: When the user scrolls up, the client sends a `/messages` request passing the `prev_batch` token and a direction (`b` for backward).
3. **Chunk Response**: The server returns a chunk of older events and a *new* `prev_batch` token for the next page.

---

## Implementing Timeline Pagination in Rust

Using Rust's `matrix-sdk`, handling timeline pagination involves using `matrix_sdk::room::timeline::Timeline`.

Here is an abbreviated code pattern demonstrating how we handle timeline pagination inside our WASM bridge:

```rust
use matrix_sdk::room::timeline::Timeline;
use wasm_bindgen::prelude::*;

#[wasm_bindgen]
pub struct RoomTimelineHandle {
    timeline: Timeline,
}

#[wasm_bindgen]
impl RoomTimelineHandle {
    /// Paginate backward to fetch older messages
    pub async fn paginate_backwards(&self, batch_size: u16) -> Result<bool, JsValue> {
        // Request backward pagination from Matrix SDK
        let hit_end = self.timeline
            .paginate_backwards(batch_size.into())
            .await
            .map_err(|e| JsValue::from_str(&format!("Pagination error: {:?}", e)))?;

        // Returns true if we reached the beginning of the room history
        Ok(hit_end)
    }
}
```

---

## Challenges & Key Insights

### 1. State Events vs. Message Events
Matrix timelines contain both `m.room.message` events and state events like `m.room.member` or `m.room.topic`. When paginating backward, state changes must be applied in reverse context so usernames and display names match the historical state of the room at that point in time.

### 2. Deduplication & Event Ordering
Network retries or concurrent sync streams can cause duplicate events. We enforce event ID deduplication in Rust before exposing timeline arrays to WebAssembly/JavaScript.

### 3. Graceful History Boundaries
Detecting when a room history has reached its beginning (`hit_end = true`) is critical to stop sending unnecessary `/messages` requests to the Synapse homeserver.

---

## Conclusion

Understanding protocol-level mechanics like timeline pagination transforms how you write software. Building on top of Matrix forces you to appreciate distributed event DAGs, tokenized pagination, and state synchronization—concepts that apply far beyond chat applications.
