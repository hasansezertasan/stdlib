# Conventions

The cross-domain rules every `SPEC.md` and `tests.yaml` in this repo follows. Read this once; then each domain becomes predictable.

## File layout

Each domain lives in its own folder:

```
<domain>/
├── SPEC.md       # the contract
└── tests.yaml    # the fixtures
```

Nothing else. No README per domain, no examples folder, no per-language code. If a fact about the domain is worth recording, it goes in `SPEC.md`.

## `SPEC.md` structure

Every `SPEC.md` is structured as:

1. **One-paragraph intro.** What the domain is, what's out of scope.
2. **One section per function**, ordered by how a reader would learn them (simple first, derived last).
3. **Per function:**
   - Signature line: ``` `name(arg: type, ...) -> type` ```
   - One-sentence summary.
   - **Rules:** numbered list of behaviors, including edge cases and error conditions.
   - **Examples:** input → output pairs in prose. Same examples must appear in `tests.yaml`.

Specs describe **behavior**, not implementation. They never say "use a hash map" or "iterate left-to-right" unless that detail is observable. They name the algorithm only when byte-exact cross-language output is required (e.g., `hash`).

## `tests.yaml` structure

Each top-level key is a function name. Its value is a list of test cases.

### Standard case

```yaml
function_name:
  - { input: <arg or [arg, arg, ...]>, expected: <value> }
```

- For unary functions, `input` is the single argument.
- For n-ary functions, `input` is a list of arguments in declaration order.
- `expected` is the return value the function must produce.

### Error cases

A separate top-level key, `<function_name>_errors`, lists inputs that must cause the function to raise an error (or otherwise fail loudly — the spec defines what counts).

```yaml
function_name_errors:
  - { input: <args that should fail> }
```

The runner's job is to assert that calling the function with these args does **not** return normally.

### Multi-argument with optional args

Trailing optional args may be omitted from the `input` list. Example: `truncate(text, length, suffix="...")` accepts a 2-element or 3-element `input`.

### Floats

Float-valued `expected` values are compared with `is_close(rel_tol=1e-3, abs_tol=1e-3)`. The runner is responsible for applying this tolerance — fixtures should be written with at most 3–4 significant digits beyond integer values.

### Named predicates

Some functions (`collections.partition`, `collections.group_by`, `iter.take_while`) take a callable. YAML can't encode callables, so the test file references them by name. The runner is expected to provide these:

**Predicates** (`x -> bool`):

| Name | Definition |
| --- | --- |
| `is_even` | `x % 2 == 0` |
| `is_positive` | `x > 0` |
| `is_truthy` | `bool(x)` |
| `lt_4` | `x < 4` |

**Key functions** (`x -> any`):

| Name | Definition |
| --- | --- |
| `identity` | `x` |
| `first_char` | `s[0]` |
| `length` | `len(x)` |

If a new domain needs a new helper, add it to this table and to the relevant `tests.yaml` header comment.

### Named scenarios

The `functional` and `random` domains use scenarios instead of input/output pairs because their inputs (functions, RNG samples) can't be encoded as plain values. Each scenario names a concrete setup the runner implements (e.g., `double_after_increment` for `compose`). Scenarios are documented inline in the relevant `tests.yaml`.

### Computed fixtures

When a function's output is determined by a published algorithm (`hash`, `color`), fixtures must be **computed**, not hand-written. The risk of typing the wrong hex digit is too high. Compute the value with a known-correct tool, paste it into the fixture, and move on. A comment indicating how the value was derived is helpful but not required.

## What counts as "pure" in this stdlib

Unless `SPEC.md` says otherwise, every function in this stdlib is:

- **Deterministic** — same input → same output.
- **Pure** — no I/O, no time, no randomness sourced from outside the arguments.
- **Non-mutating** — input data structures are returned unchanged; new structures are returned for any change.

Exceptions are explicit: `random` accepts an `rng` argument that carries state; `dates.format_relative` takes `today` explicitly rather than reading the clock.

## Error handling

When a spec says "raises an error," the test for that case lives in `<function>_errors` and the runner asserts the function did not return normally. The **kind** of error (which exception class, which error code) is intentionally not specified — that's an implementation detail. The spec only mandates that callers can detect failure.

## Versioning

This stdlib is at v1. Breaking changes to any `SPEC.md` should bump a (future) version field at the top of that spec. For v1, assume specs are stable enough to build against but not yet frozen.
