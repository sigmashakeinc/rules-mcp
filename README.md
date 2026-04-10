# rules-mcp

Security, safety, and efficiency rules for AI agents calling SigmaShake MCP (Model Context Protocol) server tools. Blocks rule injection attacks, API key exposure in audit logs, accidental hub publishing, and token-wasting query patterns.

**14 rules · 3 files**

![rules-mcp — AI agent MCP server governance demo](demo.cast)

> [▶ Watch interactive demo on SigmaShake Hub](https://hub.sigmashake.com/ruleset/rules-mcp)


## Install

```bash
ssg hub pull rules-mcp
```

Available on the [SigmaShake Hub](https://hub.sigmashake.com) — the open registry for AI agent governance rules. Compatible with Claude Code, GitHub Copilot, Cursor, Windsurf, Aider, and any AI coding agent using the `ssg` hook protocol.

## Rules

### mcp_any_security.rules — MCP tool security (5 rules)

| Rule | Decision | Severity | Description |
|------|----------|----------|-------------|
| `no-eval-inline-allow` | DENY | error | Blocks inline rules containing ALLOW (rule injection attack) |
| `no-eval-inline-force` | DENY | error | Blocks inline rules containing FORCE (rule injection vector) |
| `no-hub-submit-bad-url` | DENY | error | Requires valid `https://github.com/owner/repo` URL for hub submissions |
| `no-token-in-eval-input` | DENY | error | Blocks GitHub tokens and API keys in eval payloads (persisted in audit log) |
| `ask-hub-submit` | ASK | warning | Human approval before publishing a ruleset to the hub |

### mcp_any_safety.rules — Quality and correctness (5 rules)

| Rule | Decision | Severity | Description |
|------|----------|----------|-------------|
| `ask-large-audit-query` | ASK | warning | Confirms need for audit queries with limit >= 50 |
| `ask-eval-empty-input` | ASK | warning | Catches eval calls with empty tool name or empty input object |
| `force-rule-id-format` | FORCE | warning | Enforces kebab-case rule_id format for rules_get |
| `log-eval-call` | LOG | info | Logs every eval invocation for session observability |
| `log-audit-no-filter` | LOG | info | Notes unfiltered audit queries and recommends filtering |

### mcp_any_efficiency.rules — Cost and efficiency (4 rules)

| Rule | Decision | Severity | Description |
|------|----------|----------|-------------|
| `log-status-call` | LOG | info | Tracks health checks; reminds to avoid polling |
| `log-rules-list-call` | LOG | info | Tracks rules_list calls; recommends caching |
| `log-rules-get-call` | LOG | info | Tracks individual rule fetches; recommends batching |
| `log-audit-query` | LOG | info | Tracks audit queries; notes point-in-time nature |

## Why this matters

MCP tools expose governance infrastructure directly to AI agents. Without guardrails:

- **Rule injection**: An agent could craft inline ALLOW rules passed to `sigmashake_eval` to override existing DENY rules, defeating the entire governance layer
- **Secret leakage**: GitHub tokens passed through eval inputs are persisted in the audit log as plaintext — tokens belong only in `sigmashake_hub_submit`
- **Accidental publishing**: Hub submissions without human review could publish broken or internal rulesets publicly
- **Context waste**: Unfiltered audit queries and repeated status checks consume LLM tokens without adding value

These rules enforce defense-in-depth: security rules block dangerous patterns outright, safety rules add human checkpoints for risky operations, and efficiency rules provide observability without interrupting the agent.

## About

Part of the [SigmaShake Hub](https://hub.sigmashake.com) — open-source governance rules for AI coding agents.
Install the `ssg` CLI to enforce these rules: `npm install -g @sigmashake/ssg`
