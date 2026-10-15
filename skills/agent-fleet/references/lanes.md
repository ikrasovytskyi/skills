# Lanes — measure the overlap before you commit

(Examples below are from an engineering fleet. The measurement is the same for any
profile: map each unit of work to its lane and count things touched by more than
one. For marketing, substitute 'surface touched by more than one writer'.)

A lane is a set of things with exactly one owner — files in engineering, surfaces in marketing. The design succeeds or fails on
whether lanes actually separate, and you can measure that instead of guessing.

## The measurement

Over the last N merged PRs, map each PR to its lane (by branch prefix) and count
files touched by more than one lane:

```python
# for each merged PR: lane = branch prefix (web/ api/ mcp/ docs/)
# byfile[path] = set of lanes that touched it
shared = {f: ls for f, ls in byfile.items() if len(ls) > 1}
```

A healthy fleet returns **zero**. Measured on a real 10-agent fleet across 30
merged PRs: 0 of 67 files were touched by more than one lane.

## What that measurement actually tells you

Not "conflicts are impossible" — it tells you *where* conflicts will come from.
With clean lanes, cross-agent conflict is near zero and the real risk is **one
agent against itself**: the same agent touching the same file in two open PRs.

That risk is created by verification latency. Under auto-merge a PR lived for
minutes, so the next branch started from a main that already contained it. Once
a gate makes PRs live for hours, a second PR branches off a main missing the
first, and conflicts with its own author's earlier work.

Mitigations, both cheap:
- before claiming a new item, check your own open PRs for file overlap; stack on
  that branch and declare it, or take a different ticket
- a WIP ceiling (three open PRs) after which the agent holds idle — so a
  verification bottleneck shows up as a builder standing still rather than as
  branches quietly diverging

## Shared and generated files

The exception to clean lanes is anything generated or shared: schema packages,
generated SDKs, OpenAPI documents, migration directories. Give each exactly one
owner and say so explicitly, and treat "one migration in flight" as a hard rule
rather than a convention.


## The non-engineering version

The measurement still applies, and the failure it predicts still happens. In a
content fleet the equivalent of "two agents touched one file" is **two writers
touched one surface** — two people posting to the same channel, or two drafts
revising the same page. Count it the same way, from the last N published items.

The self-conflict version also survives: one writer with two drafts in flight
against the same page will contradict themselves once the first publishes. The
mitigation is identical — check your own unfinished work before starting more,
and hold at a WIP ceiling.
