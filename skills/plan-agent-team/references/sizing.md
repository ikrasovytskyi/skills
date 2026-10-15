# Sizing — worked examples, including the mistakes

## Example: a product engineering fleet (real, 10 agents)

Designed ten. Started eight. Two never ran because their environments did not
exist. One idled for a full day because nothing routed work to it.

What it actually needed on day one was **four**: a validator, a ticket writer,
an owner, and one maker in the busiest lane. Everything else could have been
added on evidence, and three of the ten were only justified later.

What the extra agents cost: not tokens — attention. Every agent needs a
workspace, a queue that fills, and a contract that stays consistent with nine
others. The tenth agent's marginal value was far below the cost of keeping its
contract coherent with the rest.

**The lesson worth transferring:** the constraint is not how many agents you can
run. It is how many contracts you can keep consistent, and how many queues you
can keep fed.

## Example: a content fleet (sketch)

Minimum that works: a writer for the busiest surface, a fact-checker, and an
owner who decides what publishes. Three.

Add a second writer when the first is at its cadence cap with a full queue —
that is the observable trigger, not a feeling that more content would be nice.
Add the reader (a signal agent from the audience) as soon as anything is
publishing regularly; it is the role most often skipped and the one that catches
work which is accurate, on-brand and persuades nobody.

## Triggers that justify adding an agent

Write these into the plan next to each "later" role, so the decision is made on
an observation instead of an urge:

- **a second maker in a lane** — the first is at its WIP ceiling with a queue
  that keeps growing across a week
- **splitting a lane** — two agents keep colliding on the same files or surfaces
- **a signal agent** — you are shipping regularly and finding defects only after
  users do
- **a second validator** — the gate is the bottleneck; makers report holding at
  the ceiling
- **a dedicated owner** — priority arguments keep escalating to the founder
- **a triager** — some database grows monotonically and nothing drains it

## Triggers that mean remove an agent

- idled for a week with an empty queue and no inbound in sight
- its lane has been absorbed by another agent's work
- it exists to check something already checked upstream — duplicate gates slow
  everything and diffuse responsibility, which is worse than one gate

## A note on ambition

People plan the fleet they want in six months. Plan the one that survives the
first week, and write the six-month version into the "later" list with triggers.
A fleet of four that runs beats a fleet of ten that half-runs, and the four teach
you what the next ones should be.
