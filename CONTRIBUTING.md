# Contributing

Thanks for looking. This is a one-person project: bug reports and small fixes are easy to say yes to, larger changes need a conversation before you spend an evening on them, and I review when I get a free evening rather than on any schedule.

For a clear bug fix or a typo, just open a pull request. For a new feature or a change to how a run behaves, open an issue first. Several things that look like gaps are deliberate (untracked files not propagating, no automatic commits, no rsync mode), so a short exchange up front usually saves the larger half of the work.

## Getting set up

The project uses [uv](https://docs.astral.sh/uv/) and needs Python 3.11 or later, plus `git` on `PATH`.

```
uv sync
uv run git-relay --help
```

## Running the checks

CI runs exactly these four, on Windows and on Ubuntu:

```
uv run ruff check .
uv run ruff format --check .
uv run ty check
uv run pytest
```

The tests build real git repositories in a temporary directory and relay between them, with local paths standing in for SSH URLs, so they need no SSH server and cannot touch your own repositories or keys. Every test starts dozens of `git` processes, so the suite takes seconds on Linux and noticeably longer on Windows, where process start-up is slow.

## Guards that must not be weakened

Every guard in `git_relay.py` exists because its failure was reproduced first, and the test suite keeps those reproductions. Before changing one, read the comment above it and the test that names it. `AGENTS.md` lists them in one place.

A new guard follows the same order: a test that shows the failure against real repositories, then the fix, then a comment naming the failure that was reproduced.

## Opening a pull request

One change per pull request, and a description of what it does rather than which files it touches. Run the four checks locally first. If the change is user-visible, add a line under `## [Unreleased]` in `CHANGELOG.md`.
