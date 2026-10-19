# brix

Test helpers for Emela, distributed as a Pome: checks for Option values and property-based testing
with shrinking.

Named after **°Brix**, the scale a grower uses to measure a fruit's sugar content: how you judge
the quality of a Pome.

- Source path: `github.com/emela-lang/brix`
- Root name: `Brix`
- Requires: emela 0.23.0 or newer

## Usage

Add brix as a dependency used only by the tests (`[dev-dependencies]`):

```console
$ emela pome add --dev github:emela-lang/brix
```

A `[dev-dependencies]` Pome can only be imported from the test root `test/`, which `emela test`
and `emela check` read and `emela build` does not. Put the tests that use brix under `test/`:

```emela
# test/sort_test.emel
import Brix.Pbt.Gen
import Brix.Pbt.Property
import Sort

@test
fn sort_keeps_the_length() {
  Property.check(Gen.list(Gen.int()), fn(xs) {
    assert List.length(Sort.sort(xs)) == List.length(xs)
  })
}
```

```console
$ emela test
running 1 test
test SortTest.sort_keeps_the_length ... ok

test result: ok. 1 passed; 0 failed; 0 filtered out
```

A failing property is shrunk to a smaller counterexample, and the test fails with the seed to
reproduce it:

```text
defect: Property failed (seed=20261013 case=9 case_seed=292983060 size=9)
original: [-854397770, -2143511092, -2141573835]
counterexample: [0, 0, 0]
shrink_attempts=9 limit=1000
defect: assertion failed: List.length(xs) < 3
  left: 3
 right: 3
```

(for `assert List.length(xs) < 3`)

## Modules

| Module | Contents |
|---|---|
| `Brix.Assert` | `some(value)` returns the contents of `Some` and fails on `None`; `none(value)` fails on `Some`, showing its contents |
| `Brix.Pbt.Gen` | `Gen[A]` generators and their shrink trees: `int`, `int_between`, `constant`, `map`, `pair`, `list`, and `sample` to run one |
| `Brix.Pbt.Property` | `check(gen, f)` runs `f` on generated values; `check_with(gen, config, f)` with a `Config` (`seed`, `cases`, `max_size`, `shrink_limit`) |

Generators are pure values driven by a seed (xorshift32), so a run is the same on every target and
every time. An error or a defect in `f` fails the case; a failing generator is reported apart from
a failing property.

## Testing

brix tests itself with the tests under `test/`:

```console
$ emela test
$ emela test --target c
```
