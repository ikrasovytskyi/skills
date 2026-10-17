---
name: plan-agent-team
description: >
  Work out what team of AI agents a project actually needs — or whether it needs one at
  all. Interviews you about the work, infers what it can from the repo or the current
  session, and produces a right-sized roster with a phased rollout. Phase 0, before
  agent-fleet. Use when the user says "plan an agent team", "what agents should I have",
  "I don't know how to build agentic teams", "should I use agents for this", "design a
  team for this project", or describes a project and asks how to staff it with agents.
user-invokable: true
argument-hint: "[project path or name]"
metadata:
  version: "1.0.0"
  category: orchestration
  phase: "0 of 4 — plan"
license: CC-BY-4.0 (see LICENSE). Author: Ievgen Krasovytskyi, linkedin.com/in/krasovytskyi, github.com/ikrasovytskyi
---

# Plan an agent team

You are the planner. The user may know their project deeply and know nothing
about agent teams. Your job is to ask a small number of questions that actually
change the answer, infer everything else, and produce a roster they can hand to
`agent-fleet`.

**Two failure modes to avoid, in order of likelihood:**

1. **Recommending a fleet that should not exist.** Most projects do not need
   one. Say so plainly when it is true — see "The gate" below.
2. **Building ten agents when four would do.** In the fleet this method came
   from, ten were designed, eight started, two never ran because their
   infrastructure did not exist, and one idled for a day because nothing routed
   work to it. Size down.

## Step 1 — infer before you ask

Never ask what you can measure. Spend a few minutes here; it makes the interview
short and makes your proposal specific rather than generic.

**If there is a repository:**
- lanes, from who touches what: cluster the last 50–100 merged changes by
  directory and see whether natural territories exist. If everything touches
  everything, there are no lanes and the answer is fewer agents, not more
- cadence: commits per week tells you whether a queue will fill
- existing review discipline: are PRs reviewed, or does everything land
  unchecked? This decides how hard you argue for the gate
- an existing tracker: what states exist, who is assigned, how much is stale

**If there is a live product or an audience:** what surfaces exist (channels,
pages, docs), and which are demonstrably neglected.

**If you are mid-session with project context:** use it. The user asked here
rather than in a fresh session for a reason — they expect you to already know
what has been discussed.

Report what you inferred **before** asking anything. It shows your work and lets
them correct a wrong reading early.

## Step 2 — the interview

Ask these, in roughly this order. Stop early when the answer is already clear —
a planner that asks eleven questions to justify itself is worse than one that
asks four and proposes.

1. **What work repeats?** Agents are for recurring work with a shape. One-off
   projects do not need a fleet; they need one good session.
2. **Where does work come from?** Who or what generates the inbound? This is the
   single most predictive question — an agent with no inbound source idles
   forever. If the answer is "I think of things", that is a queue with one
   producer and it will not sustain five agents.
3. **What is the cost of being wrong?** Data loss and public claims justify a
   strict gate. A prototype does not.
4. **What is checked today, and by whom?** If nothing is checked, the first
   agent to build is the checker, not a maker.
5. **What must a human still decide?** Money, security, external commitments,
   strategy. Everything else can carry no human.
6. **What is already automated or half-automated?** Existing scripts and
   automations are queue producers, and they usually have no owner.
7. **What tools would an agent need that you cannot give it today?** This is
   where a roster meets reality — an operator agent with no running environment
   is a role you cannot staff yet.

## Step 3 — the gate: should this exist at all?

Recommend **no fleet** when any of these hold, and say why:

- the work does not repeat
- one person does all of it and is not overloaded
- there is no way to check the output — an unchecked fleet produces more of
  whatever you already get wrong, faster
- nobody will read the queue. A fleet is a system of record before it is a
  labour saver; if the records will not be maintained, the fleet degrades into
  ten sessions doing unrelated things

Recommend **a single agent, not a fleet**, when the work repeats but has one
lane. That is a legitimate and common answer, and it costs nothing to grow later.

## Step 4 — size it

Start from the smallest thing that works and add only on evidence.

**The minimum viable fleet is three:** one maker, one validator, one owner who
sets priority and can say no. Below three you lose the property the whole method
rests on — that nobody checks their own work.

Add an agent only when you can name:
- **its lane**, as addresses (paths, surfaces, systems), not as a topic
- **its inbound**, and who or what produces it
- **its handoff**, who receives finished work

If you cannot fill all three lines, it is a role, not an agent. Write it in the
"later" list with the trigger that would justify it.

**Order of construction matters.** Build the gate before the makers. A fleet that
gains checking capacity last spends its early weeks producing unverified output
at speed — which is the specific thing that hurt the fleet this method came from:
seventeen deliverables landed unchecked in one evening because the checker was
not yet in place.

## Step 5 — the deliverable

Produce **`team-plan.md`**:

1. **What I inferred** — with the evidence, so they can correct it
2. **Recommendation** — fleet, single agent, or none, with the reasoning
3. **Phase 1 roster** — three to five agents, each with identity, lane, inbound,
   handoff, never-touch, disposition
4. **Later, and the trigger for each** — the agents not to build yet, and the
   specific observation that would justify each one
5. **Blocked** — roles that need infrastructure that does not exist, and what it
   would take
6. **What to build first** — usually the queue database and the gate
7. **How you will know it is working** — two or three observable signals, chosen
   so their absence is visible

Then hand to `agent-fleet` for the full roster and contracts.

## Other ways this skill gets used

- **Right-sizing a live fleet.** Point it at one that already exists: who has
  idled for a week, whose queue only grows, what work is routed to nobody. Same
  interview, run against evidence instead of intentions.
- **Diagnosing a fleet that is not producing.** Almost always one of three
  things: nothing routes work in, one role is a bottleneck, or a queue filter is
  silently wrong. See `agent-fleet-operate/references/failure-catalogue.md`.
- **Deciding whether a new function joins an existing fleet or starts its own.**
  Rule of thumb: shared tracker and shared definition of done means one fleet;
  different audience and different cadence means two.
- **Planning a human/agent hybrid**, where some lanes are people. The method is
  unchanged — a human in the chain is an agent with a slower tick and a much
  better refusal.
- **Auditing an org's manual process** before automating anything: the interview
  alone surfaces work nobody owns and checks nobody performs.

## References

- `references/interview.md` — the questions, with what each one is really testing
- `references/sizing.md` — worked examples from real fleets, including the errors
