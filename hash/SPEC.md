# `hash`

Stable digest functions. **The output must be byte-identical across all conforming implementations and languages.** This is the strictest correctness contract in the entire stdlib — a Python implementation and a Rust implementation, given the same input, must produce the same string.

Strings are hashed as their **UTF-8 byte representation** unless stated otherwise.

## `md5_hex(s: string) -> string`

Compute the MD5 digest of `s` (UTF-8 encoded) and return it as a 32-character lowercase hex string.

**Note:** MD5 is not cryptographically secure. Use only for content addressing, deduplication, or compatibility with legacy systems. Use `sha256_hex` for anything else.

**Examples:**
- `md5_hex("")` → `"d41d8cd98f00b204e9800998ecf8427e"`
- `md5_hex("hello")` → `"5d41402abc4b2a76b9719d911017c592"`

## `sha256_hex(s: string) -> string`

Compute the SHA-256 digest of `s` (UTF-8 encoded) and return it as a 64-character lowercase hex string.

**Examples:**
- `sha256_hex("")` → `"e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855"`
- `sha256_hex("hello")` → `"2cf24dba5fb0a30e26e83b2ac5b9e29e1b161e5c1fa7425e73043362938b9824"`

## `fingerprint(s: string) -> string`

Return a short (8-character lowercase hex) fingerprint of `s`, suitable for human-readable IDs (e.g., short URLs, cache keys in logs).

**Definition (this is the normative spec; implementations must match exactly):**
1. Compute `sha256_hex(s)`.
2. Return the **first 8 characters** of that hex string.

This guarantees cross-language stability. The fingerprint is not cryptographically meaningful — collisions are expected in the millions-of-keys range.

**Examples:**
- `fingerprint("")` → `"e3b0c442"`
- `fingerprint("hello")` → `"2cf24dba"`
