# `iter`

Lazy-or-eager sequence helpers. The spec is written in terms of finite sequences for testability, but implementations are encouraged to support lazy iteration where idiomatic (Python generators, JS iterators, etc.).

All functions return a sequence (list) when called on a list. They preserve input order.

## `take(seq: sequence, n: integer) -> list`

Return the first `n` elements of `seq`.

**Rules:**
1. If `n <= 0`, return `[]`.
2. If `n >= len(seq)`, return all elements.
3. Negative `n` is treated as `0`.

## `drop(seq: sequence, n: integer) -> list`

Return all elements of `seq` after the first `n`.

**Rules:**
1. If `n <= 0`, return all elements.
2. If `n >= len(seq)`, return `[]`.

## `take_while(seq: sequence, predicate: callable) -> list`

Return the longest prefix of `seq` for which `predicate(item)` is truthy. Stop at the first falsy result.

**Examples:**
- `take_while([1, 2, 3, 4, 1], lambda x: x < 4)` → `[1, 2, 3]` (stops at 4; the trailing `1` is **not** included)

## `zip_longest(*seqs: sequence, fill: any = null) -> list of lists`

Like `zip`, but pads shorter sequences with `fill` so the result has length equal to the longest input.

**Rules:**
1. Result length = `max(len(s) for s in seqs)`, or `0` if no sequences.
2. Each element is a list (not a tuple) of one value per input sequence.

**Example:**
- `zip_longest([1, 2, 3], [10, 20], fill=0)` → `[[1, 10], [2, 20], [3, 0]]`

## `flatten(seq: sequence, depth: integer = 1) -> list`

Flatten nested sequences up to `depth` levels deep.

**Rules:**
1. `depth = 1` (default) flattens one level only.
2. `depth = 0` returns `seq` unchanged.
3. `depth = -1` means flatten all the way down.
4. Strings are **not** treated as sequences (they are atoms).

**Examples:**
- `flatten([[1, 2], [3, 4]])` → `[1, 2, 3, 4]`
- `flatten([1, [2, [3, [4]]]], depth=-1)` → `[1, 2, 3, 4]`
- `flatten([1, [2, [3]]], depth=1)` → `[1, 2, [3]]`
- `flatten(["ab", ["cd"]])` → `["ab", "cd"]` (strings not flattened)
