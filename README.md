# stdlib

A standard library with no code.

`stdlib` is a **spec-only standard library**: each module is defined by a human-readable specification (`SPEC.md`) and a language-agnostic test suite (`tests.yaml`). No implementation is bundled. You — or an LLM — generate the implementation in whatever language you want, then verify it against the tests.

Inspired by [whenwords](https://github.com/dbreunig/whenwords), scaled to multiple domains.

## Why?

- **Language-agnostic by design.** The spec is the product; code is just one possible shadow of it.
- **LLM-native.** Paste the spec + tests into any model, get a working implementation.
- **Teaches Document-Driven Development.** Docs come first, code comes second, and the docs stay the source of truth.

## Domains

Each folder is a self-contained ghost library. Take one, take all — they don't depend on each other.

| Domain | What it covers |
| --- | --- |
| [`strings/`](./strings/SPEC.md) | Text manipulation: `slugify`, `truncate`, `pluralize` |
| [`collections/`](./collections/SPEC.md) | Sequence helpers: `chunk`, `partition`, `group_by` |
| [`validation/`](./validation/SPEC.md) | Predicates: `is_email`, `is_url`, `is_uuid` |
| [`numbers/`](./numbers/SPEC.md) | Numeric helpers: `clamp`, `round_to`, `lerp`, `is_close`, `sign` |
| [`dates/`](./dates/SPEC.md) | Calendar ops: `add_days`, `days_between`, `is_weekend`, `format_relative`, `parse_iso` |
| [`paths/`](./paths/SPEC.md) | Pure path string ops: `join`, `parent`, `extension`, `basename`, `normalize` |
| [`urls/`](./urls/SPEC.md) | URL string ops: `parse`, `build`, `add_query_param`, `strip_query`, `url_join` |
| [`iter/`](./iter/SPEC.md) | Sequence streams: `take`, `drop`, `take_while`, `zip_longest`, `flatten` |
| [`text/`](./text/SPEC.md) | Heavier text: `word_count`, `sentence_split`, `levenshtein`, `tokenize` |
| [`functional/`](./functional/SPEC.md) | Function combinators: `compose`, `pipe`, `memoize`, `curry` |
| [`hash/`](./hash/SPEC.md) | Stable digests: `md5_hex`, `sha256_hex`, `fingerprint` |
| [`color/`](./color/SPEC.md) | Color math: `hex_to_rgb`, `rgb_to_hex`, `rgb_to_hsl`, `lighten` |
| [`random/`](./random/SPEC.md) | Seedable randomness: `pick`, `shuffle`, `weighted_pick` |
| [`money/`](./money/SPEC.md) | Currency strings: `format_currency`, `parse_currency`, `round_to_cents` |
| [`json_path/`](./json_path/SPEC.md) | Nested data access: `get_path`, `set_path`, `merge_deep`, `flatten` |

## How to use

1. Pick a domain. Read its `SPEC.md`.
2. Hand the spec + `tests.yaml` to your favorite LLM (or write the code yourself, in any language).
3. Run the tests against your implementation. If they pass, you have a working module.

**This repository contains no implementations.** That's the point — the spec is the artifact. Implementations live in your own project, in your own language, generated however you like.

For the rules every spec and test file follows, see [`CONVENTIONS.md`](./CONVENTIONS.md). To add a new domain, see [`WRITING_A_DOMAIN.md`](./WRITING_A_DOMAIN.md).

## Layout

```
stdlib/
├── README.md
└── <domain>/                  # 15 domains: strings, collections, validation,
    ├── SPEC.md                # numbers, dates, paths, urls, iter, text,
    └── tests.yaml             # functional, hash, color, random, money, json_path
```
