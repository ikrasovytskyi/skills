---
name: agent-fleet
description: >
  Design a team of named, persistent AI agents that run a body of work — each one a
  separate Claude Code session with its own queue in StoryOS and a contract it can
  refuse work under. Works for engineering, marketing, research, ops: the method is
  domain-neutral and a profile supplies the vocabulary. Phase 1 of four: decide who
  exists, what each owns, what each must never touch, and who checks whom. Use when the
  user says "agent fleet", "agent team", "army of agents", "named agents", or wants a
  team of agents running a project or a function.
user-invokable: true
argument-hint: "<project> [engineering|marketing]"
metadata:
  version: "2.0.0"
  category: orchestration
  phase: "1 of 4 — design"
license: CC-BY-4.0
author: "Ievgen Krasovytskyi (linkedin.com/in/krasovytskyi, github.com/ikrasovytskyi)"
---

# Agent fleet — design the roster

This phase produces a roster document and nothing else. No databases, no
sessions. Provisioning is `agent-fleet-provision`, starting is
`agent-fleet-launch`, running is `agent-fleet-operate`.

Resist building here. A fleet provisioned before its boundaries are settled
gives you ten agents doing the same thing badly.

## First: pick a profile

The method below is domain-neutral. One file supplies the vocabulary and the
domain-specific mechanics. Read it now, alongside this:

- **engineering** → `references/profile-engineering.md`
- **marketing / content** → `references/profile-marketing.md`
- anything else → start from the closer of the two and write a new profile as
  you go; the core does not change

## What this method is

N named agents. Each is **a separate long-lived Claude Code session**, not a
subagent and not a script. Each has its own **queue** (records in StoryOS,
filtered to it), its own **contract** (a prompt file it re-reads every tick and
may refuse work under), and a **loop** that wakes it on a schedule.

They coordinate **only through records**. Not messages. A message is delivered
once and then gone; a record survives a closed laptop, a crashed session and a
week of nobody looking. The queue is also the audit trail — it is how you answer
"what happened overnight" without reading ten transcripts.

## The three kinds of agent

Every fleet needs all three. A fleet of only makers produces a lot, fast, wrong.

**SIGNAL — the ones who experience the work as its audience does.** In
engineering, operator personas using the product. In marketing, readers of the
target segment. They produce findings; they never write the tickets and never
make the thing. They need real exposure to the real artefact, and a context that
is **never reset** — accumulated history is the instrument.

**JUDGEMENT — the ones who decide.** Three roles, and the separation matters
more than the count:
- a **validator** who tries to *refute* findings and checks finished work
- a **writer** who turns validated findings into work items a maker can finish
  without asking a question
- an **owner** who is the only agent that can say no, set priority, and admit
  work to a lane

**MAKE — one per lane.** A lane is a set of things with exactly one owner. What
a "thing" is comes from the profile: files in engineering, surfaces in
marketing. Lanes must not overlap — measure it, do not assume it
(`references/lanes.md`).

## The five design rules

**1. Whose side are they on.** Write this first, in one sentence, at the top of
the shared contract. The version that worked: *"Every decision you make is for
the person who has to **use** this tomorrow, not whoever has to **make** it
today."* Every boundary is downstream of that sentence.

**2. Nobody checks their own work.** The same misunderstanding that produces the
mistake produces the check that misses it. Makers do not approve their own
output; the validator makes nothing.

**3. Every agent must be able to refuse.** Give each an explicit "you must
never" list and the standing to invoke it. Agents that can only comply cannot
protect anyone — and in practice the refusals are where the value showed up: an
agent that stopped rather than work around a rule found a defect in the rule.

**4. Boundaries are addresses, not vibes.** Name the things each agent owns and
the things that are never theirs. "Don't touch the API" is not a boundary;
`apps/api/**` is. "Stay off brand channels" is not a boundary; "LinkedIn company
page" is.

**5. Only create an agent whose queue will fill.** An agent with no inbound
reports idle forever and teaches you nothing. If you cannot name what routes
work to it and who routes it, you have a role, not an agent.

## Personality, and why it is not decoration

Give each agent a specific disposition — a skeptic, a gatekeeper, someone who
hates ceremony. A stated disposition is what makes an agent push back *in
character* rather than defer, and pushback is the whole point of having more
than one. "Defaults to refuted when uncertain" behaves measurably differently
from "checks carefully".

Keep it pointed at the audience. The disposition exists to protect the person on
the other end, not to be entertaining.

## Human accountability

Put a human on an item only where accountability is real — security, money,
external commitments, legal claims, and strategy only a founder can settle.
Everything else carries no human. A human name on an item nobody expects a human
to action trains everyone to ignore the field.

## Deliverable

Write `roster.md` (see `references/roster-template.md`) and stop. Per agent:
name, one-sentence identity, what they own, what they must never touch, who they
hand to, what fills their queue, and the disposition. Plus the shared contract:
whose side you are on, the honesty rules (`references/honesty-rules.md`), and
the handoff chain.

Then run `agent-fleet-provision`.

## References

- `references/profile-engineering.md` · `references/profile-marketing.md`
- `references/lanes.md` — measuring lane overlap before committing to it
- `references/honesty-rules.md` — contract clauses that earned their place
- `references/roster-template.md` — the roster shape and its sanity checks
