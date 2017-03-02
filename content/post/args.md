+++
tags = ["rust"]
draft = false
scripts = []
css = []
highlight = true
date = "2017-03-01T21:14:46Z"
title = "Least-Overhead Modeling & Laziness | Thinking In Rust #1"
description = "Something"

+++

In trying to understand how to start "Thinking In Rust", why Rust – the language as well as the stdlib – does some things the way it does, and in order to learn which practices imitate in my own code, this was a question that came up for me:

__Question:__ Why does [`std::env::args()`](https://doc.rust-lang.org/std/env/fn.args.html) not just return a `Vec<String>`, instead of a custom struct that implements `trait Iterator`?

__Short Answer:__ So you don't have to allocate that `Vec` if you don't want to.

__Longer Answer:__ The answer to "what were the arguments to the currently executing program's invocation?" (implemented in `std::env::args()`) is something that stdlib asks the underlying system for. In there, the answer does not exist in an already-allocated array (in which case rust could have unsafe-ly created a `Vec` from that memory and returned that), and so Rust will instead give you a custom data structure that represents accessing that answer in the least-overhead way possible. In addition to that, there is more overhead involved since the arguments need to be converted from OsStr, which may or may not be valid UTF8 strings, if arg #5 is `--ignore-the-rest-of-the-args` then you don't need to continue and thus save some effort.

__Learnings:__

* Don't be afraid to create new custom types in order to more accurately model what is actually present without additional transformation.
* Accurate modeling leads to less overhead.
* Take advantage of laziness that is enabled by functions (and traits) that provide data transformation on-demand; in contrast to pre-transforming all available relevant data and passing it on as a concrete data structure like `Vec`.