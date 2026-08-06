# X / Twitter Threads (7 Short Threads)

These short thread manuscripts correspond to the 7 scheduled articles on [journal.seucra.tech](https://journal.seucra.tech).

---

## Thread 1: Building Vigilant — Cycle 1 Complete
```text
1/7 Building a secure messaging app from scratch sounds awesome until you realize protocol engineering & crypto auditing take 95% of your time. 

Here's why I pivoted to Matrix Synapse and Rust WASM for Vigilant 🧵👇

2/7 Instead of reinventing the wheel, we adopted Matrix. Synapse handles event graphs, persistence, and federation. We focus on Rust async backend logic and WebAssembly client integration.

3/7 Cycle 1 completed milestones:
- Session & auth token persistence
- Direct message & public room discovery
- Timeline pagination & event streams
- Standalone WASM bridge package

4/7 Engineering takeaway: Real depth comes from understanding system boundaries & choosing mature protocols so you can focus on application architecture.

Full postmortem: https://journal.seucra.tech/posts/building-vigilant-cycle-1-complete/
```

---

## Thread 2: Extracting Matrix SDK into a Rust WASM Library
```text
1/5 Why you should extract client protocol code into a standalone WebAssembly library 🧵👇

2/5 In early Vigilant builds, Matrix SDK code was mixed with UI state. Any frontend change risked breaking deep async sync loops in Rust.

3/5 We extracted the Rust code into @seucra/matrix-sdk-bridge. Compiling Rust to WASM (`wasm32-unknown-unknown`) gives us memory safety in browser environments with zero JS overhead.

4/5 The JS frontend consumes clean, high-level methods while Rust manages cryptographic sessions and timeline streams.

5/5 Read the full breakdown: https://journal.seucra.tech/posts/extracting-matrix-sdk-into-a-rust-wasm-library/
```

---

## Thread 3: Publishing My First Rust npm Package
```text
1/4 How to compile Rust to WebAssembly and publish an npm package 📦🧵

2/4 Tools used:
- Cargo (`cdylib` crate target)
- `wasm-pack` (`--target web`)
- `wasm-bindgen` for automatic TypeScript definition generation

3/4 Outcome: `@seucra/matrix-sdk-bridge` is published on npm! JS developers get instant type completion for native WASM methods.

4/4 Full guide & build script lessons: https://journal.seucra.tech/posts/publishing-my-first-rust-npm-package/
```

---

## Thread 4: The System Architecture Behind Vigilant
```text
1/5 System architecture is about deciding WHERE boundaries live. 

Here is the 3-tier boundary powering Vigilant 🧵👇

2/5 
1. Infrastructure: Self-hosted Matrix Synapse, PostgreSQL, MinIO, Nginx in Docker.
2. Integration: Rust WASM bridge managing session tokens & sync streams.
3. Application: Lightweight TS frontend.

3/5 Rule: Session state lives EXCLUSIVELY inside WebAssembly. The frontend cannot bypass Rust security controls or make direct HTTP calls.

4/5 Modular containers + WASM boundary = clean, testable, defensible architecture.

5/5 Full architecture breakdown: https://journal.seucra.tech/posts/architecture-behind-vigilant/
```

---

## Thread 5: Matrix Timeline Pagination Design
```text
1/5 Why chat history pagination in distributed protocols like Matrix is tricky 🧵👇

2/5 Matrix timelines are DAGs of signed events. Messages arrive out of order, and state events (member joins/name changes) interrupt streams.

3/5 In Rust, we handle:
- Forward/backward token tracking (`prev_batch`)
- Historical display name context mapping
- Event ID deduplication across sync loops

4/5 Detecting `hit_end` tells the client when history is exhausted, saving server load.

5/5 Deep dive: https://journal.seucra.tech/posts/matrix-timeline-pagination-design/
```

---

## Thread 6: Rebuilding My GitHub Presence & Portfolio
```text
1/5 Uncurated GitHub profiles with 30+ random repos fail to tell your story as an engineer. 

Here's how I reconstructed my digital profile 🧵👇

2/5 Grouped repos into ecosystem categories: `product/`, `library/`, `research/`, `knowledge/`, `infrastructure/`, `historical/`.

3/5 Adopted "EVIDENCE OVER CLAIMS": Every skill on seucra.tech links to project code, npm packages, or server configs instead of rating bars.

4/5 Migrated portfolio to Hugo + Blowfish for a zero-maintenance, documentation-first workflow.

5/5 Full story: https://journal.seucra.tech/posts/rebuilding-my-github-portfolio/
```

---

## Thread 7: Starting My DSA Journey Again
```text
1/5 Rebuilding Computer Science & DSA fundamentals from scratch with a dual-language strategy 🧵👇

2/5 C for raw memory layout, pointer mechanics, `malloc`/`free`. Rust for borrow checker lifetimes, `Box`, `Rc`/`Arc`, and memory safety.

3/5 Goal: Move away from grinding LeetCode problem counts toward understanding database B-Trees, LRU caches, and stream algorithms from first principles.

4/5 Technical confidence comes from first-principles mastery.

5/5 Strategy & notes: https://journal.seucra.tech/posts/starting-my-dsa-journey-again/
```
