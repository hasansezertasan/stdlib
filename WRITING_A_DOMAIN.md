# Writing a domain

A walkthrough for adding a new ghost-library domain to this stdlib. Read [`CONVENTIONS.md`](./CONVENTIONS.md) first — this guide assumes you know the file structure and test format.

## Step 1: Pick a name

A domain name is one lowercase word (or snake_case): `strings`, `urls`, `json_path`. It maps directly to a folder.

Good domain names are:

- **Narrow.** "encoding" is too broad; "base64" or "url_encoding" is right.
- **Recognizable.** A reader should infer roughly what's inside from the name alone.
- **Boring.** This isn't where to be clever. `dates` beats `chronos`.

If your idea spans multiple concerns (e.g., "text manipulation + sentiment analysis"), it's probably two domains.

## Step 2: List the functions

Before writing prose, list 3–6 function signatures on a single line each. If you can't fit them, the domain is too big — split it.

Example for a hypothetical `bytes` domain:

```
encode_base64(b: bytes) -> string
decode_base64(s: string) -> bytes
hex_encode(b: bytes) -> string
hex_decode(s: string) -> bytes
```

Sanity-check the list:

- Each function is pure (no I/O, no time, no hidden state).
- The whole domain reads as a coherent set, not a grab-bag.
- You can write 3+ meaningful test cases for each function without straining.

If a function fails any of these, cut it.

## Step 3: Write `SPEC.md`

Use this template:

```markdown
# `<domain>`

<One paragraph: what this domain covers, what's out of scope, any
representation choices (e.g., "dates are ISO strings, no time-of-day").>

## `function_name(arg: type, ...) -> type`

<One-sentence summary.>

**Rules:**
1. <Behavior 1.>
2. <Behavior 2.>
3. <Edge case: empty input.>
4. <Edge case: invalid input — does it raise or return a sentinel?>

**Examples:**
- `function_name(...)` → `...`
- `function_name(...)` → `...`
```

Repeat the per-function block for each function.

### What to nail down

Every spec lives or dies by how it handles edges. Before you stop writing, make sure you've answered:

- **What's the result on empty input?** (`""`, `[]`, `{}`, `0`)
- **What's the result on invalid input?** Return a sentinel (`null`, `""`) or raise?
- **Is there a sort/order guarantee?** If you return a list, is its order defined?
- **What happens at integer/float boundaries?** Negative numbers, very large numbers, `NaN`, infinities?
- **What's the case sensitivity?** Especially for strings, URLs, hex.
- **Does the function mutate inputs?** Default to "no" — say so explicitly only if there's a reason.

If an answer is "implementation may choose," say so. Silence creates ambiguity.

## Step 4: Write `tests.yaml`

For each function, write at least:

- 1 happy-path case from the spec's examples.
- 1 empty-input case.
- 1 boundary case (largest, smallest, off-by-one).
- 1 invalid-input case in `<function>_errors` (if the spec says it raises).

Cover every numbered rule in `SPEC.md` with at least one test case. If a rule isn't testable, ask whether it belongs in the spec at all.

### When fixtures need computing

For `hash`-style domains where output is bytewise-fixed by an algorithm, **never hand-write the expected value**. Run a trusted tool, copy the result, move on. The risk of typo is too high and the cost of a wrong fixture is catastrophic (every implementation that "matches the spec" will be wrong in the same way).

### When you need a callable in YAML

If a function takes a callable (predicate, comparator, key function), don't try to encode it. Instead:

1. Pick or define a **named helper** (see the table in `CONVENTIONS.md`).
2. Reference it by name in the fixture.
3. The runner provides the actual function.

If you need a helper that doesn't exist yet, add it to `CONVENTIONS.md` first.

### When the test isn't about an exact return value

Some functions (PRNG output, function combinators) can't sensibly be tested with input/expected pairs. Use **invariants** or **named scenarios** instead — see `random/tests.yaml` and `functional/tests.yaml` for examples. Document the scenario or invariant convention in the file's header comment so an implementer knows what's expected.

## Step 5: Link it from the README

Add a row to the domains table in [`README.md`](./README.md). One line:

```
| [`<domain>/`](./<domain>/SPEC.md) | <One-line description: 3–5 example functions> |
```

## Step 6: Read your own spec like an outsider

Walk away for an hour. Come back and read `SPEC.md` cold, as if you'd never seen it. Ask:

- Could a competent programmer in any language implement this without asking me a question?
- Could an LLM produce a working implementation from this alone?
- Are the test fixtures sufficient to *catch* a wrong implementation, not just *describe* a right one?

If you hesitate on any of the three, the spec isn't done. Fix it before opening a PR.

## Checklist

- [ ] Folder named after the domain, lowercase / snake_case
- [ ] `SPEC.md` with intro + one section per function
- [ ] Every function has signature, summary, rules, examples
- [ ] Every rule has at least one test fixture covering it
- [ ] Error cases live in `<function>_errors` keys
- [ ] Computed fixtures (hashes, conversions) were generated, not guessed
- [ ] Named helpers exist in `CONVENTIONS.md`
- [ ] Domain linked from `README.md`'s table
- [ ] Spec re-read cold and survives the test
