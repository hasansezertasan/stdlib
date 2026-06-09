# `random`

Seedable randomness. **Determinism across languages is not promised** — the underlying PRNG differs. Within a single implementation, however, a fixed seed always produces the same output.

For cross-language deterministic streams, use `hash.fingerprint` over a counter instead.

## RNG contract

Every function in this domain accepts an optional `rng` parameter — an opaque, language-specific generator object. When `rng` is omitted, implementations must use a process-global generator that can be seeded by the language's idiomatic API. Tests below construct an `rng` from a known seed (the runner provides the seed-to-rng conversion).

This separation lets tests be deterministic (always pass an `rng`) while production code stays ergonomic.

## `pick(seq: sequence, rng) -> element`

Return one element of `seq`, chosen uniformly at random.

**Rules:**
1. `seq` must be non-empty. Empty input raises an error.
2. The chosen element is a member of `seq` (no transformation).

## `shuffle(seq: sequence, rng) -> list`

Return a new list containing all elements of `seq` in a uniformly random order. The input is **not mutated** — this is a functional shuffle.

**Rules:**
1. `len(shuffle(seq, rng)) == len(seq)`.
2. Element multiset is preserved.
3. Use Fisher–Yates (Knuth) algorithm.

## `weighted_pick(items: list, weights: list, rng) -> element`

Return one element of `items`, chosen with probability proportional to its weight.

**Rules:**
1. `len(items) == len(weights)`. Otherwise raise an error.
2. `items` must be non-empty.
3. All weights must be `>= 0` and at least one must be `> 0`.
4. The probability of selecting `items[i]` is `weights[i] / sum(weights)`.

## Tests

Tests check **invariants** rather than exact values, since the PRNG output is language-dependent:

- `pick` always returns a member of the input.
- `shuffle` preserves length and multiset.
- `weighted_pick` with a weight of `0` for an item never returns that item.
- `weighted_pick` with all-equal weights converges to uniform distribution (sampled `N` times, each item's count is within tolerance).
- Re-seeding the RNG reproduces the same sequence within the same implementation.
