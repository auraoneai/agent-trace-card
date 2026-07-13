# agent-trace-card

`agent-trace-card` turns one local agent trace JSON file into a portable review
card for incident discussion, handoff, and regression planning.

## At a Glance

| | |
| --- | --- |
| Job | Summarize an agent run as Markdown, HTML, or schema-versioned JSON. |
| Built for | Agent developers, eval engineers, incident reviewers, and governance teams. |
| Differentiator | Small, dependency-free CLI with deterministic heuristics and a checked-in v1 schema. |
| Produces | Goal, outcome, tools, retries, selected identifiers, failure modes, human intervention, and regression status. |

## Install

The current registry release is:

```bash
python -m pip install "agent-trace-card==0.1.2"
```

## Verified Quickstart

Run from a source checkout so the synthetic fixture is available:

```bash
agent-trace-card generate \
  --from examples/refund_trace.json \
  --format json \
  --out /tmp/refund-agent-card.json

agent-trace-card validate /tmp/refund-agent-card.json
```

The bundled refund fixture produces a valid `agent-trace-card/v1` document with
outcome `failed` and retry count `1`. Use `--format markdown` or `--format html`
for a shareable human-readable artifact.

## What the Generator Infers

- repeated calls to a small built-in set of mutating tool names;
- tool events that contain an `error`;
- an incorrect terminal-answer signal when the final answer contains `twice`;
- selected `order_id`, `user_id`, `account_id`, `ticket_id`, and `file_path`
  values from tool arguments.

These are deterministic review heuristics, not semantic evaluation of the
agent, policy, or outcome.

## Runtime, Data, and Network Boundary

- The CLI reads one local JSON file and writes one local artifact.
- It does not call a model, execute a tool, start a server, or make a network
  request.
- It does not redact input. Generated cards can preserve identifiers and links,
  so sanitize traces before generation and review the output before sharing it.
- Validation checks the v1 card shape and selected field types; it does not
  prove that the source trace or card claims are complete or correct.

## Limitations

- The generator uses fixed heuristics over recorded fields; it does not infer
  intent, policy compliance, or business correctness.
- Validation proves schema shape only. It does not establish that a card is
  complete, accurate, or safe to share.

## Compatibility

The published `auraone-agent-studio-open` CLI declares
`agent-trace-card>=0.1.1` as a runtime dependency and exposes a `trace-card`
export path. Other trace systems can use the standalone CLI by emitting the
documented local JSON shape.

## Publication Status

Verified on 2026-07-13:

- PyPI: [`agent-trace-card==0.1.2`](https://pypi.org/project/agent-trace-card/0.1.2/)
- GitHub release: [`v0.1.2`](https://github.com/auraoneai/agent-trace-card/releases/tag/v0.1.2)
- Bundled traces are synthetic tutorial fixtures, not evidence of external
  adoption or production performance.

## Next Action

Generate a card from one sanitized failure trace, attach it to the matching
incident or regression issue, and record which fields still need human context.
