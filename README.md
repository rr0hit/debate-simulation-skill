# Debate Simulation Skill

A Codex skill for running structured debate simulations with synthetic audience movement.

The skill is designed for debates where the interesting question is not simply "which side
ended ahead?", but "which advocate moved the audience more?" It creates a topic-blind
synthetic audience, gives each side a chance to argue and rebut, polls the same audience
before and after the debate, and judges the result by persuasion swing.

## Motivation

Many debate summaries collapse into a single narrator's judgment. That is useful for quick
analysis, but weak for questions where persuasion matters. This skill forces a more disciplined
shape:

- create the audience before revealing the motion
- preserve audience-agent continuity between first and final polls
- separate final preference from persuasion swing
- use factual framing when current facts, law, or public claims matter
- produce a standalone HTML record so the debate can be reviewed or shared later

The goal is not to predict real voters or real audiences. The output is a synthetic exercise
that makes assumptions explicit and produces a durable record of what moved the simulated
audience.

## What It Does

- Runs a multi-role debate with a moderator, advocates, media-context mapper, and audience agents.
- Defaults to 20 synthetic audience agents and 2 rebuttal rounds when not specified.
- Uses pre/post audience polling to judge persuasion swing.
- Reports both the debate winner by swing and the final audience preference.
- Creates a standalone HTML debate record with collapsible detail sections.

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
$debate-simulation Motion: Kerala poorams and ulsavams should stop using live elephants and move to alternatives. Use 20 audience agents and 2 rebuttal rounds.
```

Or ask for a debate in plain language:

```text
Debate whether poorams and ulsavams should stop using elephants.
```

If you omit configuration, the skill asks for the motion and uses defaults where appropriate.

## Output

The final result distinguishes:

- `Debate winner`: the advocate with the largest positive audience swing
- `Final audience preference`: the side with the largest final vote share
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
