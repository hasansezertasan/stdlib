# `validation`

Predicates that return `true` or `false`. All functions are pure and never raise on string input.

These are **pragmatic, not RFC-perfect**. They catch obvious garbage; they are not security boundaries.

## `is_email(s: string) -> boolean`

Return `true` if `s` looks like an email address.

**Rules:**
1. Contains exactly one `@`.
2. The local part (before `@`) is non-empty.
3. The domain part (after `@`) contains at least one `.`.
4. Every dot-separated label in the domain is non-empty.
5. No whitespace anywhere in `s`.

**Examples:**
- `is_email("a@b.co")` → `true`
- `is_email("no-at-sign")` → `false`
- `is_email("a@b")` → `false` (no dot in domain)
- `is_email("a@.b")` → `false` (empty label)

## `is_url(s: string) -> boolean`

Return `true` if `s` looks like an `http` or `https` URL.

**Rules:**
1. Starts with `http://` or `https://` (case-insensitive scheme).
2. The host portion (between `://` and the first `/`, `?`, `#`, or end-of-string) is non-empty.
3. No whitespace anywhere in `s`.

**Examples:**
- `is_url("https://example.com")` → `true`
- `is_url("HTTP://example.com/path?q=1")` → `true`
- `is_url("ftp://example.com")` → `false`
- `is_url("https://")` → `false`

## `is_uuid(s: string) -> boolean`

Return `true` if `s` is a canonical UUID string.

**Rules:**
1. Length is exactly 36 characters.
2. Matches the pattern `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` where each `x` is a hex digit `[0-9a-fA-F]`.
3. The four `-` separators are at positions 8, 13, 18, and 23.

This accepts any UUID variant or version — it does not validate the version nibble.

**Examples:**
- `is_uuid("550e8400-e29b-41d4-a716-446655440000")` → `true`
- `is_uuid("550E8400-E29B-41D4-A716-446655440000")` → `true`
- `is_uuid("550e8400e29b41d4a716446655440000")` → `false` (no dashes)
- `is_uuid("not-a-uuid")` → `false`
