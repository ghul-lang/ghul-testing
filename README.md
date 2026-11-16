# ghul-testing

The test runner for [ghūl](https://ghul.dev) programs on the wasm and llvm
targets, and the few helpers tests are written with.

Tests are written with the language's own `assert`. The library's helpers
answer a value, a `bool` or the thing a test goes on to check, and the test
asserts on it.

## Writing tests

A test fails when an `assert` in it fails, and is in error when it throws
anything else. The helpers answer a value for the test to assert on:

| helper | answers |
| --- | --- |
| `caught[E](action)` | the `E` that `action` throws, or null where it throws nothing; an exception of another type propagates |
| `caught(action)` | the exception `action` throws, of any type, or null |
| `close_to(actual, expected, tolerance)` | whether `actual` is within `tolerance` of `expected` |
| `same_items(actual, expected)` | whether two sequences hold equal elements, compared with `=~`, in the same order |

```ghul
use Testing

let e = caught[Ghul.ArgumentException](() => parse("-"))
assert e?
assert e.message =~ "expected a digit"

assert close_to(average([1.0, 2.0]), 1.5, 0.000001)
assert same_items(sorted([3, 1, 2]), [1, 2, 3])
```

## The runner

`Testing.RUNNER.run(tests, arguments)` runs a list of `Testing.TEST_CASE`s,
one after another, and returns the exit status for the program to end with.
A `TEST_CASE` is a test's qualified name, where it is declared, and the code
that runs it.

```ghul
use Testing

entry(arguments: string[]) -> int =>
    RUNNER.run([TEST_CASE("Maths.adds", "maths.ghul: 3,1", () => (assert 1 + 1 == 2))], arguments)
```

`Testing.DISCOVERY.tests()` finds the tests instead: every method and global
function marked `@test()` in the program's own sources, ordered by name. An
instance test runs on a new instance of its class, made by the class's public
`init()` taking no arguments. On wasm and llvm, the program must be built to
describe its tests, with `"reflection": "roots"` (or `"all"`) under
`language` in `ghul-project.json`. On .NET the same calls run on .NET's own
reflection, so the sources compile into a .NET program too, with the test
attribute the compiler puts on each `@test()` method.

```ghul
namespace Maths is
    class Arithmetic is
        init() is si

        @test()
        adds() is
            assert 1 + 1 == 2
        si
    si
si
```

```ghul
use Testing

entry(arguments: string[]) -> int => RUNNER.run(DISCOVERY.tests(), arguments)
```

It takes these arguments:

| argument | effect |
| --- | --- |
| `--filter <pattern>` | runs only the tests whose name matches; `*` matches any run of characters and `?` any one; given more than once, a test matching any of them runs |
| `--shard <i>/<n>` | runs the i-th of n slices of the selected tests, from 1, so n processes run them all between them |
| `--list` | writes the selected tests' names instead of running them |
| `--format tap` | writes TAP version 14, the default |
| `--format text` | writes a line for each failure and a summary |

The TAP output is a plan and a line for each test. A failure is followed by a
YAML block holding its `message` and `at`, where the test is declared, and
`error: true` where the test threw something other than a failed `assert`.

The exit status is 0 when every test passed, 1 when any failed, and 2 when an
argument is not understood or no test is selected.

## Using it

A project compiles the library in by declaring it as a dependency in its
`ghul-project.json`:

```json
"dependencies": {
    "ghul-runtime": { "version": ">=42.0.0" },
    "ghul-testing": { "git": "https://github.com/ghul-lang/ghul-testing", "version": ">=0.1.0" }
}
```

## Building and testing

The tests are projects under `tests/`, each with the output it must print in
`run.expected`. With the .NET SDK and Node.js 22.5 or later:

```sh
dotnet tool restore
dotnet ghul-test --config wasm tests
dotnet ghul-test --config llvm tests
```

## License

[MIT](./LICENSE)
