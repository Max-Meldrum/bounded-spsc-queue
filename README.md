
## Bounded SPSC Queue

[![Nightly Build Status](https://travis-ci.org/polyfractal/bounded-spsc-queue.svg?branch=master)](https://travis-ci.org/polyfractal/bounded-spsc-queue)

This crate provides a very simple bounded, Single-producer Single-consumer (SPSC)
queue for Rust.  It provides a data structure for two threads to communicate
in one direction with minimal overhead and bounded semantics.

Compared to a `sync_channel`, this queue provides a small but consistent
speedup.  `sync_channel` utilizes an unbounded linked-list data structure under the covers,
while `bounded_spsc_queue` is a simple ring buffer with single, solid block of allocated
memory.  The solid block of memory allows better cache pre-fetching due to less pointer
indirection, and generally simpler operations to achieve a bounded SPSC queue.

## Documentation

[Documentation can be found here](http://polyfractal.github.io/bounded-spsc-queue/bounded_spsc_queue/index.html)

## Example

```rust
use std::thread;
use bounded_spsc_queue::{Producer, Consumer};

// Initialize a queue with capacity of 500 values
let (p, c) = bounded_spsc_queue::make(500);

// Spawn a new thread and move the Producer into it
thread::spawn(move|| {
  for i in 0..100000 {
    p.push(i);
  }
});

// Back in the first thread, start pop'ing values off the queue
for i in 0..100000 {
  let t = c.pop();
  assert!(t == i);
}
```
