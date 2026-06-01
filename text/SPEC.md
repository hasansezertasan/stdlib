# `text`

Heavier text analysis. Sibling to `strings`, but where `strings` does single-character transforms, `text` does multi-character analysis.

## `tokenize(s: string) -> list of string`

Split `s` into tokens. A token is a maximal run of word characters (`[A-Za-z0-9_]`).

**Rules:**
1. Non-word characters are separators and are discarded.
2. Returns `[]` for an empty or whitespace-only string.
3. Tokens preserve their original case.

**Examples:**
- `tokenize("Hello, world!")` → `["Hello", "world"]`
- `tokenize("user_name = 42")` → `["user_name", "42"]`
- `tokenize("")` → `[]`

## `word_count(s: string) -> integer`

Return the number of tokens in `s`, equivalent to `len(tokenize(s))`.

## `sentence_split(s: string) -> list of string`

Split `s` into sentences.

**Rules:**
1. Sentence-ending punctuation: `.`, `!`, `?`.
2. After splitting, each sentence is trimmed of leading and trailing whitespace.
3. The terminal punctuation is **kept** as part of the sentence.
4. Empty sentences (from consecutive terminators) are discarded.
5. This is a **pragmatic** splitter. It does not handle abbreviations ("Mr. Smith"), ellipses, or quotation marks — those split where a strict reader would not.

**Examples:**
- `sentence_split("Hello. World!")` → `["Hello.", "World!"]`
- `sentence_split("Hi.")` → `["Hi."]`
- `sentence_split("  One. Two.  Three? ")` → `["One.", "Two.", "Three?"]`
- `sentence_split("")` → `[]`

## `levenshtein(a: string, b: string) -> integer`

Compute the Levenshtein edit distance between `a` and `b`: the minimum number of single-character insertions, deletions, or substitutions to turn `a` into `b`.

**Rules:**
1. Each insertion, deletion, and substitution costs 1.
2. `levenshtein(s, s)` is always `0`.
3. `levenshtein(s, "")` equals `len(s)`.
4. Operates on Unicode code points (not bytes).

**Examples:**
- `levenshtein("kitten", "sitting")` → `3`
- `levenshtein("", "abc")` → `3`
- `levenshtein("flaw", "lawn")` → `2`
