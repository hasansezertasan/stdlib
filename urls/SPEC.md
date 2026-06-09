# `urls`

String operations on HTTP/HTTPS URLs. **No network access.** All functions are pure.

Scope: only `http://` and `https://` URLs. Other schemes are out of scope for v1.

## `parse(url: string) -> {scheme, host, port, path, query, fragment}`

Decompose a URL into its parts.

**Rules:**
1. `scheme` is `"http"` or `"https"` (lowercased).
2. `host` is the host portion, lowercased.
3. `port` is an integer, or `null` if not present.
4. `path` is everything from the first `/` after the host up to `?` or `#`, or `""` if absent. Includes the leading `/` when present.
5. `query` is everything between `?` and `#`, or `""`.
6. `fragment` is everything after `#`, or `""`.
7. Invalid input raises an error.

**Example:**
- `parse("https://Example.com:8443/path/x?a=1&b=2#top")` →
  `{scheme: "https", host: "example.com", port: 8443, path: "/path/x", query: "a=1&b=2", fragment: "top"}`

## `build(parts: {scheme, host, port?, path?, query?, fragment?}) -> string`

Inverse of `parse`. Construct a URL string from parts.

**Rules:**
1. `scheme` and `host` are required.
2. `port` is omitted if `null` or if it matches the scheme's default (80 for http, 443 for https).
3. `path` defaults to `""`.
4. `query` is appended with `?` prefix if non-empty.
5. `fragment` is appended with `#` prefix if non-empty.

## `add_query_param(url: string, key: string, value: string) -> string`

Return a new URL with `key=value` appended to the query string. If `key` already exists, append a second occurrence (do not replace — multi-valued params are valid).

Values are URL-encoded using percent-encoding (`%XX` for any character outside `[A-Za-z0-9._~-]`).

**Examples:**
- `add_query_param("https://x.com/a", "q", "hello world")` → `"https://x.com/a?q=hello%20world"`
- `add_query_param("https://x.com/a?p=1", "q", "2")` → `"https://x.com/a?p=1&q=2"`

## `strip_query(url: string) -> string`

Return `url` with the query string and fragment removed.

**Example:**
- `strip_query("https://x.com/a?p=1#top")` → `"https://x.com/a"`

## `url_join(base: string, ref: string) -> string`

Resolve `ref` against `base`, following RFC 3986 §5.3 semantics for the cases this domain supports.

**Rules (simplified):**
1. If `ref` is an absolute URL (starts with `http://` or `https://`), return `ref` unchanged.
2. If `ref` starts with `/`, replace `base`'s path with `ref`'s path.
3. If `ref` starts with `?`, replace `base`'s query (preserving path).
4. If `ref` starts with `#`, replace `base`'s fragment (preserving path+query).
5. Otherwise, resolve `ref` relative to the directory of `base`'s path (using `paths.normalize` semantics).

**Examples:**
- `url_join("https://x.com/a/b", "c")` → `"https://x.com/a/c"`
- `url_join("https://x.com/a/b/", "c")` → `"https://x.com/a/b/c"`
- `url_join("https://x.com/a", "/b")` → `"https://x.com/b"`
- `url_join("https://x.com/a", "https://y.com/")` → `"https://y.com/"`
