# AI Agent Guide for ghul-testing

## Purpose

This guide is for AI agents and other automated contributors working on the ghūl test library in this repository.

## Background

- This repository is a ghūl source library, declared in a project's `ghul-project.json` like any other dependency. See [README.md](./README.md) for what it provides.
- The ghūl compiler, ghul-cli and the test tool are provided as .NET local tools. Run `dotnet tool restore` if needed.
- For a language tutorial and reference, see [`GHUL.md`](https://github.com/ghul-lang/ghul/blob/main/GHUL.md) in the `ghul` compiler repo, which owns it.

## Tests

- Each test is a project under `tests/` with a `ghul-project.json` that depends on this library by `path`, and a `run.expected` holding its exact output.
- Run them on both targets before raising a pull request: `dotnet ghul-test --config wasm tests` and `dotnet ghul-test --config llvm tests`.
- `dotnet-tests/` compiles the library and a few tests into one .NET program, which finds them through .NET's reflection. Run it with `dotnet run --project dotnet-tests` and compare its output with `dotnet-tests/run.expected`, as CI does.
- Use Unix line endings.

## Additional Notes

- Keep commits focused and well documented.
