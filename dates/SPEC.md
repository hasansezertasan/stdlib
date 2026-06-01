# `dates`

Calendar helpers. All functions operate on **dates** (year-month-day, no time-of-day, no timezone) represented as ISO 8601 strings: `"YYYY-MM-DD"`.

This domain deliberately avoids time-of-day and timezones — those are a separate ghost library (`datetime`, not in v1).

## `parse_iso(s: string) -> {year, month, day}`

Parse `"YYYY-MM-DD"` into its three components.

**Rules:**
1. `s` must match the regex `^\d{4}-\d{2}-\d{2}$`.
2. `month` is in `1..12`, `day` is valid for the given month (including Feb 29 in leap years).
3. Invalid input raises an error.

## `add_days(date: string, days: integer) -> string`

Return the ISO date that is `days` days after `date`. `days` may be negative.

**Examples:**
- `add_days("2024-01-31", 1)` → `"2024-02-01"`
- `add_days("2024-03-01", -1)` → `"2024-02-29"` (leap year)
- `add_days("2024-12-31", 1)` → `"2025-01-01"`

## `days_between(start: string, end: string) -> integer`

Return `end - start` in days. Negative if `end` precedes `start`.

**Examples:**
- `days_between("2024-01-01", "2024-01-31")` → `30`
- `days_between("2024-01-31", "2024-01-01")` → `-30`
- `days_between("2024-01-01", "2024-01-01")` → `0`

## `is_weekend(date: string) -> boolean`

Return `true` if `date` falls on a Saturday or Sunday (ISO weekday 6 or 7).

## `format_relative(date: string, today: string) -> string`

Format `date` relative to `today` as a short human-readable string.

**Rules:**
1. Compute `d = days_between(today, date)`.
2. Apply the first matching rule:
   - `d == 0` → `"today"`
   - `d == 1` → `"tomorrow"`
   - `d == -1` → `"yesterday"`
   - `2 <= d <= 6` → `"in {d} days"`
   - `-6 <= d <= -2` → `"{|d|} days ago"`
   - `7 <= d <= 30` → `"in {d} days"`
   - `-30 <= d <= -7` → `"{|d|} days ago"`
   - `31 <= d <= 365` → `"in {round(d/30)} months"` (round half-away-from-zero)
   - `-365 <= d <= -31` → `"{round(|d|/30)} months ago"`
   - `d > 365` → `"in {round(d/365)} years"`
   - `d < -365` → `"{round(|d|/365)} years ago"`

The `today` parameter is explicit (no `now()` calls) so the function stays pure and testable.
