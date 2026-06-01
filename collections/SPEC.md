# `collections`

Sequence helpers. All functions are pure and preserve the input's order.

## `chunk(seq: sequence, size: integer) -> list of lists`

Split `seq` into contiguous sub-lists of at most `size` elements.

**Rules:**
1. `size` must be `>= 1`. If `size < 1`, raise an error (e.g., `ValueError`).
2. The last chunk may be shorter than `size` if `len(seq)` is not a multiple of `size`.
3. If `seq` is empty, return `[]`.

**Examples:**
- `chunk([1, 2, 3, 4, 5], 2)` → `[[1, 2], [3, 4], [5]]`
- `chunk([], 3)` → `[]`
- `chunk([1, 2, 3], 5)` → `[[1, 2, 3]]`

## `partition(seq: sequence, predicate: callable) -> (list, list)`

Split `seq` into two lists based on whether `predicate(item)` is truthy.

**Returns:** a 2-tuple `(truthy_items, falsy_items)`. Order within each list matches the original order in `seq`.

**Examples:**
- `partition([1, 2, 3, 4], lambda x: x % 2 == 0)` → `([2, 4], [1, 3])`
- `partition([], lambda x: True)` → `([], [])`

## `group_by(seq: sequence, key_fn: callable) -> dict`

Group items of `seq` by the value of `key_fn(item)`.

**Rules:**
1. Returns a dict mapping each distinct key to a list of items that produced it.
2. Items within each group preserve their original order from `seq`.
3. Key insertion order in the returned dict matches the order keys first appear in `seq` (relevant for ordered-dict languages like Python 3.7+).

**Examples:**
- `group_by(["apple", "ant", "bee"], lambda s: s[0])` → `{"a": ["apple", "ant"], "b": ["bee"]}`
- `group_by([], lambda x: x)` → `{}`
