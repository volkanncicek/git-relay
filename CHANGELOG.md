# Changelog

All notable changes to this project are documented here. The format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/), and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

## [0.1.1] - 2026-10-22

### Changed

- **The host's `origin/<branch>` now follows origin.** The host never fetches from origin, so its remote-tracking ref stayed at the day it was cloned and `git status` there reported the branch ever further ahead. The push to the host now also force-updates `refs/remotes/origin/<branch>` from the workstation's copy, so the host's status is truthful again after every run.
- **Releases carry the built wheel and sdist.** Each GitHub release is now created by CI from a verified tag, with this file's section as its notes.

### Fixed

- **A commit message with an emoji no longer crashes the run on Windows.** Output from `git` and `ssh` was decoded with the locale code page, which on Windows cannot decode every UTF-8 byte git writes, so the run died with a traceback instead of relaying the commit. Output is now read as UTF-8 on every platform.
- **The per-target lock now excludes a second run on Windows.** The lock file was named after the process id there, so every run locked a file of its own and two runs for the same target could proceed at once. It is now named after the user, as it already was by uid on Linux and macOS.

## [0.1.0] - 2026-07-28

First release.

### Added

- **Two-hop relay over plain git.** A run fetches from the host and rebases, fetches from origin and rebases, then pushes to origin and to the host. Nothing is rsynced and `.git/` is never copied; the tool moves commits and never creates one.
- **One TOML file per target** under `~/.config/git-relay/`, with separate SSH keys for the host and for origin, plus `--list`, `--config` and `--dry-run`.
- **Remote pre-flight.** Refuses unless the host has the expected branch checked out, because `receive.denyCurrentBranch=updateInstead` only updates the working tree of the checked-out branch. Reads that setting over SSH rather than trusting `git push --dry-run`, which reports success against a host that would refuse the real push.
- **Local pre-flight.** Names a paused rebase instead of mistaking it for a dirty tree, refuses a dirty or detached clone, and lists untracked files that will not propagate.
- **Per-target lock** held on a file descriptor, so a crashed run leaves nothing to clean up.

[Unreleased]: https://github.com/volkanncicek/git-relay/compare/v0.1.1...HEAD
[0.1.1]: https://github.com/volkanncicek/git-relay/compare/v0.1.0...v0.1.1
[0.1.0]: https://github.com/volkanncicek/git-relay/releases/tag/v0.1.0
