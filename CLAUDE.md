# superplex — Fork Notice

`superplex` is [TVB Apps LLC](https://github.com/TVB-Apps-LLC)'s internal fork of [Superpowers](https://github.com/obra/superpowers), maintained as our own custom orchestrator.

This fork is managed internally and does not accept outside contributions. The upstream project's contributor guidelines (PR requirements, acceptance criteria for new harnesses, skill-change evaluation process, etc.) applied to submissions against the original `obra/superpowers` repository and are not reproduced here. If you want to contribute to the original project, see [obra/superpowers](https://github.com/obra/superpowers) and its own `CLAUDE.md`.

## Eval harness

Skill-behavior evals live in [superpowers-evals](https://github.com/prime-radiant-inc/superpowers-evals/), cloned into `evals/` — see `evals/README.md` for setup. The harness drives real tmux sessions of Claude Code / Codex and judges skill compliance with an LLM verifier. Plugin-infrastructure tests still live at `tests/`.
