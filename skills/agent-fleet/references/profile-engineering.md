# Profile: engineering

Vocabulary and mechanics for a fleet that ships software.

## Vocabulary

| core term | here |
|---|---|
| lane | a set of file paths with one owner (`apps/web/**`) |
| workspace | a git worktree, one per agent, written to by nobody else |
| deliverable | a pull request |
| land it | merge |
| mechanical checks | CI: lint, typecheck, tests, build |
| environments | local instance, staging, production |

## Roster that worked (10 agents)

- **Signal (2)** — an agency operator and a solo operator, each with a persistent
  local instance they never reset, running scripted UAT scenarios
- **Judgement (3)** — validator, product manager, product owner
- **Make (5)** — web, api, mcp, docs, and the marketing site (a separate repo)

Grew to add a **designer** once "it's all too complex" became the most common
client complaint. Two rules made that lane work:
- **The designer's deliverable is something the founder can see**: a clickable
  before/after artifact in the product's own tokens, not a written spec.
  Nothing is built from it until the founder approves it.
- **Mechanical CSS bugs go to the web maker, not the designer.** A ticket with
  no decision left in it is a build ticket, whoever filed it.

## Mechanics this profile adds

**Workspaces are git worktrees.** One agent, one worktree, forever. Two sessions
in one worktree is dangerous: one renames a branch under the other and the next
commit strands on a merged branch.

**The folder guard.** Agents that write files must verify `pwd` matches their
worktree and stop otherwise. Read-only roles must NOT have this rule — their
working directory may reset between calls and the check can never pass, which
turns a guard into a permanent silent stop. If a workspace is a symlink, accept
both the logical and resolved paths.

**The gate is the merge.** A maker opens a PR, records it on the ticket with
what it did and did not verify, sets the validator as next agent, and moves on.
Nothing reaches the trunk without the validator.

**Check the repo's real merge mechanics before writing instructions.** The
legacy branch-protection API returns empty for repos governed by rulesets. A
merge queue owns the merge strategy, so `--squash` is refused and `--auto` is the
only path — meaning a blanket ban on `--auto` for makers must carve out the
validator, or the gate cannot merge at all.

**Conflicts.** Clean lanes make cross-agent conflict near zero; the real risk is
one agent against itself, because a gate makes PRs live for hours instead of
minutes. Mitigate with a self-overlap check before claiming, and a WIP ceiling.

**Local instances.** Signal agents need one permanently; the validator needs one
on demand and should stop it after. Unique ports per agent. Build shared
workspace packages first or the API dies on boot with hundreds of type errors
and nothing warns you.

## Cadence

Drain until empty. Shipping the whole queue as fast as it can be verified is
correct here.
