# `money`

Currency string formatting and parsing. Money is represented internally as **minor units** — integer cents, not floats. This avoids floating-point rounding bugs that turn `$0.10 + $0.20` into `$0.30000000000000004`.

Currency metadata (decimal places, symbol) is keyed by ISO 4217 code. v1 supports a fixed table:

| Code | Symbol | Minor units |
| --- | --- | --- |
| `USD` | `$` | 2 |
| `EUR` | `€` | 2 |
| `GBP` | `£` | 2 |
| `JPY` | `¥` | 0 |
| `TRY` | `₺` | 2 |

Unknown currency codes raise an error.

## `format_currency(minor: integer, code: string) -> string`

Format an integer minor-unit amount as a human-readable currency string.

**Rules:**
1. Look up `code` in the currency table.
2. Convert `minor` to its major-unit value as a fixed-point decimal string with the table's decimal places.
3. Apply thousands separators (`,`) to the integer part. Decimal separator is `.`.
4. Prefix the result with the currency symbol.
5. Negative amounts are formatted as `-{symbol}{amount}` (sign before symbol).

**Examples:**
- `format_currency(12345, "USD")` → `"$123.45"`
- `format_currency(1234567, "USD")` → `"$12,345.67"`
- `format_currency(1234, "JPY")` → `"¥1,234"` (no decimals)
- `format_currency(-500, "EUR")` → `"-€5.00"`

## `parse_currency(s: string, code: string) -> integer`

Inverse of `format_currency`. Parse a formatted string back into minor units.

**Rules:**
1. The currency symbol (and optional leading `-`) must match `code`.
2. Thousands separators (`,`) are optional and ignored.
3. The number of decimal places must match the currency's metadata. (E.g., `"$1.5"` for USD raises — must be `"$1.50"`.)
4. Returns an integer.

**Examples:**
- `parse_currency("$123.45", "USD")` → `12345`
- `parse_currency("$12,345.67", "USD")` → `1234567`
- `parse_currency("¥1234", "JPY")` → `1234`
- `parse_currency("-€5.00", "EUR")` → `-500`

## `round_to_cents(major: number, code: string) -> integer`

Convert a major-unit float to minor units, rounding half-away-from-zero.

**Rules:**
1. Look up the decimal places `d` for `code`.
2. Returns `round(major * 10**d)` with half-away-from-zero rounding.

**Examples:**
- `round_to_cents(1.234, "USD")` → `123`
- `round_to_cents(1.235, "USD")` → `124`
- `round_to_cents(0.005, "USD")` → `1`
- `round_to_cents(1234.5, "JPY")` → `1235`
