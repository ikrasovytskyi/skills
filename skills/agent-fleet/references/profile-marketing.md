# Profile: marketing / content

Vocabulary and mechanics for a fleet that publishes.

## Vocabulary

| core term | here |
|---|---|
| lane | a **surface** with one owner: LinkedIn, blog, newsletter, paid, site copy |
| workspace | usually none — most agents write records and documents, not files |
| deliverable | a draft |
| land it | publish, or schedule |
| mechanical checks | links resolve, every claim sourced, brand terms, legal review |
| environments | draft → scheduled → published |

## Roster sketch

- **Signal (1–2)** — readers of the target segment. They read finished and
  near-finished work *as the audience* and say whether it lands, what it assumes,
  and where it loses them. This is the role most often skipped and it is the one
  that catches the piece that is technically fine and persuades nobody.
- **Judgement (3)** — a **fact-checker** (refutes claims, owns truth), an
  **editor** (turns a validated insight into a brief a writer can execute
  without asking), and an **owner** (says no, sets priority, decides what
  publishes and when).
- **Make (per surface)** — one per channel. A writer whose lane is the blog does
  not post to LinkedIn, ever.

## Mechanics this profile changes

**No worktrees, mostly no folder guard.** Agents write records and documents.
The guard applies only to anyone touching a repo (a website agent). Do not
inherit it by default — an unsatisfiable guard is a silent daily stop.

**The gate is publication, and it matters more than a merge.** Publishing is
less reversible: a wrong claim reaches the audience instantly and gets
screenshotted. Nothing publishes without the fact-checker and the owner.

**Claims are the security bugs of marketing.** The rule that survives everything
else: *never publish a claim about the product without checking it is true* —
not from memory, not from an older page, not from a headline. Check the source,
the documentation, or ask. If you cannot establish a sentence is true, ship
without it and say why on the record. A real fleet found its own pricing page
claiming a capability the product did not have.

**Voice is a shared contract, and it is fragile.** Engineering has conventions a
linter enforces; voice has nothing mechanical. Put the voice rules in the shared
contract, with examples of what it is *not*, and have the editor enforce them at
the gate rather than hoping each writer absorbed them.

**Findings about the product are not yours to fix.** If the copy is wrong,
change the copy. If the *product* is wrong, that is a finding for the other
fleet — never an edit.

## Cadence — the one real architectural difference

**Do not drain.** A content queue emptied as fast as possible produces twelve
posts in an afternoon and nothing for a week. Each surface carries a cadence
limit, and the agent's tick respects it:

```
per surface: max N published per day/week, minimum gap between items
if the cap is reached: stop, say "cadence reached for <surface> — N scheduled
through <date>", end the tick idle
```

That turns cadence into something visible rather than something an agent
improvises. It also means "queue depth" alone is a bad health signal here — read
it together with what is scheduled and when.

**Scheduling is state, not completion.** A scheduled post is neither done nor
outstanding. Give the queue an explicit `Scheduled` state with the date, or
agents will re-pick items already committed to the calendar.

## What carries over unchanged

Everything else: roster design, the honesty rules, nobody-checks-their-own-work,
one owner per lane, run rows, the failure catalogue, and correcting an agent by
patching the prompt rather than by remembering to tell it again.
