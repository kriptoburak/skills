---
name: hermes-tweet-harbor-research
description: "Create reproducible Harbor tasks and adapters from X/Twitter social-signal research collected with the Hermes Tweet plugin. Use when evaluating agents on launch monitoring, social listening, support triage, creator research, or community-audit workflows while keeping live credentials out of benchmark tasks."
---

# Hermes Tweet Harbor Research

Use this skill when a Harbor task or adapter needs realistic X/Twitter social
signal fixtures gathered through
[Hermes Tweet](https://github.com/Xquik-dev/hermes-tweet), a native Hermes
Agent plugin.

## Scope

This skill does not run live social actions inside Harbor tasks. Instead, use
Hermes Tweet to collect or refresh sanitized fixtures before task generation,
then make the Harbor task deterministic.

Good fits:

- Launch-monitoring evaluations.
- Social-listening and trend-research tasks.
- Support-triage tasks from public mentions or timelines.
- Creator, brand, or competitor research tasks.
- Giveaway and community-audit evidence tasks.

## Collect Fixtures

Install Hermes Tweet in the Hermes runtime:

```bash
hermes plugins install Xquik-dev/hermes-tweet --enable
```

Keep action routes disabled while collecting benchmark fixtures:

```bash
export HERMES_TWEET_ENABLE_ACTIONS=false
```

Use `tweet_explore` first, then `tweet_read` for catalog-listed read routes.
Save only the fields needed by the evaluation, such as post text, public URLs,
timestamps, public counts, and sanitized author labels.

Do not store API keys, cookies, private account details, DMs, or raw runtime
credentials in Harbor task directories.

## Choose Task or Adapter

Use a direct Harbor task when the dataset is small or hand-curated:

```bash
harbor tasks init social-listening-fixture
```

Use a Harbor adapter when many fixture records should become many tasks:

```bash
harbor adapters init social-listening-fixtures
```

Prefer adapters when you need parity tracking, repeated fixture refreshes, or a
published dataset citation.

## Task Authoring Pattern

1. Put sanitized fixture files under `environment/fixtures/`.
2. Copy them into the image from `environment/Dockerfile`.
3. Write `instruction.md` so the agent analyzes fixture files, not live X.
4. Keep `tests/test.sh` focused on deterministic output checks.
5. Write `/logs/verifier/reward.txt` in every verifier path.

The instruction should not mention hidden tests, runtime API keys, or live
social accounts. It should specify exact input files and output schema.

## Adapter Pattern

For batch conversion, implement the adapter around local fixture files:

- `adapter.py` reads sanitized JSONL or CSV records.
- `make_local_task_id` creates stable task IDs from fixture IDs.
- `generate_task` writes one Harbor task per record or scenario.
- `README.md` documents fixture source, collection date, license constraints,
  privacy filtering, and citation.
- `parity_experiment.json` records the reference behavior being preserved.

Validate after generation:

```bash
harbor adapters validate adapters/social-listening-fixtures
harbor tasks check datasets/social-listening-fixtures
```

## Safety Gates

- Keep `HERMES_TWEET_ENABLE_ACTIONS=false` during fixture collection.
- Use `tweet_action` only outside Harbor and only with explicit human approval.
- Do not include credentials, private messages, private account metadata, or
  unredacted user PII in tasks.
- Snapshot enough public context to make the task reproducible without network
  access.
- If a fixture cannot be shared safely, replace it with a synthetic record that
  preserves the evaluation shape.

## References

- Hermes Tweet:
  <https://github.com/Xquik-dev/hermes-tweet>
- Harbor task creation:
  `skills/harbor-task-creator/SKILL.md`
- Harbor adapter creation:
  `skills/harbor-adapter-creator/SKILL.md`
