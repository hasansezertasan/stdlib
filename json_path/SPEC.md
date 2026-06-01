# `json_path`

Dotted-path access to nested data structures. Works on plain values: dicts/maps, lists/arrays, and scalars.

The "path" is a string of segments separated by `.`. Numeric segments index into lists; non-numeric segments index into dicts.

This is **not JSONPath the W3C draft** — it's a much simpler subset. No wildcards, no filters, no recursion operators.

## Path grammar

- `"a.b.c"` — dict path
- `"items.0.name"` — list index `0`, then `name`
- `""` (empty string) — the document itself

## `get_path(data, path: string) -> value`

Walk `data` following `path` and return the value found.

**Rules:**
1. Numeric segments are interpreted as **list indices** (zero-based, non-negative only — `-1` is not supported in v1).
2. Non-numeric segments are interpreted as **dict keys**.
3. If any segment is missing, return `null` (do **not** raise).
4. If a segment is the wrong kind for its container (e.g., string key into a list), return `null`.

**Examples:**
- `get_path({"a": {"b": 42}}, "a.b")` → `42`
- `get_path({"items": [{"name": "x"}]}, "items.0.name")` → `"x"`
- `get_path({"a": 1}, "a.b")` → `null`
- `get_path({"a": 1}, "")` → `{"a": 1}`

## `set_path(data, path: string, value) -> new data`

Return a new structure with `value` set at `path`. **Input is not mutated**.

**Rules:**
1. Missing intermediate dicts are auto-created. **Missing list indices are not** — setting `"items.5"` when `items` has length 2 raises an error.
2. If an intermediate segment exists but is the wrong kind, raise an error.
3. An empty `path` replaces the entire document.

**Examples:**
- `set_path({}, "a.b", 1)` → `{"a": {"b": 1}}`
- `set_path({"a": [1, 2]}, "a.0", 99)` → `{"a": [99, 2]}`
- `set_path({"a": 1}, "", "new")` → `"new"`

## `merge_deep(left, right) -> merged`

Recursively merge two dicts. **Input is not mutated.**

**Rules:**
1. Both inputs must be dicts at the top level. If not, return `right` unchanged.
2. For each key in `right`:
   - If both `left[key]` and `right[key]` are dicts → recurse.
   - Otherwise → `right[key]` wins.
3. Lists are **not merged** — they are replaced wholesale by the right-hand side.

**Examples:**
- `merge_deep({"a": 1, "b": 2}, {"b": 3, "c": 4})` → `{"a": 1, "b": 3, "c": 4}`
- `merge_deep({"a": {"x": 1}}, {"a": {"y": 2}})` → `{"a": {"x": 1, "y": 2}}`
- `merge_deep({"a": [1, 2]}, {"a": [3]})` → `{"a": [3]}` (lists not merged)

## `flatten(data) -> dict of {path: leaf}`

Flatten a nested structure into a single dict whose keys are dotted paths.

**Rules:**
1. Recurse into dicts and lists. Other values are leaves.
2. List indices are stringified (`"0"`, `"1"`, ...).
3. Empty dicts and empty lists produce no entries.

**Examples:**
- `flatten({"a": {"b": 1, "c": [10, 20]}})` → `{"a.b": 1, "a.c.0": 10, "a.c.1": 20}`
- `flatten({})` → `{}`
- `flatten({"a": []})` → `{}`
