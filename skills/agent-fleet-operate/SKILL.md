---
name: agent-fleet-operate
description: >
  Run a live named-agent fleet: the gate that stops unchecked work reaching the world,
  monitoring, routing work that nobody owns, and correcting an agent so the correction
  sticks. Phase 4 of four — the one you
  stay in. Use when the user says "check the fleet", "what are the agents doing", "an
  agent is wrong about X", "PRs are merging without review", "the queue is empty but
  there's work", or is supervising agents already running.
user-invokable: true
argument-hint: "<agent name, or 'status'>"
metadata:
  version: "2.1.0"
  category: orchestration
  phase: "4 of 4 — operate"
license: CC-BY-4.0
author: "Ievgen Krasovytskyi (linkedin.com/in/krasovytskyi, github.com/ikrasovytskyi)"
---

# Agent fleet — operate

The fleet mostly works. This phase is about the ways it fails **quietly**, and
the one principle that governs all of them:

> Anything that depends on a human remembering something at the right moment
> will eventually fail unattended, and fail silently. It is only really fixed
> when it moves from instructions into structure.

## Consult, do not decree

Anything inside an agent's lane goes to it as a proposal with the reasoning and
the evidence, not as a ruling — and you read the answer. This is the highest-value
loop in the whole method, and it is easy to skip because issuing an instruction
feels faster.

The record from one fleet, in a single evening: an agent found a folder rule
unsatisfiable in its harness and said so instead of working around it. One found
a queue holding 45 finished items that nobody could close, because a rule written
for one database had been applied to another. One refused to start work whose
criteria it could not satisfy. One corrected the supervisor's claim about how
widely a bug had spread. **Every one of those was a defect in an instruction,
found by the agent the instruction governed** — and none would have surfaced from
compliance.

So state it in the shared contract, because agents default to obeying:

  · an instruction that cannot be satisfied gets reported, never worked around —
    the workaround hides the defect
  · two instructions that contradict get named, and the agent asks which wins
    rather than silently choosing
  · being overruled after arguing is fine; complying without arguing is not
  · **silence is taken as agreement**, so disagreement must be spoken

And tell them their answers are read. An agent that believes its reply disappears
into a log writes a different, worse reply.

**Watch for the failure this exists to catch: you will create the very defect you
are writing rules against.** In one session the supervisor wrote a rule to stop
two owners deciding one thing — and in the same hour gave two agents authority
over the same database, spotting it only when prompted to go and tell one of them.
Proposing rather than ruling is what surfaces that, because the agent asks the
question you did not.

## The owner routes; it does not build

When the founder says "anything left on your list, go build it", the owner's
answer is to assign it, not to open an editor. The founder corrected exactly
this a minute later: *"oh sorry man, go ask others to work."* An owner who
builds is a maker with no validator and no lane, judging its own work, and
nobody is routing while it codes.

Mechanical chores the founder explicitly hands the owner (a rebase, a README)
are the exception, and the exception is named each time.

## A direction change is routed, not announced

When the founder states a new direction ("clients say it's too complex, I want
it as simple as Trello"), it becomes work in the same hour, or it becomes a
mood:

- **One design item** that owns the direction, with a method (measure the
  current state against the reference products), a visual deliverable, and an
  approval step before any build is split from it.
- **Superseded build items go on hold, with a comment saying why.** A re-skin
  started before the simplification would lock in what the simplification is
  about to cut.
- **Duplicates are merged on sight.** Two design items for one surface produce
  two treatments.
- **Evidence is linked, not restated.** That day's client-call bugs became the
  design item's evidence list.

Then save the direction where every future session reads it. A direction that
lives only in one chat is lost at the next restart.

## Production writes need the founder's OK, every time

A schema or data change on a live system waits for an explicit yes from the
founder, relayed by the owner, per write. The approval does not carry over to
the next write. The maker quotes the state before and after on the item.

Before asking, prove the code that reads the new state is actually deployed.
Check ancestry against the build's commit (`git merge-base --is-ancestor <pr-sha>
<deployed-sha>`), not a grep for the ticket number: the PR that shipped it was
filed under a different ticket, and the grep found nothing.

## The maker's "not verified" list is the validator's checklist

When a maker hands work over, its "what I did not verify" lines are the most
valuable thing in the handoff. The owner forwards them to the validator **by
name**, per PR: "split view was never opened", "this also moves a shared editor,
check it at desktop width", "the fix is in a helper the sidebar also uses". A
validator who re-derives the risks from the diff finds fewer of them.

## When the owner is unreachable

Sessions restart and their addresses change. One evening a maker went to ask
the owner a question, found the session gone, and stopped: it reported "idle,
no queue assigned" with six items routed to it. Two failures stacked, and the
contract must close both:

- **If the owner can't be reached, the question goes on the item as a comment**
  and the agent moves to the next item. Waiting is not a state.
- **"No queue" is a count, and it says what it counted.** An agent that reports
  an empty queue states the query. Then the supervisor can see that the filter
  is wrong, not the queue.
- Address peers by their agent name, never by a session id copied from an old
  listing.

## Correcting an agent

Two actions, and the second is the one that matters:

1. **Message the session** with the correction and the reproducible evidence.
2. **Patch the prompt file**, because the message dies with the session.

**If you ever send the same correction twice, that is a prompt bug, not a
supervision need.**

And when you patch: put the rule **where it executes**. Appending "you no longer
merge" to the end of a file while `gh pr merge --auto` remains step 8 of the
runbook changes nothing — the numbered step is the line the agent reaches. This
is the same one-concept-in-two-places defect that fleets are built to catch, and
supervisors commit it constantly.

## Verify before you correct

Two false corrections in one evening, both nearly sent:

- An agent was accused of skipping acceptance criteria. The measuring regex
  required `1.` and the agent wrote `AC1`, and the criteria were in a dedicated
  field the check never read.
- An agent was told its filter worked when its own token appeared to return
  nothing — the empty result came from a quoting bug in the checking script.

Read the thing. Do not judge from a pattern when you can look.

Three more, all from the supervisor's own side:

- **A bot's reading of a screenshot is not a report.** The founder sent mobile
  screenshots through a chat bot, and the bot's interpretation was filed as the
  bug. Both tickets described defects that didn't exist ("898 was not about
  search. It was about not having the colour behind those buttons."). Refuting
  the bot's guess is not finding the defect either. With no human reporter, go
  to the screen or the code.
- **Quote the code before stating what it does.** The owner told a peer three
  times, from memory, what an importer did ("it doesn't exist", "it overwrites
  descriptions", "this team includes that project"), and was corrected each
  time. A claim about code carries a file and a line, or it is a guess.
- **Route by the item's own text.** Two items were routed to the lane that
  "sounded right" against what their own bodies said, and had to be reverted.
  Read the item, and its comments, because decisions live in comments and the
  body is the original filing.

## The gate

Makers do not land their own work. A maker finishes the deliverable, records it
on the item with what it did and did not verify, sets the validator as next
agent, and moves on. The validator checks each acceptance criterion — exercising
it, not reading it — and lands it, sends it back, or escalates.

In engineering "land" means merge. In marketing it means publish or schedule,
and the gate matters MORE: publishing is less reversible than merging, and a
wrong claim reaches the audience instantly.

What auto-merge cost in one evening, for the argument: 17 documentation PRs
merged unchecked; one PR shipped half its ticket with the dropped half filed
nowhere; two permission leaks reached main, one letting a guest export record
titles from a database that returned 404 to their own session.

Rules that make the gate hold:

- **"Never land it to be helpful."** If work piles up faster than verification,
  the validator reports the backlog rather than clearing it. A backlog you can see is
  a problem; a merge nobody was sure about is a defect you cannot see.
- **The validator never resolves a conflict** — choosing between two versions is
  authorship. It goes back to the author. (Engineering: merge conflicts.
  Marketing: two drafts revising one page.)
- **Check the real landing mechanics before writing instructions.** Engineering
  specifics are in `agent-fleet/references/profile-engineering.md`; the general
  lesson is that a ban written for makers must carve out the validator, or the
  gate cannot land anything at all. That happened: a blanket ban on one flag
  left the validator unable to merge.

## An item that needs two lanes is split, never passed

The most-missed rule, and it looks like good behaviour when it goes wrong. A
maker finishes its half, sees the rest belongs to another lane, and hands the
whole item across. That is a narrowing of scope wearing a handoff's clothes, and
it produces the one artefact nothing in the system watches: **a deliverable that
is finished, correct, and in nobody's queue.**

Three things break at once — the deliverable is orphaned, the receiving agent
inherits criteria it cannot satisfy, and the validator is handed a contract
nobody can fulfil, so the item can never pass.

The rule: hand the ITEM to the writer with the division spelled out — which
criteria belong to which lane, which half is built, where its deliverable is.
One item per lane, each satisfiable alone. The finished half then lands on its
own merits, waiting for nobody.

Two corollaries worth stating explicitly in every contract:
- **a deliverable belongs to its author until it lands or closes.** Passing an
  item never passes the thing made for it
- the validator facing a partial pass must neither land it nor bounce it to its
  author — both are wrong in a way that looks reasonable. The item goes to the
  writer to be split

**Supervisor's tell:** scan for open deliverables that appear in no agent's
queue. Green, old, unmentioned. Add it to whatever monitoring you build.

**And never let the split block the merge.** A verified, correct deliverable
must not wait for its malformed item to be reorganised. The validator lands it on
the criteria it was actually written against, records what remains and whose lane
it belongs to, and sends the item to be split afterwards. Splitting is
bookkeeping; the blockage is real.

This matters most when the work touches something **shared** — a package every
lane depends on. Then holding it does not merely delay one item, it blocks every
lane downstream. Check for that explicitly before deciding to hold anything: the
question is not "is this item complete" but "who cannot work until this lands".

## Work nobody owns is invisible

The most common silent failure. Records exist, no agent is named on them, so
they appear in no queue and everyone reports idle. Look for it explicitly:
`open AND no agent AND no human`. In one real fleet that was 41 of 56 open
tickets while eight agents idled.

Auto-generated task rows are the chronic version: something creates them when
work ships, nothing routes them, and they pile up (88 in one case) until someone
counts. Give every such database a triager, and let the consuming agent pull
Backlog directly rather than waiting for a promotion nobody is issuing.

## Reading a "Done" that means two different things

On the issue tracker Done means *shipped and verified* — the validator's call.
On a downstream task queue it means *the page was written*, which is mechanical.
Applying the first rule to the second deadlocked a queue at 45 In Progress and
zero Done in its entire history — every one finished, none closable, because the
closing step belonged to nobody.

When one word means two things, say which one, per database.

## WIP ceilings make bottlenecks visible

Cap unfinished deliverables per maker (three works). At the cap the agent holds
idle and says so. A verification bottleneck then looks like a maker standing
still — visible at a glance — instead of work quietly diverging until it all
conflicts at once.

In a content fleet the ceiling doubles as cadence protection: a writer three
drafts deep is not blocked, they are ahead of the calendar.

## Environments

Name the environment in every verdict. Live, staging and a local instance —
or published, scheduled and draft — answer different questions, and "verified" without a named environment is the ambiguity
that gets a bad deploy approved. Deployment is usually by image tag, not by
branch — a stale `staging` branch may mean nothing at all, and a supervisor who
reads it as a signal will draw a confident wrong conclusion.

## What good looks like

Agents that stop and say why. In one evening: one refused to work because its
folder was wrong and declined to `cd` ("the guard exists precisely to stop an
agent from deciding that for itself"); one caught that a rule I wrote was
unsatisfiable in its harness; one found a queue deadlock nobody had noticed; one
found two permission leaks by using the product as a customer.

Three of the four real defects that evening were found by agents, not by the
supervisor. That is the system working.

## References

- `references/failure-catalogue.md` — every silent failure seen, with its tell
