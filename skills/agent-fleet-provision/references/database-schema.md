# Field-by-field rationale

## `agents` (relation → agents database)

The queue. Everything else is decoration. Two things to get right:

**Name it deliberately.** The relation's api_name is what every prompt filters
on, and creation tools have been known to ignore the requested field names and
return 200 — verify what was actually created rather than what you asked for.

**Queue filters are role-shaped, not uniform.** Builders pull `ToDo`. Judgement
roles must NOT filter on state at all — their inbound arrives in whatever state
the sender left it: a builder hands off at In Progress, a fresh finding lands in
Triage, a reopened item comes back at Backlog. A validator holding two items
reported "idle — queue empty" because she had a builder's filter.

The judgement-role query: filter `agents has <me>`, no state filter, then drop
Done and Will Not Do **by id**.

## `acceptance_criteria` as its own field

Not a heading inside `details`. Two reasons: the verifier reads it as the
contract and needs it unambiguous, and any tooling that measures ticket quality
will look in the wrong place otherwise. (Measuring AC by grepping `details` for
numbered lines produced two false "no acceptance criteria" verdicts against
tickets that had excellent ones in the proper field.)

## `verdict`

Unvalidated / Confirmed / Refuted / Mis-scoped / Cannot verify.

`Mis-scoped` is the one that earns its place: it is the verdict for "this
shipped, and it is not what the ticket asked for". Without it a verifier faces a
false binary and passes work that quietly did half the job.

## `human` (checkbox) and `assignee`

Reserve for decisions only a person can make. Every other ticket carries neither,
and builders pull on the agent relation. A human name on a ticket nobody expects
a human to action trains everyone to ignore assignees.

## `epic` — set it on create

Never as a follow-up, never blank. An epic-less ticket drops out of every
epic-scoped view and is effectively invisible.

## A record with a NULL state is invisible to everything

It matches no state filter, so no agent will ever see it. Whatever creates
records must set the state in the same call. Audit for nulls after any bulk
import — one real case sat unreachable until an agent happened to notice.
