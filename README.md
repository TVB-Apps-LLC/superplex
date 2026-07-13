# Superplex

Superplex is a complete software development methodology for your coding agents, built on top of a set of composable skills and some initial instructions that make sure your agent uses them.

> **This is a fork.** `superplex` is [TVB Apps LLC](https://github.com/TVB-Apps-LLC)'s internal fork of [Superpowers](https://github.com/obra/superpowers), maintained as our own custom orchestrator. It is not affiliated with the upstream project's maintainers and is not accepting outside contributions. For the original project, see [obra/superpowers](https://github.com/obra/superpowers).

## Quickstart

Give your agent Superplex: [Claude Code](#claude-code), [Antigravity](#antigravity), [Codex App](#codex-app), [Codex CLI](#codex-cli), [Cursor](#cursor), [Factory Droid](#factory-droid), [GitHub Copilot CLI](#github-copilot-cli), [Kimi Code](#kimi-code), [OpenCode](#opencode), [Pi](#pi).

## How it works

It starts from the moment you fire up your coding agent. As soon as it sees that you're building something, it *doesn't* just jump into trying to write code. Instead, it steps back and asks you what you're really trying to do. 

Once it's teased a spec out of the conversation, it shows it to you in chunks short enough to actually read and digest. 

After you've signed off on the design, your agent puts together an implementation plan that's clear enough for an enthusiastic junior engineer with poor taste, no judgement, no project context, and an aversion to testing to follow. It emphasizes true red/green TDD, YAGNI (You Aren't Gonna Need It), and DRY. 

Next up, once you say "go", it launches a *subagent-driven-development* process, having agents work through each engineering task, inspecting and reviewing their work, and continuing forward. It's not uncommon for your agent to work autonomously for a couple hours at a time without deviating from the plan you put together.

There's a bunch more to it, but that's the core of the system. And because the skills trigger automatically, you don't need to do anything special. Your coding agent just has Superplex.

## Installation

Installation differs by harness. If you use more than one, install Superplex separately for each one.

`superplex` is not published to any harness's official or curated marketplace (those list the upstream Superpowers project, not this fork). Install directly from [TVB-Apps-LLC/superplex](https://github.com/TVB-Apps-LLC/superplex) using each harness's repo-based install method below.

### Claude Code

Register this repository as a marketplace and install the plugin:

```bash
/plugin marketplace add TVB-Apps-LLC/superplex
/plugin install superplex@superplex-dev
```

### Antigravity

Install Superplex as a plugin from this repository:

```bash
agy plugin install https://github.com/TVB-Apps-LLC/superplex
```

Antigravity runs the plugin's session-start hook, so Superplex is active from
the first message. Reinstall with the same command to update.

### Codex App / Codex CLI

Not currently published to the [official Codex plugin marketplace](https://github.com/openai/plugins) (that lists the upstream Superpowers project). Package a local install from this repo with `scripts/package-codex-plugin.sh` and load it per Codex's local/portal plugin instructions.

### Cursor

Not currently published to Cursor's plugin marketplace (that lists the upstream Superpowers project). Clone this repository and point Cursor at the local checkout.

### Factory Droid

- Register the marketplace:

  ```bash
  droid plugin marketplace add https://github.com/TVB-Apps-LLC/superplex
  ```

- Install the plugin:

  ```bash
  droid plugin install superplex@superplex
  ```

### GitHub Copilot CLI

Not currently published to a Copilot CLI marketplace (the upstream `obra/superpowers-marketplace` lists the original Superpowers project, not this fork). Clone this repository and point Copilot CLI at the local checkout.

### Kimi Code

Install directly from this repository:

```text
/plugins install https://github.com/TVB-Apps-LLC/superplex
```

- Detailed docs: [docs/README.kimi.md](docs/README.kimi.md)

### OpenCode

OpenCode uses its own plugin install; install Superplex separately even if you
already use it in another harness.

- Tell OpenCode:

  ```
  Fetch and follow instructions from https://raw.githubusercontent.com/TVB-Apps-LLC/superplex/refs/heads/main/.opencode/INSTALL.md
  ```

- Detailed docs: [docs/README.opencode.md](docs/README.opencode.md)

### Pi

Install Superplex as a Pi package from this repository:

```bash
pi install git:github.com/TVB-Apps-LLC/superplex
```

For local development, run Pi with this checkout loaded as a temporary package:

```bash
pi -e /path/to/superplex
```

The Pi package loads the Superplex skills and a small extension that injects the `using-superplex` bootstrap at session startup and again after compaction. Pi has native skills, so no compatibility `Skill` tool is required. Subagent and task-list tools remain optional Pi companion packages.

## The Basic Workflow

1. **brainstorming** - Activates before writing code. Refines rough ideas through questions, explores alternatives, presents design in sections for validation. Saves design document.

2. **using-git-worktrees** - Activates after design approval. Creates isolated workspace on new branch, runs project setup, verifies clean test baseline.

3. **writing-plans** - Activates with approved design. Builds a dependency-ordered PR map, optionally writes a separate test plan, and breaks each PR into bite-sized tasks with exact file paths and verification steps.

4. **subagent-driven-development** or **executing-plans** - Activates with plan. Executes one PR at a time with focused implementer subagents, then reviews the complete PR before moving to dependent work.

5. **test-driven-development** - Activates during implementation. Enforces RED-GREEN-REFACTOR: write failing test, watch it fail, write minimal code, watch it pass, commit. Review fixes must rerun their covering tests.

6. **requesting-code-review** - Activates after each PR. Reviews the full PR for spec compliance, code quality, and test coverage.

7. **receiving-code-review** and **finishing-a-development-branch** - Process PR feedback, monitor reviews/reactions/checks, resolve threads, squash-merge when the PR gate passes, and retain branches/worktrees until the complete task is validated.

**The agent checks for relevant skills before any task.** Mandatory workflows, not suggestions.

## What's Inside

### Skills Library

**Testing**
- **test-driven-development** - RED-GREEN-REFACTOR cycle (includes testing anti-patterns reference)

**Debugging**
- **systematic-debugging** - 4-phase root cause process (includes root-cause-tracing, defense-in-depth, condition-based-waiting techniques)
- **verification-before-completion** - Ensure it's actually fixed

**Collaboration** 
- **brainstorming** - Socratic design refinement
- **writing-plans** - Detailed implementation plans
- **executing-plans** - Batch execution with checkpoints
- **dispatching-parallel-agents** - Concurrent subagent workflows
- **requesting-code-review** - Pre-review checklist
- **receiving-code-review** - Responding to feedback
- **using-git-worktrees** - Parallel development branches
- **finishing-a-development-branch** - Merge/PR decision workflow
- **subagent-driven-development** - PR-oriented execution with focused implementer subagents, one whole-PR review, and a persistent feedback loop

**Meta**
- **writing-skills** - Create new skills following best practices (includes testing methodology)
- **using-superplex** - Introduction to the skills system

## Philosophy

- **Test-Driven Development** - Write tests first, always
- **Systematic over ad-hoc** - Process over guessing
- **Complexity reduction** - Simplicity as primary goal
- **Evidence over claims** - Verify before declaring success

Read [the original release announcement](https://blog.fsck.com/2025/10/09/superpowers/).

## Contributing

`superplex` is maintained internally by TVB Apps LLC and is not open to outside contributions. If you'd like to contribute skills or fixes to the original project, see the upstream [obra/superpowers](https://github.com/obra/superpowers) repository and its contribution guidelines.

Skill-behavior tests use the drill eval harness from [superpowers-evals](https://github.com/prime-radiant-inc/superpowers-evals/), cloned into `evals/` — see `evals/README.md` for setup. Plugin-infrastructure tests live at `tests/` and run via the relevant `run-*.sh` or `npm test`.

See `skills/writing-skills/SKILL.md` for the complete guide.

## Updating

Superplex updates are somewhat coding-agent dependent, but are often automatic.

## License

MIT License - see LICENSE file for details

## Visual companion

Brainstorming's optional visual companion shows a plain `Superplex vX.Y.Z` label linking back to this repository. Unlike upstream Superpowers, this fork does not fetch a remote logo or report usage/version to any third party — there is no telemetry to opt out of.

## Fork

`superplex` is maintained by [TVB Apps LLC](https://github.com/TVB-Apps-LLC) as an internal fork of Superpowers, originally created by [Jesse Vincent](https://blog.fsck.com) and [Prime Radiant](https://primeradiant.com). For upstream community, support, and release announcements, see the [original project](https://github.com/obra/superpowers).
