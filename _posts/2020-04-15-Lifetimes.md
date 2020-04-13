---
layout: post
title:  "Rust Lifetimes"
date:   2020-02-12 21:22:37 +0200
categories: rust
---

Coming from a background in C/C++ and other programming languages doesn't help with understanding this one fundamental concept in Rust. Here, I'll try to break it down so it becomes a bit more palatable (and a reference to my future self.)

A simple example:

```rust
fn main() {
    let list = vec![100, 34, 72, 55];               // list is created
    let first_two = &list[0..2];                    // reference is created (first_two)
    println!("first two are {:?}", first_two);      // first_two is referred
    println!("list is {:?}", list);                 // list is referred
    println!("again, first two: {:?}", first_two);  // first_two is referred
}                                                   // first_two is dropped, then, list is dropped
```