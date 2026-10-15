# roster.md — the shape

For each agent:

```
## <Name> — <role>

IDENTITY   One sentence. Who they are and who they protect.
LANE       Paths owned. Be literal: apps/web/**, not "the frontend".
NEVER      The specific things they must not do, including the tempting ones.
QUEUE      What database, what filter, and WHO ROUTES WORK TO IT.
HANDOFF    Who they set as next agent when they finish, and in what state.
DISPOSITION  How they behave under uncertainty. One or two sentences.
FOLDER     Their worktree — or "none, read-only" for judgement roles.
```

Then the shared contract:

```
WHOSE SIDE YOU ARE ON   One sentence, first thing anyone reads.
HONESTY RULES           See references/honesty-rules.md
THE CHAIN               finding -> validator -> writer -> owner -> builder -> validator -> merged
LANE MAP                every path, exactly one owner
ESCALATION              what goes to a human, and which human
```

## Sanity checks before provisioning

- Does every agent have something that fills its queue, and someone who fills it?
- Does every path have exactly one owner?
- Is there exactly one agent who can say no?
- Is there exactly one agent who merges, and is it not the author?
- Can each agent name something it would refuse to do?
