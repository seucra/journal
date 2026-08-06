# LinkedIn Posts Manuscripts (7 Posts)

These post manuscripts correspond to the 7 scheduled articles on [journal.seucra.tech](https://journal.seucra.tech). 
Post each on LinkedIn 1–2 days after the corresponding journal article publication date.

---

## Post 1: Building Vigilant — Cycle 1 Complete
**Scheduled Date**: August 11–12, 2026  
**Target Article**: `https://journal.seucra.tech/posts/building-vigilant-cycle-1-complete/`

### Post Copy:
```text
When building an ambitious backend project, it is easy to get stuck solving the wrong problem.

When I started building Vigilant (a secure messaging application), my initial reaction was to write everything from scratch: custom protocols, custom encryption handlers, custom sync loops. 

It didn't take long to realize that building cryptographic protocols from scratch is a massive trap. Instead of focusing on backend architecture, room management, and client integration, I was spending 90% of my time reinventing mature infrastructure.

The turning point? Adopting the open Matrix protocol and self-hosting Matrix Synapse. 

Building on top of established standards allowed me to focus on real engineering depth:
- Writing a Rust backend & WebAssembly bridge.
- Managing session persistence and timeline synchronization.
- Deploying a multi-container Synapse, Postgres, and MinIO stack.

Cycle 1 of Vigilant's backend is officially complete. 

Key lesson: Great software engineering isn't about reinventing the wheel—it's about understanding system boundaries and spending effort where it creates genuine value.

Read the full engineering postmortem here: https://journal.seucra.tech/posts/building-vigilant-cycle-1-complete/

GitHub: https://github.com/seucra/Vigilant
#Rust #Matrix #BackendEngineering #Systems #WebAssembly #OpenSource
```

---

## Post 2: Extracting Matrix SDK into a Rust WASM Library
**Scheduled Date**: August 15–16, 2026  
**Target Article**: `https://journal.seucra.tech/posts/extracting-matrix-sdk-into-a-rust-wasm-library/`

### Post Copy:
```text
As your application grows, keeping backend protocol code tightly coupled with UI state creates a massive maintenance bottleneck.

While building Vigilant, our Matrix SDK integration logic was initially mixed directly into application handlers. Any change to the UI threatened to break deep async sync loops in Rust.

To solve this, I extracted the Rust integration into a standalone WebAssembly bridge: `@seucra/matrix-sdk-bridge`.

Why WebAssembly?
1. Memory Safety: Rust's borrow checker guarantees thread safety and zero-cost abstractions inside the browser.
2. Clean Architecture: JavaScript handles UI rendering, while WebAssembly encapsulates protocol state, cryptographic sessions, and timeline pagination.
3. Decoupled Workflow: The frontend team can consume clean, high-level JS methods without needing to modify Rust code.

Treating internal tooling as an independent library forces cleaner abstractions and higher code quality.

Read the technical breakdown here: https://journal.seucra.tech/posts/extracting-matrix-sdk-into-a-rust-wasm-library/

npm: https://www.npmjs.com/package/@seucra/matrix-sdk-bridge
#Rust #WebAssembly #WASM #SoftwareArchitecture #Matrix #TypeScript
```

---

## Post 3: Publishing My First Rust npm Package
**Scheduled Date**: August 19–20, 2026  
**Target Article**: `https://journal.seucra.tech/posts/publishing-my-first-rust-npm-package/`

### Post Copy:
```text
Publishing an npm package written in Rust and compiled to WebAssembly involves a completely different set of tooling than standard JavaScript development.

I recently published `@seucra/matrix-sdk-bridge` on npm. Here is the engineering workflow behind it:

1. Dynamic Crate Configuration (`cdylib`): Configuring `Cargo.toml` so Rust outputs a dynamic system library targeting WebAssembly (`wasm32-unknown-unknown`).
2. Build Automation (`wasm-pack`): Using `--target web` to compile Rust to native WASM with ES module loaders—avoiding complex bundler hacks for consumers.
3. Automatic TypeScript Definitions: `wasm-bindgen` automatically generates clean `.d.ts` typing files for Rust structs, giving JS developers instant IntelliSense.

Bridging systems programming with modern web delivery opens up massive possibilities for high-performance web applications.

Full step-by-step breakdown: https://journal.seucra.tech/posts/publishing-my-first-rust-npm-package/

#Rust #npm #WebAssembly #OpenSource #SystemsProgramming #WebDev
```

---

## Post 4: The System Architecture Behind Vigilant
**Scheduled Date**: August 23–24, 2026  
**Target Article**: `https://journal.seucra.tech/posts/architecture-behind-vigilant/`

### Post Copy:
```text
System architecture is ultimately about deciding WHERE boundaries live.

In Vigilant, we enforce a strict 3-tier boundary:
1. Infrastructure Layer: Self-hosted Matrix Synapse, PostgreSQL, MinIO, and Nginx operating in Docker containers.
2. Integration Layer: A WebAssembly module written in Rust (`@seucra/matrix-sdk-bridge`) managing session tokens, sync streams, and cryptographic state.
3. Application Layer: A lightweight frontend focused strictly on UI views.

By enforcing that session state lives EXCLUSIVELY inside the WASM module, the frontend cannot bypass security controls or make unstructured HTTP calls.

Restraint in architecture produces testable, defensible, and modular software.

Full architectural breakdown and system diagrams: https://journal.seucra.tech/posts/architecture-behind-vigilant/

#SystemArchitecture #Backend #DistributedSystems #Rust #Docker #Linux
```

---

## Post 5: Matrix Timeline Pagination Design
**Scheduled Date**: August 27–28, 2026  
**Target Article**: `https://journal.seucra.tech/posts/matrix-timeline-pagination-design/`

### Post Copy:
```text
Loading chat history sounds simple: scroll up and fetch older messages. 

In distributed protocols like Matrix, timelines are directed acyclic graphs (DAGs) of events. Messages can arrive out of order, state events interrupt streams, and pagination requires tracking forward and backward tokens across sync streams.

While implementing room history pagination in Rust, three key challenges emerged:
1. State Event Ordering: Ensuring historical display names and room states match the exact point in time of the paginated batch.
2. Deduplication: Eliminating duplicate event IDs across concurrent sync loops.
3. Boundary Detection: Recognizing when `hit_end` is reached to stop sending unnecessary `/messages` HTTP requests to Synapse.

Understanding low-level protocol mechanics like tokenized pagination transforms how you approach distributed data streams.

Deep dive into Matrix pagination: https://journal.seucra.tech/posts/matrix-timeline-pagination-design/

#Matrix #Rust #DistributedSystems #Backend #Algorithms #AsyncRust
```

---

## Post 6: Rebuilding My GitHub Presence & Portfolio
**Scheduled Date**: August 31 – September 1, 2026  
**Target Article**: `https://journal.seucra.tech/posts/rebuilding-my-github-portfolio/`

### Post Copy:
```text
Over several years of learning to code, every developer accumulates repositories: hackathon prototypes, class assignments, and half-finished experiments.

Eventually, you end up with 30+ repositories that fail to communicate who you are as an engineer.

I recently completed a Digital Profile Reconstruction:
1. Organized my repositories into a clear ecosystem (`product/`, `library/`, `research/`, `knowledge/`, `infrastructure/`, `historical/`).
2. Migrated my portfolio site to Hugo + Blowfish for a documentation-first, zero-maintenance layout.
3. Adopted a strict philosophy of EVIDENCE OVER CLAIMS.

Instead of meaningless percentage bars ("Rust: 80%"), every skill on my site links directly to project code, npm packages, and infrastructure records.

Present your work like an engineer—not a collector of code snippets.

Read the story behind the migration: https://journal.seucra.tech/posts/rebuilding-my-github-portfolio/

Portfolio: https://seucra.tech
#Career #SoftwareEngineering #GitHub #Portfolio #OpenSource
```

---

## Post 7: Starting My DSA Journey Again (With Engineering Intention)
**Scheduled Date**: September 4–5, 2026  
**Target Article**: `https://journal.seucra.tech/posts/starting-my-dsa-journey-again/`

### Post Copy:
```text
Data Structures and Algorithms (DSA) are too often taught as isolated academic exercises. Students grind problem counts without understanding how algorithms power real backend systems.

I'm starting a systematic DSA rebuild with a dual-language strategy:
- C: To master manual memory allocation (`malloc`/`free`), pointer mechanics, and memory layout.
- Rust: To master borrow checker lifetimes, smart pointers (`Rc`/`Arc`), and zero-cost abstractions.

Instead of aiming to solve 500 random LeetCode problems, the focus is on structural understanding—connecting algorithms to database indexing (B-Trees), memory caches (LRU), and stream processing.

True technical confidence comes from knowing you can break down complex problems from first principles.

My strategy & notes: https://journal.seucra.tech/posts/starting-my-dsa-journey-again/

#DataStructures #Algorithms #Rust #C #ComputerScience #Learning
```
