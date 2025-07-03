## Update

The `ec-divisors` contest is currently **closed** for submissions.

## Overview

Welcome, contestant!

Please read ALL requirements *carefully* in [`../README.md`](../README.md) before reading this.
Please read this entire README carefully as well.
If you have any questions, do not hesitate to ask in IRC/Matrix #monero-dev,
or create an issue.

The goal of this competition is to optimize the provided `ec-divisors`
implementation.

The reference implementation source code is provided in
[`./ec-divisors-contest-src`](./ec-divisors-contest-src). You may modify all of
[`./ec-divisors-contest-src`](./ec-divisors-contest-src). The tests in
[`./ec-divisors-contest-src/src/tests`](./ec-divisors-contest-src/tests) are
provided for your convenience. You can alter those tests as you see fit. You do
*not* have to pass the tests in [`./ec-divisors-contest-src/src/tests`](./ec-divisors-contest-src/src/tests).

However, you *must* pass the test provided in [`./tests/divisors.rs`](./tests/divisors.rs).

You may *not* modify anything outside of [`./ec-divisors-contest-src`](./ec-divisors-contest-src)
(except for adding/extending trait implementations if you wish).

Again, please read ALL contest requirements carefully at [`../README.md`](../README.md).

## Why

The specific code this contest is aiming to optimize is expected to be
used in Monero's upcoming upgrade to [FCMP++](https://www.getmonero.org/2024/04/27/fcmps.html).
Specifically, it's a major component of FCMP++ prove (used to construct a FCMP++
transaction). Here is a flamegraph of [prove](https://raw.githubusercontent.com/j-berman/fcmp-plus-plus/760b7784c3b77a7f43329317448fe5bcbc00dfd3/crypto/fcmps/flamegraph_prove.svg).
Notice how `scalar_mul_divisor` dominates the flamegraph by an extreme majority.
For reference, the flamegraphs were constructed using [flamegraph](https://github.com/flamegraph-rs/flamegraph),
with the commands detailed [here](https://github.com/j-berman/fcmp-plus-plus/blob/760b7784c3b77a7f43329317448fe5bcbc00dfd3/crypto/fcmps/README.md#flamegraphs).

## Scoring

The benchmark calls 2 functions: `ScalarDecomposition::new` then
`scalar_mul_divisor`. In the integration in Monero, we call
`ScalarDecomposition::new` one fewer times than `scalar_mul_divisor`. Thus, if
we receive two submissions where the overall benchmark improvement is roughly
the same, where submission A has fast `ScalarDecomposition::new` but slow
`scalar_mul_divisor` and submission B vice versa, then submission B is a
stronger submission. Thus, you may be inclined to focus optimizing the benchmark
by moving more logic into the pre-process `ScalarDecomposition::new` step.

## How to run the code

Caution: we use different versions of Rust in different circumstances. Please
be mindful of this.

First, make sure you have rust 1.69.0 and rust 1.84.1 installed.

```
rustup install 1.69.0
rustup install 1.84.1
```

Second, make sure you have the `wasm32v1-none` target installed.

```
rustup +1.84.1 target add wasm32v1-none
```

To run the tests:

```
cargo +1.69.0 test --release -- --nocapture
cargo +1.84.1 test --release -- --nocapture
```

To run the benchmark, only use 1.84.1:

```
cargo +1.84.1 bench
```

If you have build conflict issues (as a result of switching compiler versions),
you may need to remove `target/` and Cargo.lock, then run again.

To run the wasm cycle counter:

```
git clone https://github.com/j-berman/wasm-cycles
cd wasm-cycles
cargo +1.84.1 run --release -- ../fcmp-plus-plus-optimization-competition/ec-divisors-contest +1.84.1
```

Remember, your code must improve BOTH the benchmark and wasm cycle count by at
least 20% in order to qualify as a valid submission. It also must follow ALL
requirements in [`../README.md`](../README.md).

Good luck!
