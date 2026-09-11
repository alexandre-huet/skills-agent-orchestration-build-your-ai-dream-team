# Agent team

To build Mona's Project Pulse dashboard, I'm using a four-agent custom team defined under `.github/agents/`, orchestrated with GitHub Copilot CLI running in a Codespace.

## Orchestrator

- **Model:** Claude Opus 4.7 (copilot)
- **Responsibility:** Coordinates the Planner, Coder, and Designer agents. Breaks the request into phases, assigns non-overlapping file scopes, decides what can run in parallel vs. sequentially, and reports final outcomes. Does not implement work itself.
- **Definition:** `.github/agents/orchestrator.agent.md`

## Planner

- **Model:** Claude Opus 4.7 (copilot)
- **Responsibility:** Researches the codebase, docs, dependencies, and edge cases to produce an implementation plan with ordered steps, file assignments, dependencies, parallelizable work, and validation expectations. Does not write code.
- **Definition:** `.github/agents/planner.agent.md`

## Coder

- **Model:** GPT-5.5 (copilot)
- **Responsibility:** Implements code and logic within the file scope assigned by the Orchestrator, including support configuration like `.vscode/launch.json` for running the Project Pulse app. Keeps behavior deterministic, testable, and validated before reporting completion.
- **Definition:** `.github/agents/coder.agent.md`

## Designer

- **Model:** Gemini 3.1 Pro (copilot)
- **Responsibility:** Handles UI/UX, accessibility, information architecture, and visual design for Project Pulse — producing a polished dashboard with project cards, status badges, and deterministic CSS hooks (`.dashboard`, `.project-card`).
- **Definition:** `.github/agents/designer.agent.md`

## Orchestration note

I am using GitHub Copilot CLI inside a Codespace to orchestrate this agent team — the Orchestrator agent delegates to Planner, Coder, and Designer, while I (the learner) retain control of all git operations.
