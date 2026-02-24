# Prompts

For an overview of custom prompts, see [this documentation](https://developers.openai.com/codex/custom-prompts).

## Where prompts live in this repository

### Base system prompt

The primary system prompt sent to the model is:

- `codex-rs/protocol/src/prompts/base_instructions/default.md`

This file defines the agent's identity, personality, capabilities, AGENTS.md handling, planning behavior, and tool guidelines.

The text is embedded at compile time in `codex-rs/protocol/src/models.rs` via the `BASE_INSTRUCTIONS_DEFAULT` constant and is used as the default `BaseInstructions` for every session.

### Permission / sandbox prompts

Appended to the system prompt based on the active sandbox mode and approval policy:

```
codex-rs/protocol/src/prompts/permissions/
├── approval_policy/
│   ├── never.md
│   ├── on_failure.md
│   ├── on_request_rule.md
│   └── unless_trusted.md
└── sandbox_mode/
    ├── danger_full_access.md
    ├── read_only.md
    └── workspace_write.md
```

### Template prompts (core)

Runtime templates used for specific features, located in `codex-rs/core/templates/`:

| Path | Purpose |
|------|---------|
| `agents/orchestrator.md` | Multi-agent orchestration prompt |
| `collaboration_mode/*.md` | Collaboration mode instructions (default, execute, pair\_programming, plan) |
| `collab/experimental_prompt.md` | Experimental collaboration features |
| `compact/prompt.md` | Context compaction (checkpoint) prompt |
| `compact/summary_prefix.md` | Prefix for compaction summaries |
| `memories/consolidation.md` | Memory consolidation prompt |
| `memories/stage_one_system.md` | Memory extraction stage-one system prompt |
| `memories/stage_one_input.md` | Memory extraction stage-one user prompt |
| `memories/read_path.md` | Memory read-path template |
| `model_instructions/gpt-5.2-codex_instructions_template.md` | Model-specific instruction template |
| `personalities/gpt-5.2-codex_*.md` | Personality variants for supported models |
| `review/exit_success.xml` | Review prompt on successful exit |
| `review/exit_interrupted.xml` | Review prompt on interrupted exit |
| `review/history_message_*.md` | History message templates for review |
| `search_tool/tool_description.md` | File-search tool description injected into the system prompt |

## Custom (user-defined) prompts

Place Markdown (`.md`) files in `~/.codex/prompts/` to create custom slash commands available in the TUI.

```
~/.codex/prompts/
├── review.md          # invoked with /prompts:review
└── explain.md         # invoked with /prompts:explain
```

Each file may start with optional YAML frontmatter:

```markdown
---
description: "Short description shown in the slash popup"
argument-hint: "[file] [priority]"
---
Prompt body. Use $1, $2, or $ARGUMENTS for user-supplied arguments.
```

Files without frontmatter are also valid — their entire content becomes the prompt body.

The discovery logic lives in `codex-rs/core/src/custom_prompts.rs`.

## Overriding the base system prompt

To replace the built-in base system prompt entirely, set `model_instructions_file` in `~/.codex/config.toml`:

```toml
model_instructions_file = "/path/to/my_instructions.md"
```

> **Warning:** Deviating from the built-in instructions may degrade agent behavior. This option is intended for advanced use cases only.
