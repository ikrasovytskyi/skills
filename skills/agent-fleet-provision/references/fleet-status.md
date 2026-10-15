# fleet-status — one screen for "what is the fleet doing"

Build this early. Without it you are inferring fleet health from screenshots.

Per agent it should report: ToDo depth, work in progress, current claim. Plus
lane-queue depths, **unrouted count**, human-assigned count, and the last few run
rows.

## The trap in writing it

The first version of this script showed `docs_tasks 0` and `Otto 0` while 88
items and a Triage ticket sat outside the window. Two causes, both worth
designing against:

**It reused one database's state option ids against another.** Every database has
its own. Resolve `state` options per database, always.

**It counted only ToDo and In Progress** — a builder's view of the world. That
hides Triage and Backlog entirely, which is precisely where unowned work
accumulates. Report every open state separately, or the monitor flatters the
system in exactly the way the system already fails.

## Shape

```python
def statemap(db):                       # per-database, never shared
    fields = GET(f"/databases/{db}")["fields"]
    f = next(x for x in fields if api_name(x) == "state")
    return {o["id"]: label(o) for o in f["options"]}

def page(db):                           # cursor pagination; has_more + next_cursor
    ...

# per agent: filter {"field":"agents","op":"has","value":[agent_id]}
# open = state not in (Done, Will Not Do)
# UNROUTED = open and no agent and no human   <- the number that matters most
```

Add a second orphan check alongside it: **open deliverables in nobody's queue.**
Cross-reference open PRs (or drafts) against the items each agent holds; anything
finished that no agent's queue points at is a cross-lane item that was passed
instead of split. It is invisible to every per-agent view by construction.

`UNROUTED` is the headline. A fleet with unrouted open work has agents idling
next to a pile nobody can see.

## Use it as a pre-launch check too

Run it before starting sessions and write the expected queue number next to each
agent's launch instructions. If an agent reports a different number, stop — that
is a broken filter, and a broken filter reports "idle" rather than an error.
