---
name: debate-simulation
description: Use when the user wants to run, design, document, or reuse a multi-subagent debate simulation judged by pre/post audience movement. This skill creates a synthetic debate, not an empirical poll or prediction. It asks for a motion, audience size, and number of rebuttal rounds, then orchestrates separate subagents for advocates, moderator, topic-blind audience scout, media environment mapping, cohort-shaped audience voting, checkpoints, swing-based judging, and a final standalone HTML debate record.
---

# Debate Simulation

## Overview

Run an agent-based debate simulation where advocates argue a motion before a synthetic audience. The result is judged by how much the audience moves between the first and final poll, not by which side has the largest final vote share.

Explain this plainly to first-time users:

> This is a debate simulation, not a real survey, forecast, or empirical result. We create plausible audience agents, expose them to arguments, and measure which advocate persuaded them more.

## Required Setup

Before running the simulation, ask for:

- `motion`: the debate question or resolution.
- `audience_count`: number of audience agents. Default to 20 if the user does not specify.
- `rebuttal_rounds`: number of rebuttal rounds after openings. Default to 2 if the user does not specify.

Use concise phrasing:

> Give me the motion, how many audience agents you want, and how many rebuttal rounds. If you do not specify, I will use 20 audience agents and 2 rebuttal rounds.

If the user provides all three, start without further clarification.

## Core Roles

Use these roles:

- `Moderator`: researches the motion, frames the debate agenda, runs checkpoints, fact-checks important claims, and reports results.
- `Audience Scout`: creates the audience before knowing the motion, using only broad eligibility constraints such as location, stakeholder category, or demographic scope.
- `Media Environment Mapper`: after the motion is revealed, maps what each audience profile is likely to have already seen through cohort-appropriate media, social circles, and lived experience.
- `Advocate A` and `Advocate B`: research and argue the strongest case for their assigned side.
- `Audience Agents`: each named audience member is its own persistent subagent. They vote in the first poll, listen to arguments and rebuttals, and vote again. They should react like people shaped by background, media diet, identity, emotion, and priorities.

Do not describe a side as having "won the debate" by itself. Say "the advocate for X won the debate" when judging persuasion.

## Subagent Execution

This skill is meant to be run with actual subagents when subagent tools are available. Do not collapse the method into a quick single-agent simulation unless subagents are unavailable. If subagents are unavailable, state that limitation before running a compact fallback.

The parent agent is only the orchestrator, editor, and reporter. When subagents are available, the parent agent must not invent, simulate, or impersonate substantive output for the Moderator, advocates, or audience voters. It may summarize outputs it actually received from those subagents.

Use separate subagents for the core roles:

- Spawn `Audience Scout` first, with `fork_context: false`. Give it only broad audience constraints and audience count. Do not reveal the motion or sides.
- Spawn `Moderator` after the motion is known. Give it the motion, the two sides, and the instruction to research, frame issue rounds dynamically, and fact-check major claims.
- Spawn `Media Environment Mapper` after audience profiles exist. Give it the motion and audience profiles. Ask it to map cohort-appropriate media/social exposure for each profile, not a neutral reading list.
- Spawn `Advocate A` and `Advocate B` separately. Give each only its assigned side, the motion, and the audience/media brief if available. Ask each to research and build the strongest case for its side. Keep each advocate's agent id and reuse that same advocate subagent for later rebuttal or closing turns.
- Spawn one `Audience Agent` subagent for every named audience member. The audience count is the number of audience subagents to create. Do not batch audience members unless the user explicitly authorizes batching. If tool limits make one subagent per audience member impossible, stop and ask the user to reduce the audience count or permit batching/fallback.
- Keep a participant map of role/person name to subagent id. If a participant must be invoked more than once, continue the same subagent with `send_input`. If the agent was closed but can be resumed, use `resume_agent` before sending input. Do not replace an existing participant with a fresh subagent unless the original cannot be resumed, and report that limitation.

Keep context boundaries strict:

- The Scout must not know the motion.
- Audience agents in the first poll must not see advocate arguments.
- Audience agents in the final poll must be the same subagents used for the first poll. They should see the same profile, their own first vote and reasoning, and the debate summary with openings and rebuttals.
- Advocates may use web research. Audience agents should not browse broadly unless the user explicitly changes the format.

Use subagents in parallel where possible:

- After the Scout returns profiles, run Moderator, Media Environment Mapper, and both Advocates in parallel when their inputs are ready.
- Run audience first-poll agents in parallel, one subagent per audience member.
- Run audience final-poll turns in parallel by sending input to the same audience subagents after the debate summary is ready.

Recommended subagent prompts:

Audience Scout:

```text
You are the Audience Scout for a synthetic debate simulation. You do not know the motion. Create N plausible audience profiles from the allowed population. For each: name, place, occupation/class, family/community context, political memory, emotional drivers, trusted media/social circles, default priors, persuasion triggers, and polling weight. Weights must sum to 100.
```

Moderator:

```text
You are the Moderator for a synthetic debate simulation. Research the motion, identify the real issues that should frame the debate, define the debate agenda, and note major factual claims that should be checked. This is not an empirical poll.
```

Media Environment Mapper:

```text
Map what each audience profile is likely to have already seen or heard about this motion through cohort-appropriate media, social networks, community cues, and lived experience. Do not produce a broad neutral research brief.
```

Advocate:

```text
You are the advocate for SIDE. Research and present the strongest case for SIDE on the motion. Address this audience's likely concerns. Include likely counters to the other side. Cite important factual sources. Remain available as the same advocate for later rebuttal or closing turns.
```

Audience first poll:

```text
You are this audience member, not a neutral analyst. Use only your profile and likely media/social exposure. Before hearing the debate, vote SIDE A, SIDE B, or undecided, and give the main driver. Remember this first vote and reasoning for your later final poll.
```

Audience final poll:

```text
You are the same audience member continuing from your first poll. Given your first vote, your first-vote reasoning, and the debate arguments/rebuttals, vote SIDE A, SIDE B, or undecided. Explain what moved you or why you held your position.
```

## Audience Rules

The audience must not be hand-picked for the debate outcome.

- Scout the audience before revealing the motion.
- Give each audience agent a short background: place, work, family or community context, political memory, emotional drivers, trusted media, social circles, default priors, and likely persuasion triggers.
- After the motion is revealed, do not ask audience agents to "research broadly and form an opinion." Instead, give them their likely information environment and ask what their current opinion is.
- Allow cohort-appropriate sources and cues. A student, trade worker, business owner, party worker, local activist, or professional should not all read the same broad source set.
- Audience agents may be `side A`, `side B`, or `undecided` in the first poll. Do not use confidence scores unless the user explicitly asks.

## Workflow

1. Get the motion and configuration.
2. Explain the simulation is synthetic and not empirical.
3. Spawn the topic-blind Audience Scout and create the audience.
4. Reveal the motion to the Moderator and Media Environment Mapper.
5. Spawn Moderator, Media Environment Mapper, Advocate A, and Advocate B as separate subagents.
6. Collect the Moderator agenda, media environment brief, and advocate research.
7. Spawn one first-poll audience subagent per named audience member. Store each audience subagent id with that person's profile, weight, first vote, and reasoning. Stop at Checkpoint 1.
8. Run advocate openings and the configured rebuttal rounds, using the same advocate subagents for every later turn. Use `send_input` to continue the existing advocate agents rather than spawning replacements. Stop at Checkpoint 2.
9. Send the final-poll prompt to the same audience subagents used for the first poll. Include each person's first vote and reasoning plus the shared debate summary. Use `resume_agent` first if an audience subagent was closed.
10. Judge by net audience swing and report final preference separately.
11. Create a standalone HTML record of the completed debate in the current working directory and include its local path in the final response.

If the user asks to stop at each checkpoint, pause after each checkpoint and wait for "go on" or equivalent.

## Checkpoint Format

Checkpoint 1: first poll

- Show weighted result: `side A`, `side B`, `undecided`.
- Show headcount result if useful.
- Show a compact table of audience agents, weights, first vote, and main driver.
- Do not include confidence scores by default.

Checkpoint 2: arguments and rebuttals

- Present each advocate's opening case.
- Present each rebuttal round clearly.
- Include the strongest line or core argument from each advocate.
- Include the likely pressure on undecided or soft-support audience segments.

Final checkpoint: result

- Show first poll, final poll, and net swing.
- Declare the winner by persuasion swing as "the advocate for X".
- If there is no movement, call the debate a tie, even if one side leads the final poll.
- Separately report which side the audience preferred overall in the final poll.
- Summarize what each side argued and what influenced the audience most.
- Create the required standalone HTML debate record after reporting the final result.

HTML debate record:

- Create a self-contained `.html` file with inline CSS and no external runtime dependencies.
- Use a descriptive kebab-case filename such as `debate-<topic>.html`.
- Include the motion, date context, synthetic-audience disclaimer, configuration, first poll, final poll, net swing, debate winner by swing, final audience preference, core arguments, rebuttal rounds, movement analysis, and cited sources.
- Use native `<details>` and `<summary>` sections for supporting detail such as factual frame, rebuttal rounds, audience movement, headcount derivation, source notes, or per-agent tables.
- Do not invent details that were not produced during the simulation. If only aggregate results were reported, label any derived headcount as derived from the aggregate poll.
- Keep the page readable as a record of the debate, not as a marketing landing page.

## Judging Rule

The debate winner is the advocate with the largest positive net swing from first poll to final poll.

Use this distinction:

- `Debate winner`: advocate with the biggest audience movement.
- `Final audience preference`: side with the largest final vote share.
- `Tie`: no net vote movement, or equal movement for both sides.

Example:

| Measure | Result |
|---|---|
| First poll leader | Side A |
| Final poll leader | Side A |
| Persuasion swing | 0 |
| Debate winner by swing | Tie |
| Audience preference overall | Side A |

## Research Rules

Use web research when the motion involves current facts, public figures, elections, law, economics, products, or any claim that may have changed recently.

- Advocates may research their assigned side.
- The Moderator may research the motion and verify major claims.
- The Media Environment Mapper may research what each cohort is likely to have seen.
- Audience agents should not perform broad neutral research unless the user explicitly changes the format.
- Cite sources used for factual framing.

## Output Style

Keep the simulation readable and direct. Do not overstate the result.

Use language like:

- "The advocate for KC won this simulated debate by swing."
- "VD remained the final audience preference, but the VD advocate did not win by persuasion."
- "This is a synthetic audience simulation, not a real voter poll."

Avoid language like:

- "KC won the election."
- "This proves voters prefer VD."
- "The simulation predicts the real outcome."
