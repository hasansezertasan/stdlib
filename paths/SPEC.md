# `paths`

Pure string operations on POSIX-style paths. **No filesystem access.** Separator is always `/`.

This domain treats paths as text. It does not resolve symlinks, check existence, or care about the host OS. For real filesystem work, use your language's native APIs.

## `join(*parts: string) -> string`

Concatenate path segments with `/`, normalizing redundant separators.

**Rules:**
1. Empty parts are skipped.
2. A part beginning with `/` resets the result to that absolute path (matches POSIX `os.path.join` behavior).
3. Trailing `/` on intermediate parts is collapsed.

**Examples:**
- `join("a", "b", "c")` → `"a/b/c"`
- `join("a/", "b")` → `"a/b"`
- `join("a", "/b", "c")` → `"/b/c"`
- `join("")` → `""`

## `parent(path: string) -> string`

Return the directory portion of `path`.

**Rules:**
1. Returns everything up to (but not including) the final `/`.
2. If there is no `/`, returns `""`.
3. If the result would be `""` but `path` is absolute (begins with `/`), return `"/"`.
4. `parent("/")` → `"/"`.

**Examples:**
- `parent("/a/b/c")` → `"/a/b"`
- `parent("a/b")` → `"a"`
- `parent("file.txt")` → `""`
- `parent("/a")` → `"/"`

## `basename(path: string) -> string`

Return the final component of `path`.

**Rules:**
1. Returns everything after the final `/`.
2. If `path` ends with `/`, returns `""`.

**Examples:**
- `basename("/a/b/c.txt")` → `"c.txt"`
- `basename("file.txt")` → `"file.txt"`
- `basename("/a/b/")` → `""`

## `extension(path: string) -> string`

Return the file extension (everything from the last `.` of the basename onward), or `""` if none.

**Rules:**
1. Operates on `basename(path)`.
2. A leading dot does **not** count (`.bashrc` has no extension).
3. Returned extension includes the dot.

**Examples:**
- `extension("a/b/file.txt")` → `".txt"`
- `extension("archive.tar.gz")` → `".gz"`
- `extension(".bashrc")` → `""`
- `extension("README")` → `""`

## `normalize(path: string) -> string`

Collapse `.` and `..` segments and redundant separators.

**Rules:**
1. Multiple `/` collapse to one (but a leading `//` stays as `/`).
2. `.` segments are removed.
3. `..` segments pop the previous segment, except when at the root or already at the top of a relative path.
4. Trailing `/` is removed unless the result would be empty (then return `"."` for relative input, `"/"` for absolute).
5. An empty string normalizes to `"."`.

**Examples:**
- `normalize("a/./b/../c")` → `"a/c"`
- `normalize("/a/b/../../c")` → `"/c"`
- `normalize("a//b///c")` → `"a/b/c"`
- `normalize("../../a")` → `"../../a"`
- `normalize("")` → `"."`
