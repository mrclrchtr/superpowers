# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Commands

### Claude Code skill tests
```bash
# Run the main Claude Code skill test suite
./tests/claude-code/run-skill-tests.sh

# Run a specific Claude Code skill test
./tests/claude-code/run-skill-tests.sh --test test-subagent-driven-development.sh

# Run Claude Code integration tests (slow; uses real Claude sessions)
./tests/claude-code/run-skill-tests.sh --integration
```

### Skill-triggering tests
```bash
# Run all naive-prompt skill triggering tests
./tests/skill-triggering/run-all.sh

# Run one triggering test
./tests/skill-triggering/run-test.sh <skill-name> ./tests/skill-triggering/prompts/<skill-name>.txt [max-turns]
```

### Explicit skill request tests
```bash
# Run explicit skill invocation tests
./tests/explicit-skill-requests/run-all.sh
```

### OpenCode plugin tests
```bash
# Run OpenCode plugin tests
./tests/opencode/run-tests.sh

# Run OpenCode integration tests
./tests/opencode/run-tests.sh --integration
```

### Analyze Claude session transcripts
```bash
python3 tests/claude-code/analyze-token-usage.py ~/.claude/projects/<encoded-project-dir>/<session-id>.jsonl
```

## Architecture

This repository is a plugin/skills library for coding agents, not a conventional application package. There is no root `package.json`; the primary artifacts are markdown skills, plugin metadata, hooks, and test harnesses.

- `skills/`: core product. Each skill lives in its own directory with a `SKILL.md` file and optional supporting files. Skills are discovered by host agents from these files.
- `commands/`: reusable prompt documents invoked by the plugin/skill workflow.
- `agents/`: agent prompt definitions used by hosts that support agent registration.
- `hooks/`: Claude Code hook configuration. `hooks/session-start` injects the `using-superpowers` bootstrap content at session start.
- `lib/skills-core.js`: shared logic for parsing skill frontmatter, finding skills recursively, resolving overrides/shadowing, and checking for updates.
- `.cursor-plugin/plugin.json`: Cursor/Claude plugin manifest wiring the `skills/`, `agents/`, `commands/`, and `hooks/` directories into the host.
- `.opencode/plugins/superpowers.js`: OpenCode adapter that injects bootstrap context and maps Claude-oriented concepts to OpenCode equivalents.

## Key conventions

- Skills are defined by `SKILL.md` files with YAML frontmatter. The most important frontmatter fields are `name` and `description`.
- `skills/using-superpowers/SKILL.md` is the bootstrap skill: it tells the agent to check for relevant skills before taking action.
- This repo’s behavior is heavily workflow-driven: brainstorming, planning, TDD, code review, worktrees, and branch finishing are treated as mandatory skill-guided flows rather than optional guidance.
- Tests are mostly integration-style shell scripts that run real Claude Code or OpenCode sessions and inspect transcripts/logs, rather than relying only on isolated unit tests.
- Some tests assume the repo path matters for plugin discovery, so prefer running the provided scripts from the repository root unless a script explicitly changes directories itself.

## Important references

- `README.md`: primary product overview, installation paths, and the expected end-to-end workflow.
- `docs/testing.md`: how Claude Code integration tests work, required settings, transcript locations, and token usage analysis.
- `.codex/INSTALL.md` and `docs/README.opencode.md`: host-specific installation and architecture notes for Codex and OpenCode.
- `skills/writing-skills/SKILL.md`: authoring conventions for new or edited skills.
