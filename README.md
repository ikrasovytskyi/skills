# Agent-team skills

By **Ievgen Krasovytskyi** · [LinkedIn](https://www.linkedin.com/in/krasovytskyi/) · [GitHub](https://github.com/ikrasovytskyi)

Skills for running real work with a **team of named AI agents**. Each agent is its own Claude Code session, with its own queue, a lane it owns, things it must never touch, and a validator who checks its work before anything reaches the world.

This is the method behind the agent team that builds [StoryOS](https://storyos.dev): ten agents across API, web, docs, design, product and validation, with StoryOS itself as the queue. Every rule in these files comes from a specific failure, and the failure is written next to the rule.

## The skills

They run in order. Most people start at phase 0 and stay in phase 4.

| Phase | Skill | What it does |
|---|---|---|
| 0 | [`plan-agent-team`](skills/plan-agent-team/SKILL.md) | Works out whether you need an agent team at all, and how big. Interviews you, reads what it can from the repo, and proposes a right-sized roster with a phased rollout. |
| 1 | [`agent-fleet`](skills/agent-fleet/SKILL.md) | Designs the roster: who exists, what each one owns, what each must never touch, and who checks whom. Domain-neutral; a profile supplies the vocabulary (engineering, marketing). |
| 2 | [`agent-fleet-provision`](skills/agent-fleet-provision/SKILL.md) | Builds the infrastructure: the queue databases in StoryOS and their fields, then each agent's workspace (worktrees and local instances for engineering, tool access and a content calendar for marketing). |
| 3 | [`agent-fleet-launch`](skills/agent-fleet-launch/SKILL.md) | Starts the fleet: one session per agent, identity checked, queue count verified, then the loop. Also covers stopping it cleanly and moving it to another machine. |
| 4 | [`agent-fleet-operate`](skills/agent-fleet-operate/SKILL.md) | Runs it: the gate that stops unchecked work reaching the world, routing work nobody owns, correcting an agent so the correction sticks, and a catalogue of every silent failure seen so far. |
| any | [`agent-fleet-marketing`](skills/agent-fleet-marketing/SKILL.md) | Entry point for a marketing or content team: writers per channel, an editor, a fact-checker, an owner who decides what publishes, and readers who report whether it lands. |

## Install (Claude Code)

```bash
git clone https://github.com/ikrasovytskyi/skills.git
cp -R skills/skills/* ~/.claude/skills/
```

Then start a new Claude Code session and say "plan an agent team", or run `/plan-agent-team`.

Keep the folders whole: several skills read files from each other's `references/` directory.

## What you need

- **Claude Code.** Each agent is a long-lived Claude Code session; the launch phase uses its loop.
- **A StoryOS workspace** as the queue: issues, agents, agent runs. Hosted at [app.storyos.dev](https://app.storyos.dev), or self-hosted. The provisioning skill creates the databases.
- For an engineering fleet: a git repository with branch protection, and room for one worktree per agent.

## What these are not

- Not a framework. There is no code to run; these are instructions an agent follows.
- Not finished. There is no operations profile yet, and the method has mostly been exercised on one engineering fleet and one content fleet. If something fails for you, the failure catalogue in `agent-fleet-operate` is the place it belongs: open an issue.

## Author

Written by **Ievgen Krasovytskyi**, founder of [StoryOS](https://storyos.dev), from running the agent team that builds it.

- LinkedIn: [linkedin.com/in/krasovytskyi](https://www.linkedin.com/in/krasovytskyi/)
- GitHub: [@ikrasovytskyi](https://github.com/ikrasovytskyi)

## License

[CC BY 4.0](LICENSE). Use them, adapt them and share them, commercially too, as long as you give credit. A credit line that works:

> Agent-team skills by Ievgen Krasovytskyi ([LinkedIn](https://www.linkedin.com/in/krasovytskyi/) · [GitHub](https://github.com/ikrasovytskyi/skills)), CC BY 4.0.

If you post about using them, tag Ievgen on LinkedIn.

