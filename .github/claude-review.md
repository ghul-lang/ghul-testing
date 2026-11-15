# Cloud code review brief

What this repository is, and what to watch for in it. Everything else - what PR
context is available, how to post a review, what makes a finding worth raising,
comment hygiene, PR-description shape, the versioning mechanism - comes from the
review workflow's runtime notes. Don't restate it here: this file is read first,
so a stale copy would silently override the current text.

Not loaded by local Claude Code; only the cloud reviewer reads this.

## What this repo is

`ghul-testing` is a ghūl source library: the runner that runs a project's
tests and the few helpers tests are written with. A project compiles it in by
declaring it in the `dependencies` of its `ghul-project.json`, and `ghul test`
adds it to a test build, so an ordinary program never carries it. Releases are
the repository's `vX.Y.Z` tags, which a dependency's version range resolves
against.

Tests are written with the language's own `assert`. The library has no
assertion functions: its helpers answer a value (a `bool`, or what a test
asserts on) and the test asserts on it.

`README.md` is the documentation and the contract.

## What to watch for here

- **Every target.** The library compiles for wasm and llvm, and its tests run
  on both. Nothing that only one target has: no .NET API that the core library
  lacks, no threads, no reflection.
- **The runner's output is read by tools.** The TAP it writes, its argument
  syntax and its exit statuses (0 all passed, 1 a test failed, 2 a usage error
  or no test selected) are read by `ghul test` and by CI. A change to any of them
  is a compatibility change.
- **Helpers answer, they don't throw.** A new helper returns a value for the
  test to assert on. One that throws on failure is a finding.
- **The README stays true.** A change in behavior with no matching change to
  the README is a finding, and so is README text describing something the
  diff does not do.
- **Tests run on every target.** A new feature wants a test under `tests/`
  that runs on wasm and llvm.

## Versioning

Major means an existing test program stops compiling, or the runner's output,
arguments or exit statuses change in a way a reader of them would notice.
Minor means additions: new helpers, new arguments, new output that existing
readers ignore. Patch is a fix that changes nothing a correct test program or
a reader of the runner's output can observe.
