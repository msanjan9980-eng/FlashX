# Speed Demon 2026 - Team "Deploy or Die"

This repository contains our optimized submissions for all four tracks of the Speed Demon 2026 Hackathon.

## Team & Tech Stack
*   **Team Name:** Deploy or Die
*   **Language:** Rust
*   **Dependencies:** Exactly 1 (`libc`)

## Core Architecture & Optimizations
Across all tracks, we applied low-level systems engineering techniques to achieve maximum performance, leaving behind the standard Python implementations.

*   **Zero-Copy I/O:** We bypass standard file reading and use `libc::mmap` to map the datasets directly into memory, eliminating kernel-to-user space copy overhead.
*   **SIMD & Branchless Parsing:** We use `libc::memchr` (which leverages AVX2/AVX-512 CPU instructions) to scan bytes at 30GB/s. We also avoided `str::parse` by using bitwise operations (`b & 0x0F`) to parse integers directly from raw bytes.
*   **Custom Hashing:** We implemented a custom FNV-1a hasher to replace Rust's default (and slower) SipHash.
*   **Memory Optimization:** We heavily utilized pre-allocated arrays, custom flat arenas, and `select_nth_unstable` (QuickSelect) to avoid dynamic heap allocations and unnecessary sorting.

## Tracks
*   **Track A (Ingest):** Multi-threaded byte parsing with a custom arena and QuickSelect percentiles.
*   **Track B (Index):** Zero-allocation inverted index using flat `Vec<u32>` postings and linear merge intersections for boolean queries.
*   **Track C (Join):** Manual zero-allocation CSV state machine parser with direct integer cent math (no floats).
*   **Track D (Traverse):** Optimized Dijkstra's algorithm using a `BinaryHeap` and a `visited` timestamp array to achieve O(1) distance resets per query.

## Build Instructions
Each track is containerized. The board builds them offline using Docker. To test locally:

```bash
python3 check.py <track_letter> --data data/<track_letter>
