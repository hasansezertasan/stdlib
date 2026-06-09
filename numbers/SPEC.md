# `numbers`

Numeric helpers. All functions are pure. Inputs and outputs are real numbers (integers or floats); implementations may use whatever native numeric type is idiomatic.

## `clamp(value: number, lo: number, hi: number) -> number`

Constrain `value` to the closed interval `[lo, hi]`.

**Rules:**
1. If `value < lo`, return `lo`.
2. If `value > hi`, return `hi`.
3. Otherwise return `value`.
4. If `lo > hi`, raise an error.

## `round_to(value: number, step: number) -> number`

Round `value` to the nearest multiple of `step`. Halfway cases round away from zero.

**Rules:**
1. `step` must be `> 0`.
2. `round_to(value, step) = round(value / step) * step` with half-away-from-zero rounding.

**Examples:**
- `round_to(7, 5)` → `5`
- `round_to(7.5, 5)` → `10`
- `round_to(0.27, 0.05)` → `0.25`

## `lerp(a: number, b: number, t: number) -> number`

Linear interpolation between `a` and `b` by parameter `t`.

**Rules:**
1. Returns `a + (b - a) * t`.
2. `t` is **not** clamped — `t < 0` extrapolates below `a`, `t > 1` extrapolates beyond `b`.

## `is_close(a: number, b: number, rel_tol: number = 1e-9, abs_tol: number = 0.0) -> boolean`

Test whether two numbers are approximately equal.

**Rules:**
1. Returns `true` iff `|a - b| <= max(rel_tol * max(|a|, |b|), abs_tol)`.
2. `is_close(x, x)` is always `true` for any finite `x`.
3. Behavior with `NaN` or infinities is undefined; implementations may return `false`.

## `sign(value: number) -> integer`

Return the sign of `value`.

**Rules:**
1. Returns `1` if `value > 0`.
2. Returns `-1` if `value < 0`.
3. Returns `0` if `value == 0`.
