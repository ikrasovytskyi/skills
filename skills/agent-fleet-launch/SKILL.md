---
name: agent-fleet-launch
description: >
  Start a provisioned named-agent fleet — one session per agent, workspace set
  correctly, identity checked, queue count verified, then the loop. Phase 3 of four. Also
  covers stopping the fleet cleanly and restarting it on another account. Use when the
  user says "launch the agents", "start the fleet", "give me the prompts for each agent",
  "stop the loops", or is moving a fleet between machines or accounts.
user-invokable: true
argument-hint: "<agent name, or 'all'>"
metadata:
  version: "2.0.0"
  category: orchestration
  phase: "3 of 4 — launch"
license: CC-BY-4.0
author: "Ievgen Krasovytskyi (linkedin.com/in/krasovytskyi, github.com/ikrasovytskyi)"
---

# Agent fleet — launch

Every failure in this phase looks identical from outside: the agent reports
"idle" and does nothing. None of them raise an error. So the launch procedure is
built entirely around **checking a number before trusting anything**.

## The procedure, per agent

1. **New session in the Claude Code app.** Set the folder to the agent's
   workspace, if it has one. Leave any "worktree" checkbox unchecked — an agent
   with a worktree already has one, and you do not want a worktree of a worktree.
   Agents that write no files need no particular folder; say so explicitly rather
   than picking one arbitrarily.
2. **Paste the identity prompt** (below). Do not skip it.
3. **Check the queue number** against what you measured while provisioning. If
   it disagrees, stop. Do not start the loop.
4. **Paste the loop line.**

**Never run `cd <folder> && claude` inside a session.** That launches a *second*
session nested in the first. It is the single most common way to end up with two
agents in one folder, and the nested one sits idle and invisible because nobody
ever typed into it.

## The identity prompt

```
You are <Name>. Read these three files, in order, before replying:
  <bin>/agent-house-rules.md
  <bin>/agents/<name>-<role>.md
  <bin>/<name>.txt
Then query your queue over the REST API and reply with exactly this, nothing more:
  PWD: what `pwd` printed
  WHO: one sentence on who you are and who you protect
  LANE: what you own, and the one thing you must never touch
  HANDOFF: who you pass work to when you finish an item
  QUEUE: how many items, and their numbers and titles
Do NOT start work. Do NOT start a loop.
```

`PWD` and `QUEUE` are the assertions. The rest tells you whether the contract
was actually absorbed — an agent that cannot state what it must never touch has
not read its file.

## The loop

```
/loop 1h Read <bin>/<name>.txt and execute it exactly as written. Do not summarise it — do it.
```

Three things about it:

- **Decline any offer to convert to a cloud schedule.** The agent is
  machine-bound — worktree, token, local database, authenticated CLI. A cloud
  run has none of them and fails on the first file read. Pre-answer this in the
  prompt file or the agent will ask every time and eventually accept.
- **The loop re-reads the file each tick**, which is why prompt files are the
  durable channel and chat messages are not.
- **Session-scoped, expires after ~7 days.** Closing the session stops it. A
  fleet you intend to keep running needs restarting weekly — this will surprise
  you a month later.
- **Interval follows the profile.** Engineering ticks hourly and drains.
  Marketing ticks against a cadence: a content agent that drains produces twelve
  posts in an afternoon. Stagger start minutes across agents either way.

## Messaging between sessions

Agents talk to each other by **name** ("Iris - Main Dev (Web)"). A session's
id changes every time it restarts, so an address copied from an old listing goes
nowhere. Put the fallback in the identity prompt: if a peer can't be reached,
the question goes as a comment on the item, and the agent carries on with its
queue.

## One session per folder

The exceptions are deliberate: read-only judgement roles can share a folder or
run from anywhere, because they never write to a worktree.

Two sessions in one worktree is genuinely dangerous. In one real incident a
session running from the wrong directory renamed a branch out from under the
session that legitimately owned that worktree. Nothing was lost, but the next
commit would have landed on a merged branch and been stranded.

**Verify with process inspection, not with the session list**, which shows
titles rather than working directories:

```bash
for pid in $(pgrep -f claude); do
  cwd=$(lsof -a -p $pid -d cwd -Fn 2>/dev/null | sed -n 's/^n//p')
  # keep only top-level sessions: skip any whose parent is itself claude/node
done
```

An app session appears as two processes (launcher plus the real one). Count
sessions, not PIDs.

## The folder guard, and how to scope it correctly

Agents that write files must be in their own folder; the guard is `pwd`. Agents
that write only records must **not** have that rule — in many harnesses their working directory resets to the
home project between calls and the check can never pass, which turns the guard
into a permanent silent stop.

For them the rule is a prohibition, not a location: never enter another agent's
workspace and work there, no destructive commands, no writes outside your own
lane. In a content fleet this is most of the roster.

If an agent's folder is a **symlink**, accept both the logical and resolved
paths — `pwd` may print either depending on how the session was started.

## Stopping the fleet

```
Cancel every scheduled job you have and confirm the ids you cancelled.
If you changed any record this tick, write your run row first.
Stop any dev server you started.
Leave claimed work exactly as it is — do NOT release it. You restart as the same
agent and your resume rule will pick it up.
Then stop.
```

Ask each to reply with the job ids, whether it wrote a run row, and what it was
mid-way through. That reply is your handover note.

## Moving to another machine or account

Prompt files are the state. Copy `<bin>/` and the roster; recreate worktrees and
`.env` files; re-mint tokens. Then run this phase again. Nothing needs to be told
to any agent — new sessions read the files fresh, which is the entire reason the
rules live in files.
