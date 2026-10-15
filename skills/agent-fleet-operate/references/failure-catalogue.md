# Silent failure catalogue

Every entry was observed. All of them present as "the agents are running fine,
there is just nothing to do." None raise an error. The **tell** column is what to
check, because the symptom is always the same.

## Queue reads

| Failure | Tell |
|---|---|
| Comparing a select value to a label instead of an option UUID | Agent reports idle; the same filter returns rows for another agent |
| Reusing one database's option ids against another | One database reads 0 while its rows plainly exist |
| Judgement role given a builder's `state = ToDo` filter | Agent holds In Progress / Triage items and reports empty |
| Records with a NULL state | Match no filter at all; count them explicitly |
| `apiSlug` collision across workspaces | Agent reads or writes another project's tracker |
| Monitoring script counting only ToDo + In Progress | Backlog and Triage piles stay invisible to the supervisor too |
| An agent's own queue query is wrong, so it reports "no queue" | Count by the `agents` relation from the supervisor's side; six routed items, "idle" reported |

## Routing

| Failure | Tell |
|---|---|
| Open records with no agent and no human | Everyone idle; `unrouted` count is non-zero |
| Auto-generated task rows with no triager | One database grows monotonically, never drains |
| A queue whose closing step belongs to nobody | Many In Progress, zero Done, ever |
| Single-valued `agents` relation on a design-then-build item | The design is posted and the item sits with the designer; nobody builds it. Re-route at the handoff, or split |
| Decision made in a comment, agent reads the body | Built against the original filing; the ruling three comments down is ignored |

## Sessions and folders

| Failure | Tell |
|---|---|
| `cd <folder> && claude` run inside a session | Two sessions per agent; the nested one is idle and silent |
| Session started in the home directory | Folder guard stops it hourly; or worse, it reaches into a worktree |
| Two sessions in one worktree | One renames a branch under the other; commits strand on merged branches |
| Loop armed in the session that fails the guard | Fires hourly, does nothing, looks scheduled |
| A `/loop` converted to a cloud schedule | Fails on the first local file read |
| Loop silently expired (~7 days) | Nothing happens and nothing says so |
| Poller script dies under `set -e` on `x=$(command -v tool)` when the tool is missing | No run rows since one date; the job is "loaded" and does nothing for weeks |
| Agent working in a checkout weeks behind main | Reports defects already fixed, or designs against code that has since been removed. Print HEAD before trusting any finding |
| Owner session restarted; peers still address the old one | Agents report "couldn't reach the owner" and idle instead of asking on the item |

## Work that vanishes

| Failure | Tell |
|---|---|
| Scope narrowed, dropped half filed nowhere | The PR body mentions it; the ticket does not |
| PR merged without the ticket recording it | Ticket body is the original stub after the work shipped |
| Work done, no run row written | Run log shows other agents active and this one absent |
| Findings reported only in chat | Nothing in the tracker; dies with the session |
| A cross-lane item **passed** instead of split | A deliverable that is finished and green sits in **nobody's** queue |

## Identifiers

| Failure | Tell |
|---|---|
| Tracker IDs and PR numbers share a range | "#556" resolves to two unrelated things; an agent reads the wrong one |
| A bare #N inside a PR body | Autolinks to a PR even when a ticket was meant |
| Work shipped under a different ticket number than the one it closes | A grep of history for the ticket finds nothing; check by commit ancestry against the deployed build |

If your tracker and your forge both number from 1, they will collide, and every
handoff that cites a bare number becomes ambiguous. Require the namespace in
writing — "ticket #551", "PR #556" — before the first collision rather than
after.

## Counting and ordering

| Failure | Tell |
|---|---|
| Indexing the wrong end of a list endpoint | Confident quotes of "the latest comment" that are the oldest one |
| Reporting the slice you pull from as the total | A queue "of 9" that is really 25; work in states nobody filters on |
| A monitor counting only the active states | Databases look empty while dozens of rows sit in Backlog or Triage |
| Two counts that disagree in **both** directions | Not a reconciliation problem: two different trees. Print HEAD on both sides before explaining anything |
| `updated_at` used as a proxy for activity | Misses comments entirely, and gets bumped by machine writes. It never measures human intent |

**Rule: when an agent reports a count, it must say what it counted.** A bare
number cannot be checked by whoever reads it, and the gap between the slice an
agent works from and the total that exists is exactly where work disappears.

**Rule: verify the ordering of any list before indexing it.** Reading
`items[-1]` as "newest" is wrong for every API that returns newest-first, and it
fails silently — you get a real comment, just the wrong one. One agent built a
fabricated finding on this and reported it upward before catching it.

## Ownership

| Failure | Tell |
|---|---|
| One item needs two lanes, so it is handed to the second | The first agent's deliverable is orphaned: done, correct, owned by nobody |
| An agent holds an item whose criteria it cannot satisfy | It cannot finish and cannot honestly close; it stalls quietly |
| A validator faces a partial pass | Merging is wrong, bouncing is wrong; the item needed splitting |

**The rule:** an item whose acceptance criteria cannot all be met by one agent
is **split by the writer**, never passed between makers. Passing it is a
narrowing of scope wearing a handoff's clothes — and the dropped part still needs
its own record.

**And: a deliverable belongs to its author until it lands or closes.** Passing an
item never passes the thing that was made for it.

*Observed:* one item needed an API change, a UI control and a configuration
change. The API half shipped, green and mergeable, then the item was passed to
the UI lane. Forty-eight hours later the deliverable was still open and owned by
nobody, while the receiving agent held criteria it could not satisfy — nine of
ten belonged to the lane that had already finished.

## Time

| Failure | Tell |
|---|---|
| An agent infers "today" from its own session context | Confident age/staleness claims that are wrong by days |
| A recurring pass judged against a stale date | "Already ran today" when it ran two days ago; work quietly skipped |
| A cadence window measured from a remembered date | Publishing bunches or stalls with no visible cause |

A long-lived session carries early timestamps in context and they age silently.
The rule is one line — **read the clock, never infer the date** — and the
corollary matters as much: a wrong date does not produce one wrong answer, it
produces a class of them. On discovering one, re-audit every other judgement in
the session that rested on the same assumption.

*Observed:* an agent called a 49-hour-old deliverable "hours old", having read a
correct timestamp and compared it against a date inferred from earlier in its own
session. It checked twice; only the second check was a measurement.

## Supervisor errors

| Failure | Tell |
|---|---|
| New rule appended while the old one sits in the runbook | Agents keep doing the old thing; the operative step wins |
| Rule applied uniformly to roles with different needs | An agent reports it cannot satisfy the rule at all — believe it |
| Judging ticket quality by regex over the wrong field | "No acceptance criteria" on tickets that have excellent ones |
| Concluding a filter is broken from one empty result | Run it against a known non-empty case first |
| Reading a stale branch as a deploy signal | Deployment is by image tag; the branch means nothing |
| Checking legacy branch protection on a ruleset-governed repo | Reports "no protection" on a protected branch |
| Filing a bot's interpretation of a screenshot as the bug | The ticket describes a defect nobody reported; the reporter's real complaint is somewhere else |
| Stating what code does from memory | A maker corrects you with a file and line; three times in one evening |
| Owner builds when asked to "go" | Nobody routes while it codes, and its work has no validator |

## The meta-pattern

Almost every entry is **one concept implemented in two places that drift**: two
lists of option ids, two definitions of Done, two copies of a rule, a branch and
an image tag both claiming to say what is deployed.

When you fix one, ask what the single source should be, and make the second
place read from it rather than restating it.
