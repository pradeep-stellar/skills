---
name: command-line-prog-creator
description: Create a new command-line program (CLI tool) from scratch, or add commands to an existing one. Use when the user asks to "build a CLI", "write a command-line tool", "make a script I can run from the terminal", "add a subcommand", or wants argument parsing, --help output, exit codes, config files, shell completions, or packaging for a CLI in Python, Node/TypeScript, Go, or Rust.
---

# Command-Line Program Creator

Build CLIs that behave like good Unix citizens: predictable, scriptable, and self-documenting.

## 1. Clarify before coding

Settle these (ask only what can't be inferred from the request or the repo):

- **Purpose**: one sentence — what does the tool do, for whom?
- **Language**: follow the existing repo if there is one. Otherwise default by need:
  - Quick/glue tool → Python (`argparse` or `click`/`typer`)
  - Distributed to JS users → TypeScript (`commander`, `citty`, or `yargs`)
  - Single static binary → Go (`cobra`) or Rust (`clap`)
- **Shape**: single command, or `tool <subcommand>` style?
- **Inputs/outputs**: args, flags, stdin, files; human output vs. machine output (`--json`).

## 2. Design the interface first

Write the usage text before the implementation, and show it to the user if the surface is non-trivial.

- Name: short, lowercase, no clash with common binaries (`which <name>`).
- Positional args for the primary object(s); flags for modifiers. Every flag gets a long form (`--output`); add short forms (`-o`) only for frequent ones.
- Standard flags: `-h/--help`, `--version`, and where relevant `-v/--verbose`, `-q/--quiet`, `--json`, `-n/--dry-run`, `-y/--yes`, `--no-color`.
- Subcommands: verb-noun or noun-verb, consistently. Each has its own `--help`.
- Destructive actions: require confirmation or `--yes`; support `--dry-run`.
- Read from stdin when an input path is `-` or when stdin is piped and no input is given.

## 3. Behavior rules

- **stdout is for data, stderr is for everything else** (logs, progress, errors, prompts). Never mix them.
- **Exit codes**: `0` success, `1` general failure, `2` usage error (bad flags/args). Add specific codes only if documented.
- **Errors**: one clear line — what failed, why, and how to fix it. No stack traces unless `--verbose`/`--debug`.
- **Color/progress**: only when stdout/stderr is a TTY; honor `NO_COLOR` and `--no-color`.
- **Machine output**: `--json` emits stable, documented JSON only on stdout.
- **Signals**: exit cleanly on SIGINT (130) and SIGPIPE (e.g. `tool | head`) without tracebacks.
- **Idempotence**: re-running should be safe wherever possible.
- **Config precedence**: flags > environment variables > project config file > user config (`$XDG_CONFIG_HOME/<tool>/`) > defaults. Env vars are prefixed with the tool name (`MYTOOL_TOKEN`). Never take secrets as flags; use env vars or files.

## 4. Help text

`--help` must include: one-line summary, usage line, argument/flag list with defaults, and 2–3 realistic examples. Keep it under ~40 lines per command.

## 5. Project layout

Keep parsing thin and logic testable:

```
<tool>/
  README.md          # install, quickstart, command reference
  src/<tool>/
    cli.*            # arg parsing + wiring only
    core.*           # pure logic, no I/O assumptions
  tests/
```

Entry point must be exposed as a real command (`[project.scripts]` in pyproject, `bin` in package.json, `main` package in Go, `[[bin]]` in Cargo).

## 6. Testing

- Unit-test `core` directly.
- Test the CLI end-to-end by invoking it as a subprocess: assert on exit code, stdout, and stderr separately.
- Cover: `--help`, `--version`, a bad flag (exit 2), missing input, stdin piping, `--json` validity.

## 7. Finish

1. Run the tool yourself: `--help`, a happy path, and an error path. Report actual output.
2. Run the tests and linter; report results honestly.
3. Write a README with install instructions and a command reference.
4. Offer (don't assume) extras: shell completions, man page, CI, release/packaging (PyPI, npm, Homebrew tap, GitHub release binaries).

## Don'ts

- Don't print progress or banners to stdout.
- Don't prompt interactively when stdin isn't a TTY — fail with a clear message instead.
- Don't add dependencies for what the standard library handles.
- Don't invent flags the user didn't need; keep the surface small.
