---
layout: post
title:  "Rust Lifetimes"
date:   2020-04-15 21:22:37 +0200
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

A complicated (yet valid) example:

```rust
fn return_first_two(borrowed_list: &[i32]) -> &[i32] {      // borrowed_list is a reference
    &borrowed_list[0..2]
}                                                           // borrowed_list is not dropped because it references something outside of its scope

fn main() {
    let list = vec![100, 34, 72, 55];               // list is created
    let first_two = return_first_two(&list);        // reference is returned (first_two)
    println!("first two are {:?}", first_two);      // first_two is referred
    println!("list is {:?}", list);                 // list is referred
    println!("again, first two: {:?}", first_two);  // first_two is referred
}
```