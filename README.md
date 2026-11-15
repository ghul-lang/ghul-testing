# ghul-testing

The test runner for [ghūl](https://ghul.dev) programs on the wasm and llvm
targets, and the few helpers tests are written with.

Tests are written with the language's own `assert`. The library's helpers
answer a value, a `bool` or the thing a test goes on to check, and the test
asserts on it.

This repository is being set up. The runner and the helpers arrive in the
releases that follow.

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
