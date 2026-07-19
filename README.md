# brix

Assertions for Emela's unit tests (spec 0040), distributed as a **Pome** (spec
0032).

Named after **°Brix** — the scale a grower uses to measure a fruit's sugar
content: how you judge the quality of a Pome.

- **Source path:** `github.com/emela-lang/brix`
- **Import root:** `brix` (the source-path leaf; no `[pome].module` override)
- **Requires:** emela ≥ 0.6 (`@test` and `emela test`, specs 0039/0040)

## Usage

Add brix as a development-time dependency (spec 0040 D1). It resolves for
`check` / `lint` / LSP / `test`, and the compiler rejects any build artifact
that would reach it (D4) — brix never becomes a runtime dependency of your
Pome:

```console
$ emela pome add --dev github:emela-lang/brix
```

Write tests next to the code they check (spec 0040 T7) — same file, so private
functions are testable by bare name — and run them:

```emela
module rle

import brix.assert

fn run_length(s: String) -> Int uses {} {
    ...
}

@test
fn empty_input_has_zero_runs() -> Unit uses {} {
    assert.eq(run_length(""), 0)
}
```

```console
$ emela test
running 1 test
test rle.empty_input_has_zero_runs ... ok

test result: ok. 1 passed; 0 failed
```

A `@test` body is an implicit-try scope (spec 0040 T3): a failed assertion
throws, the throw propagates as the test's failure at that point, and no `?`
is needed. The runner knows nothing about this library beyond "an uncaught
throw fails the test" (B1/C4); the `Show` impl on `AssertError` is what renders
the failure detail (C7):

```text
---- rle.empty_input_has_zero_runs ----
threw AssertError: assertion failed: expected `0`, got `1`
```

## API (module `brix.assert`)

| Function | Fails when |
|---|---|
| `eq<T: Eq + Show>(actual, expected)` | the values differ |
| `ne<T: Eq + Show>(actual, unexpected)` | the values are equal |
| `ok(condition)` | the condition is false |
| `fail(message)` | always |

Every assertion returns `Unit` and `throws AssertError`; brix is pure Emela
with no compiler privileges, so alternative assertion libraries compose the
same way.

## Testing

brix tests itself with its own assertions — the `@test` functions live beside
the API in `src/assert.emel`:

```console
$ emela test
```
