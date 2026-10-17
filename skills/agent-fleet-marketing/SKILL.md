---
name: agent-fleet-marketing
description: >
  Stand up a team of named AI agents to run marketing and content — writers per channel,
  an editor, a fact-checker, an owner who decides what publishes, and readers who tell
  you whether it lands. Each is a persistent session with its own queue in StoryOS, a
  publishing gate, and a cadence so it does not dump a month of posts in an afternoon.
  Use when the user says "marketing agents", "content team of agents", "agents to run
  our blog / LinkedIn / newsletter", "editorial fleet", or wants the agent-team method
  applied to marketing rather than engineering.
user-invokable: true
argument-hint: "<brand or project name>"
metadata:
  version: "1.0.0"
  category: orchestration
license: CC-BY-4.0 (see LICENSE). Author: Ievgen Krasovytskyi, linkedin.com/in/krasovytskyi, github.com/ikrasovytskyi
---

# Marketing agent fleet — entry point

This skill contains no method of its own. The method is shared with every other
fleet; only the vocabulary and two mechanics differ, and duplicating it here
would be the exact defect the method warns about.

## Do this

1. Read **`agent-fleet/references/profile-marketing.md`** — the vocabulary, the
   roster sketch, and the two things that genuinely differ.
2. Run **`agent-fleet`** for the design phase, with the marketing profile in
   hand. It produces `roster.md` and nothing else.
3. Then `agent-fleet-provision` → `agent-fleet-launch` → `agent-fleet-operate`.

## What is different here, in one screen

**Lanes are surfaces, not files.** LinkedIn, blog, newsletter, paid, site copy —
one owner each. A writer whose lane is the blog does not post to LinkedIn, ever.

**Most agents need no folder.** They write records and documents. Do not give
them a workspace "for consistency": an unsatisfiable folder rule is a permanent
silent stop, and in a content fleet it would hit almost everyone.

**The gate is publication, and it matters more than a merge.** Publishing is less
reversible: a wrong claim reaches the audience instantly and gets screenshotted.

**Claims are the security bugs of marketing.** Never publish a claim about the
product without checking it is true — not from memory, not from an older page.
If you cannot establish a sentence is true, ship without it and say why. A real
fleet found its own pricing page claiming a capability the product did not have.

**Cadence replaces draining.** This is the one architectural difference. An
engineering queue is worked until empty; a content queue worked until empty
produces twelve posts in an afternoon and nothing for a week. Every surface
carries a cap and a minimum gap, and an agent at its cap stops and says so.

**Scheduled is a state.** A scheduled post is neither done nor outstanding. Give
it its own state and a date, or agents re-pick work already on the calendar.

## The role most often skipped

The **reader** — a signal agent from the target segment whose only job is to read
finished work as the audience and report what they actually took away. It is the
role that catches the piece which is accurate, on-brand, well-structured, and
persuades nobody. A fleet without one optimises everything except whether it
worked.
