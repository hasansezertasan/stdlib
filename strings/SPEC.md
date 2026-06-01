# `strings`

Text-manipulation helpers. All functions are pure: same input → same output, no side effects.

## `slugify(text: string) -> string`

Convert arbitrary text into a URL-safe slug.

**Rules:**
1. Lowercase the entire string.
2. Replace any run of non-`[a-z0-9]` characters (ASCII only — non-ASCII letters are treated as separators) with a single `-`.
3. Strip leading and trailing `-`.
4. If the result is empty, return `""`.

**Examples:**
- `slugify("Hello, World!")` → `"hello-world"`
- `slugify("  Multiple   Spaces  ")` → `"multiple-spaces"`
- `slugify("___underscores___")` → `"underscores"`
- `slugify("!!!")` → `""`

## `truncate(text: string, length: integer, suffix: string = "...") -> string`

Shorten `text` so its total length does not exceed `length`.

**Rules:**
1. If `len(text) <= length`, return `text` unchanged.
2. Otherwise, return `text[: length - len(suffix)] + suffix`.
3. If `length < len(suffix)`, return `suffix[:length]` (truncate the suffix itself).
4. `length` must be `>= 0`. Negative `length` is undefined behavior; implementations may raise.

**Examples:**
- `truncate("hello world", 11)` → `"hello world"`
- `truncate("hello world", 8)` → `"hello..."`
- `truncate("hello", 2)` → `".."`

## `pluralize(word: string, count: integer) -> string`

Return the English plural of `word` when `count != 1`, otherwise return `word` unchanged.

**Rules (applied in order):**
1. If `count == 1`, return `word`.
2. If `word` ends in a consonant followed by `y` → replace `y` with `ies`.
3. If `word` ends in `s`, `x`, `z`, `ch`, or `sh` → append `es`.
4. Otherwise → append `s`.

Pluralization preserves the case of the original ending (assume input is lowercase for v1).

**Examples:**
- `pluralize("cat", 1)` → `"cat"`
- `pluralize("cat", 2)` → `"cats"`
- `pluralize("city", 3)` → `"cities"`
- `pluralize("box", 2)` → `"boxes"`
- `pluralize("day", 2)` → `"days"` (vowel before `y`)
