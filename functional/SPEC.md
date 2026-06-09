# `functional`

Function combinators. This domain is **semantic, not syntactic** — each entry describes the contract the resulting function must satisfy, not its signature in any particular language.

The tests in `tests.yaml` are written in terms of *observable behavior* after applying the combinator, since "a function" is not a YAML-encodable value.

## `compose(*fns: callable) -> callable`

Return a new function `g` such that `g(x) = fns[0](fns[1](...fns[-1](x)))`. Composition is **right-to-left**.

**Rules:**
1. `compose()` with zero functions returns the identity function: `g(x) = x`.
2. `compose(f)` returns `f` (or a wrapper with equivalent behavior).
3. All input functions are unary (take one argument) for v1.

**Example:**
- `compose(double, increment)(3)` → `8` (because `increment(3) = 4`, then `double(4) = 8`)

## `pipe(*fns: callable) -> callable`

Return a new function `g` such that `g(x) = fns[-1](...fns[1](fns[0](x)))`. Application is **left-to-right** — the mirror of `compose`.

**Example:**
- `pipe(increment, double)(3)` → `8`

`pipe(a, b, c)` is equivalent to `compose(c, b, a)`.

## `memoize(fn: callable) -> callable`

Return a wrapped function that caches results by its arguments.

**Rules:**
1. The first call with a given argument computes and stores the result.
2. Subsequent calls with the same argument return the cached value without invoking `fn` again.
3. Cache key equality is by value (two equal-but-distinct argument values are the same key).
4. Cache is unbounded for v1 (no eviction policy).
5. The wrapped function must produce the same output as `fn` for any input.

**Observable contract:** if `fn` is called via `memoize(fn)` twice with equal arguments, `fn` itself is invoked at most once. This is what the tests verify (via a call counter).

## `curry(fn: callable, arity: integer) -> callable`

Convert an `arity`-ary function into a chain of unary functions.

**Rules:**
1. `arity` must be `>= 1`. `arity = 1` is a no-op.
2. `curry(fn, n)(a)(b)...(z)` (with `n` arguments) equals `fn(a, b, ..., z)`.
3. Partial application is allowed: each intermediate call returns a function expecting the remaining arguments.

**Example:**
- `curry(add, 2)(3)(4)` → `7` (where `add(a, b) = a + b`)
