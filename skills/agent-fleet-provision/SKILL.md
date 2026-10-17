---
name: agent-fleet-provision
description: >
  Build the infrastructure a named-agent fleet runs on, with StoryOS as the backend:
  the queue databases and their fields, then whatever workspace each agent needs —
  worktrees and local instances for engineering, tool access and a content calendar for
  marketing. Phase 2 of four, after agent-fleet has produced a roster.
  Use when the user says "provision the agents", "set up the agent infrastructure",
  "create the agent databases", or is ready to build a fleet whose roster is decided.
user-invokable: true
argument-hint: "<path to roster.md>"
metadata:
  version: "2.0.0"
  category: orchestration
  phase: "2 of 4 — provision"
license: CC-BY-4.0
author: "Ievgen Krasovytskyi (linkedin.com/in/krasovytskyi, github.com/ikrasovytskyi)"
---

# Agent fleet — provision the infrastructure

Requires a settled roster (`agent-fleet`). Produces databases, folders and
environments. Starts nothing — that is `agent-fleet-launch`.

## The databases

StoryOS is the backend. Minimum four:

**`<project>/issues`** — the work. Fields that matter and why:
- `state` (workflow) — Triage / Backlog / ToDo / In Progress / Done / Will Not Do
- `agents` (relation → agents db) — **this is the queue.** Name it deliberately;
  the relation's api_name is what every agent filters on
- `epic` (relation) — set on create, never left blank, or the ticket is invisible
  to epic-scoped planning
- `assignee` (member) and `human` (checkbox) — human accountability, sparingly
- `acceptance_criteria` (rich text) — a **dedicated field**, not a section inside
  details. Judgement roles read it as the contract
- `verdict` (select) — Unvalidated / Confirmed / Refuted / Mis-scoped / Cannot verify
- `details`, `user_story`, `expected_vs_actual`

**`<project>/agents`** — one record per agent: role, lane, reads, writes, must-not,
definition file. This is what the `agents` relation points at.

**`<project>/agent_runs`** — one row per tick that changed something. The only
place you can tell "quiet because nothing to do" from "quiet because broken".

**`<project>/scenarios`** — for signal agents: persona, steps, result, last run.
Engineering calls these UAT scenarios; a content fleet uses the same shape for
"read this as the target reader and report what you actually took away".

Downstream queues (docs tasks, website tasks, a content calendar) are optional
and share one trap — see "Auto-generated queues" below.

**Marketing adds one field that engineering does not need:** a `scheduled_for`
date and a `Scheduled` state. A scheduled item is neither done nor outstanding,
and without a state for it agents re-pick work already committed to the calendar.

## Per agent

**Every agent, every profile:**
- **token** — `write` scope, never `admin`. In an env file only, never in git
- **a queue that returns the expected count** before you hand it over

**Agents that write files** (all of engineering; a website agent in marketing):
- **workspace** — one agent, one folder, forever. In a repo, a git worktree
- **local instance if it needs one** — its own database, unique ports, a
  **stable** auth secret (a per-boot random one logs the agent out on every
  restart), and shared workspace packages built up front or the app dies on boot
  with hundreds of type errors and nothing warns you
- allocate a port block per agent; two agents on default ports collide silently

**Agents that do not write files** (judgement roles; most of a content fleet):
- **no folder at all.** Do not give them one "for consistency" — an unsatisfiable
  folder rule is a permanent silent stop
- **tool access instead**: analytics, scheduler, CMS, ad accounts. Scope each to
  the surfaces that agent owns, the same way a lane scopes file paths

## StoryOS API gotchas that cost real time

These are not documentation gaps you can reason around; each one produced a
wrong answer that looked right.

- **Select/workflow values are option UUIDs, never labels.** Resolve the option
  id from the schema each run. A client-side comparison to `"ToDo"` can never
  match, and it fails *silently* — the agent reports idle with a full queue.
- **Every database has its OWN option ids.** Reusing one database's `state` ids
  against another returns zero for everything.
- **`apiSlug` is not unique across a workspace.** Match on `qualifiedSlug`
  (`project/website_tasks`), or an agent writes into another client's tracker.
- **`/databases` returns a bare list**, not `{data: […]}`.
- **The title field is `name`**, not `title`, on writes.
- **Some responses contain raw control characters** — parse leniently
  (`json.loads(..., strict=False)`).
- **A default `Python-urllib` User-Agent gets 403.** Set one.
- **Relations are set with a plain array of ids** on update; the PATCH response
  does not echo relations, so re-read before concluding a write failed.
- **The `agents` relation is one-to-many: one agent per item.** A two-agent
  write is rejected. An item that moves design → build is re-routed by the owner
  when the design is posted. Decide that at provisioning, or design items stall
  with the designer.
- **Filter the queue with `{field: "agents", op: "has", value: [<agent id>]}`**, an array. `eq` and a bare id are rejected; a loop that treats the error as an empty result reports an idle agent with a full queue.
- **A title written on create is occasionally dropped.** Re-read the record and
  check the title is non-empty after every create.
- **Via MCP, set operators on a workflow field may fail validation.** An `or`
  of `eq` conditions works. Check one filter against a known non-empty case
  before scripting it.

## Auto-generated queues need a triager

If an automation creates a task row whenever work ships, those rows arrive with
**no owner** and are invisible to every queue filter. They accumulate silently —
88 rows in one real case, none of them ever seen.

Two decisions, both required: who prunes them (an owner role, checking the
database as a whole, since such tables usually have no `agents` field), and
whether the consuming agent may pull Backlog directly rather than waiting for a
promotion that is never coming.

## Verification before you hand over

For each agent, prove: the queue query returns the expected count, the token
authenticates, and — where they apply — the workspace is on the right branch, the
instance boots, the ports are free, and every tool the agent needs actually
answers. **A queue count that disagrees with reality is the one failure
that looks like success** — stop there rather than launching.

## References

- `references/fleet-status.md` — a monitoring script and the trap in writing one
- `references/database-schema.md` — field-by-field rationale
