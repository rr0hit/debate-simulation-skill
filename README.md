# Agentic Debate System

A Codex skill for spinning up synthetic debates for fun.

Give it a motion, let the agents argue, and watch a synthetic audience-agent panel move. The
interesting question is not "is this proposition true?", but "which advocate agent made the more
persuasive case to the audience agents?" The skill creates a topic-blind synthetic
audience-agent panel, gives both sides a chance to argue and rebut, polls the same audience
agents before and after the debate, and judges advocate performance by audience-agent swing.

## Motivation

This started from a simple idea: debates are more entertaining when there is a synthetic
audience-agent panel to win over. A single narrator can summarize both sides, but that misses
the fun part: which advocate agent moved audience agents?

This skill turns that into a small agentic debate system:

- create the audience agents before revealing the motion
- preserve audience-agent continuity between first and final polls
- separate final preference from persuasion swing
- use factual framing when current facts, law, or public claims matter
- produce a standalone HTML record so the debate can be reviewed or shared later

It is not trying to predict human polling samples, human audiences, or actual outcomes. It does not
validate a proposition, prove an idea, or decide which side is correct. It is a synthetic,
inspectable game for comparing advocate agents, their arguments, and how those arguments move
audience agents within the simulation.

## What It Does

- Runs a multi-role debate with a moderator, advocates, media-context mapper, and audience agents.
- Defaults to 20 synthetic audience agents and 2 rebuttal rounds when not specified.
- Uses pre/post audience-agent polling to judge persuasion swing.
- Reports both the advocate winner by swing and the final audience-agent preference.
- Creates a standalone HTML debate record with collapsible detail sections, because debates are
  more fun when you can send the scorecard around.

## Install

In Codex, ask the built-in skill installer to install this skill from GitHub:

```text
$skill-installer install https://github.com/rr0hit/debate-simulation-skill/tree/main/skills/debate-simulation
```

Restart Codex after installation so it can load the new skill metadata.

Manual install also works:

```text
~/.codex/skills/debate-simulation/
  SKILL.md
  agents/openai.yaml
```

## Usage

Invoke it explicitly:

```text
$debate-simulation Motion: Pineapple belongs on pizza. Use 20 audience agents and 2 rebuttal rounds.
```

Or ask for a debate in plain language:

```text
Debate whether pineapple belongs on pizza.
```

Try it on serious topics, silly topics, product arguments, policy questions, office disputes, or
anything where it would be interesting to compare how advocate agents move a synthetic
audience-agent panel. Do not use it to validate ideas, prove a side is true, or forecast what a
human audience would do.

If you omit configuration, the skill asks for the motion and uses defaults where appropriate.

## Cost Note

This skill can create many subagents, especially with larger audience-agent panels or more
rebuttal rounds. For cost-sensitive runs, use one of the available mini models when the quality
tradeoff is acceptable, especially for audience-agent polling turns.

## Output

The final result distinguishes:

- `Debate winner`: the advocate agent with the largest positive audience-agent swing
- `Final audience preference`: the side with the largest final audience-agent vote share
- `Tie`: no movement or equal movement

The skill also writes a self-contained `.html` debate record in the current working directory.
That record includes the motion, polls, swing, core arguments, rebuttals, sources, and collapsible
details.

## Structure

```text
skills/debate-simulation/
  SKILL.md
  LICENSE.txt
  agents/openai.yaml
```

## License

MIT. See `LICENSE`.
