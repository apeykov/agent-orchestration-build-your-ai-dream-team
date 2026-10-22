# Project Pulse Agent Team

Mona's Project Pulse dashboard will be built by the custom agents in
`.github/agents/`. The Orchestrator owns coordination; the specialists retain
clear, non-overlapping file scopes.

| Agent | Model | Role |
| --- | --- | --- |
| [Orchestrator](../.github/agents/orchestrator.agent.md) | Claude Sonnet 5.5 | Converts the request into dependency-aware phases, delegates scoped work to the Planner, Designer, and Coder, coordinates integration, and reports blockers. It does not implement changes or perform Git operations. |
| [Planner](../.github/agents/planner.agent.md) | Claude Sonnet 5.5 | Researches the repository, relevant documentation, dependencies, and edge cases. It returns an implementation plan with file ownership, sequencing, parallel work, risks, validation expectations, and open questions; it does not write code. |
| [Designer](../.github/agents/designer.agent.md) | Gemini 3.8 Flash | Defines and implements assigned UX and visual-design work: accessibility, information hierarchy, responsive behavior, project cards, status badges, priority treatment, and deterministic dashboard CSS hooks. |
| [Coder](../.github/agents/coder.agent.md) | GPT-5.6 Terra | Implements assigned code with explicit errors and deterministic, testable behavior. For assigned runnable-app work, it can also create support configuration, including the Project Pulse `.vscode/launch.json`. |

## Delivery Flow

1. The Orchestrator first asks the Planner for a repository-informed implementation plan.
2. It assigns phases with explicit files and dependencies. Independent, non-overlapping work can run in parallel; overlapping or dependent work runs sequentially.
3. The Designer establishes the assigned Project Pulse experience, and the Coder implements its assigned application and runnable-app support.
4. The Orchestrator verifies that the plan, design, implementation, and validation results are consistent before handing the completed dashboard back to the learner.

All four agents leave staging, commits, and pushes to the learner.

Orchestrator, Planner, Coder, and Designer.
The model assigned to each agent.
The responsibility of each agent.
The .github/agents/ file for each agent.
How the team will work together to build Project Pulse.