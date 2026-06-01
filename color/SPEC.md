# `color`

Color math. Colors are passed as plain values — no Color class. Hex strings, RGB tuples (3 integers in `0..255`), HSL tuples (3 floats: `H ∈ [0, 360)`, `S, L ∈ [0, 1]`).

## `hex_to_rgb(hex: string) -> [r, g, b]`

Parse a CSS-style hex color into a 3-integer RGB tuple.

**Rules:**
1. Input may be `"#rgb"`, `"#rrggbb"`, `"rgb"`, or `"rrggbb"`.
2. Case-insensitive.
3. `"#rgb"` form is expanded as `#rrggbb` (each digit duplicated).
4. Returns `[r, g, b]` with each component in `0..255`.
5. Invalid input raises an error.

**Examples:**
- `hex_to_rgb("#ff0000")` → `[255, 0, 0]`
- `hex_to_rgb("#abc")` → `[170, 187, 204]`
- `hex_to_rgb("FFFFFF")` → `[255, 255, 255]`

## `rgb_to_hex(rgb: [r, g, b]) -> string`

Inverse of `hex_to_rgb`. Return a lowercase `"#rrggbb"` string.

**Rules:**
1. Components are clamped to `0..255` before formatting.
2. Output always uses 6 hex digits with leading `#`.

**Examples:**
- `rgb_to_hex([255, 0, 0])` → `"#ff0000"`
- `rgb_to_hex([170, 187, 204])` → `"#aabbcc"`

## `rgb_to_hsl(rgb: [r, g, b]) -> [h, s, l]`

Convert RGB to HSL.

**Rules:**
1. Input components are integers in `0..255`.
2. Output `h` is in `[0, 360)`, output `s` and `l` are floats in `[0, 1]`.
3. For pure gray (R == G == B), `h` is `0` and `s` is `0`.
4. Use the standard HSL formula: lightness is `(max + min) / 2`, saturation is calculated by the `(max - min)` over `min(max+min, 2-max-min)` rule.
5. Test fixtures are stated with `is_close(rel_tol=1e-3)` tolerance for floats.

**Examples (approximate):**
- `rgb_to_hsl([255, 0, 0])` → `[0.0, 1.0, 0.5]`
- `rgb_to_hsl([128, 128, 128])` → `[0.0, 0.0, ~0.502]`
- `rgb_to_hsl([0, 255, 0])` → `[120.0, 1.0, 0.5]`

## `lighten(hex: string, amount: number) -> string`

Lighten a color by `amount` (a number in `[-1, 1]`).

**Rules:**
1. Convert input hex to HSL.
2. New `l' = clamp(l + amount, 0, 1)`.
3. Convert back to RGB, then to hex (lowercase `"#rrggbb"`).
4. Negative `amount` darkens.

**Examples:**
- `lighten("#808080", 0.2)` → `"#b3b3b3"` (approx)
- `lighten("#ff0000", -0.25)` → `"#800000"` (approx)
- `lighten("#000000", 1.0)` → `"#ffffff"`
