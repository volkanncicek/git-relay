# Security

## Reporting a vulnerability

Report privately through GitHub: **[Security → Report a vulnerability](https://github.com/volkanncicek/git-relay/security/advisories/new)**. Please do not open a public issue for a security problem.

This is a small single-maintainer project. Expect an acknowledgement within a week, and a fix released as a new version once the problem is confirmed.

## What the tool touches

None of this is a vulnerability by itself, but it is what an audit would want stated plainly.

- **It runs `git` and `ssh`, and nothing else.** Every network operation is git over SSH or a single `ssh` call, made with the key named in the target's config and `IdentitiesOnly=yes`, so ssh-agent cannot offer an unrelated key. There are no HTTP requests and no telemetry.
- **It accepts a host key it has never seen.** Both hops run with `StrictHostKeyChecking=accept-new`: the first connection to a host records its key in `known_hosts`, and a later change of that key is refused. That first connection is trust on first use; if that matters for a host, add its key to `known_hosts` yourself before the first run.
- **It runs one remote command on the host:** `git -C <remote.path> config --get receive.denyCurrentBranch`, with the path shell-quoted. Everything else on the host goes through git's own receive and upload commands.
- **It keeps the two keys apart.** `remote.ssh_key` is only ever used toward the host and `local.git_ssh_key` only toward origin, so the host never receives a credential that can reach origin.
- **It writes refs, never files outside git.** On the workstation it rebases and pushes the configured branch. On the host it pushes that branch, which `receive.denyCurrentBranch=updateInstead` turns into a working-tree update that git itself refuses when the tree is dirty, and it force-updates `refs/remotes/origin/<branch>` to mirror origin. Untracked files are never touched on either side.
- **The config is read as TOML, never executed.** A target file holds paths and host names; nothing in it reaches a shell unquoted.
- **It creates one lock file per target** in `XDG_RUNTIME_DIR`, else `TEMP`, else `/tmp`, readable only by its owner.

## Scope

Anything that lets `git-relay` send a key to the wrong side, run a command on the host other than the one above, write outside the refs and working trees it is configured for, or create a commit is in scope. Behaviour of `git` and `ssh` themselves is not.
