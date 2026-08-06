---
title: Starting My DSA Journey Again (With Engineering Intention)
date: 2026-09-03 10:00:00 +0530
categories: [Learning, Fundamentals]
tags: [dsa, rust, c, algorithms, computer-science]
author: Shams Tabrez Ahmed
description: Reflections on rebuilding Computer Science & Data Structure fundamentals from scratch, focusing on deep understanding over problem counts.
math: true
mermaid: true
---

## Introduction

In Computer Science education, Data Structures and Algorithms (DSA) are often taught as isolated academic exercises. Students memorize patterns to pass exams or grind LeetCode problems without connecting those algorithms to actual software engineering.

During my 3rd semester, my DSA coursework covered standard academic implementations (linked lists, trees, sorting algorithms, graph traversals). But like many students, after the semester ended, much of that knowledge became rusty due to lack of deliberate practice.

As I started building complex backend systems like **Vigilant**, I realized that core computer science fundamentals—memory layout, time complexity, pointer safety, tree balancing, and hash collisions—are not just interview requirements; they are the foundation of writing efficient, reliable software.

This post marks the beginning of my **systematic DSA rebuild**.

---

## Why Rebuild DSA Now?

### 1. Code Reading vs. Independent Implementation
Over the past year, my ability to read code, design architectures, and debug systems grew significantly. However, my independent ability to implement complex algorithms from scratch without assistance needed deliberate practice.

### 2. Deepening Rust Ownership & Lifetimes
Implementing data structures in Rust (like doubly linked lists, binary trees, or custom graphs) is notoriously challenging because Rust's borrow checker enforces strict aliasing rules. Implementing data structures in Rust forces you to truly master references, `Box`, `Option`, smart pointers (`Rc`/`Arc`), and interior mutability (`RefCell`).

### 3. Shift from Problem Counts to Structural Mastery
Instead of aiming to solve 500 random LeetCode problems, my goal is to build a structured **Computer Science knowledge repository**:

```
DSA Repository Structure
├── algorithms/       ──► Sorting, Searching, Graph, Dynamic Programming
├── data-structures/  ──► Vectors, Linked Lists, Trees, Heaps, Hash Maps, Graphs
├── patterns/         ──► Two Pointers, Sliding Window, Fast/Slow Pointers
├── revision/         ──► Periodic concept reviews & benchmark implementations
└── notes/            ──► Deep-dive breakdowns on memory layout & trade-offs
```

---

## The Rebuild Strategy

1. **Dual Language Approach (C & Rust)**:
   - **C**: To understand raw pointers, manual memory allocation (`malloc`/`free`), cache locality, and memory layout.
   - **Rust**: To understand memory safety, type abstractions, pattern matching, and generic implementations.

2. **Documenting the Journey**:
   - Write clear explanations for *why* an algorithm works, its space/time trade-offs, and where it appears in real production systems (e.g., how B-Trees power database indexes, or how LRU caches manage memory).

---

## Conclusion

Rebuilding computer science fundamentals is a long-term commitment. True technical confidence comes from knowing that when you face a performance bottleneck or algorithmic challenge, you can break it down, analyze its complexity, and write a clean, defensible solution from first principles.
