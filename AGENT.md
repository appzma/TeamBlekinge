# AGENT.md

## Overview

This repository contains an **Autonomous Software Development Agent** — an AI-powered system capable of independently understanding, planning, writing, testing, and iterating on code to fulfill software development tasks. The agent operates with minimal human intervention, using a structured workflow to produce high-quality, working software.

---

## Table of Contents

- [What Is This Agent?](#what-is-this-agent)
- [Core Capabilities](#core-capabilities)
- [How It Works](#how-it-works)
- [Workflow](#workflow)
- [Tools & Integrations](#tools--integrations)
- [Interacting With the Agent](#interacting-with-the-agent)
- [Task Lifecycle](#task-lifecycle)
- [Decision Making](#decision-making)
- [Constraints & Guardrails](#constraints--guardrails)
- [Error Handling & Recovery](#error-handling--recovery)
- [Output & Deliverables](#output--deliverables)
- [Configuration](#configuration)
- [Best Practices for Task Prompts](#best-practices-for-task-prompts)
- [Limitations](#limitations)
- [Contributing](#contributing)

---

## What Is This Agent?

This agent is an **autonomous coding assistant** that operates as a self-directed engineer. Given a task, goal, or problem statement, it:

1. Analyzes the request and existing codebase context.
2. Plans a step-by-step approach.
3. Writes, modifies, and organizes code.
4. Runs tests and validates correctness.
5. Iterates based on results until the task is complete.
6. Summarizes what was done and why.

It is designed to act like a thoughtful, senior software engineer — not just generating code snippets, but reasoning about architecture, edge cases, maintainability, and correctness.

---

## Core Capabilities

| Capability | Description |
|---|---|
| **Code Generation** | Writes new functions, modules, classes, and full files from scratch |
| **Code Editing** | Modifies existing code with surgical precision |
| **Refactoring** | Restructures code for clarity, performance, or maintainability |
| **Debugging** | Identifies bugs, traces root causes, and applies fixes |
| **Testing** | Writes unit tests, integration tests, and validates behavior |
| **File Management** | Creates, moves, renames, and deletes files as needed |
| **Dependency Resolution** | Identifies and documents required dependencies |
| **Documentation** | Writes inline comments, docstrings, READMEs, and changelogs |
| **Architecture Planning** | Proposes and reasons about system design decisions |
| **Multi-step Reasoning** | Breaks complex tasks into sequential, verifiable steps |

---

## How It Works

The agent operates on a **perceive → plan → act → verify** loop:

```
┌─────────────────────────────────────────────────────────────┐
│                        AGENT LOOP                           │
│                                                             │
│  ┌──────────┐    ┌──────────┐    ┌──────────┐    ┌───────┐ │
│  │ PERCEIVE │───▶│  PLAN    │───▶│   ACT    │───▶│VERIFY │ │
│  └──────────┘    └──────────┘    └──────────┘    └───┬───┘ │
│       ▲                                               │     │
│       └───────────────── iterate ─────────────────────┘     │
└─────────────────────────────────────────────────────────────┘
```

### Perceive
- Reads and understands the task prompt.
- Surveys the existing codebase: file structure, dependencies, patterns, conventions.
- Gathers relevant context (existing tests, configs, documentation).

### Plan
- Breaks the task into discrete, ordered steps.
- Identifies which files need to be created or modified.
- Anticipates edge cases, dependencies, and potential failure points.
- Selects an approach before writing any code.

### Act
- Executes the plan step by step.
- Writes or edits code in a consistent, idiomatic style.
- Creates supporting files (tests, docs, configs) as needed.
- Commits logical units of work progressively.

### Verify
- Reviews written code for correctness and completeness.
- Runs available tests or reasons through expected behavior.
- Checks that the output satisfies the original task requirements.
- Iterates if issues are found.

---

## Workflow

### Step 1: Task Intake
```
User provides a task description
        │
        ▼
Agent reads the full task and asks clarifying questions if needed
        │
        ▼
Agent confirms understanding before proceeding
```

### Step 2: Codebase Exploration
```
Agent scans repository structure
        │
        ▼
Agent reads relevant files (entry points, related modules, tests)
        │
        ▼
Agent identifies conventions, patterns, and constraints
```

### Step 3: Planning
```
Agent produces an internal plan:
  - Files to create/edit
  - Order of operations
  - Known risks or ambiguities
```

### Step 4: Execution
```
Agent implements changes file by file
        │
        ▼
Each change is purposeful and explained
        │
        ▼
Agent avoids unnecessary modifications outside task scope
```

### Step 5: Validation
```
Agent reviews all changes holistically
        │
        ▼
Agent checks for regressions, gaps, or inconsistencies
        │
        ▼
Agent iterates if needed
```

### Step 6: Summary
```
Agent produces a clear summary:
  - What was done
  - Why decisions were made
  - What to watch out for
  - Suggestions for follow-up
```

---

## Tools & Integrations

The agent has access to the following tools and capabilities:

### File Operations
- `Get_a_file_in_GitHub` — Read the contents of any file from the repository.
- `Create_a_file_in_GitHub` — Create a new file with specified contents.
- `Edit_a_file_in_GitHub` — Make targeted edits to existing files.

### Version Control
- `create_branch` — Create new branches for feature work.
- `create_PR` — Create pull requests with title, body, head, and base branch.

### Reasoning & Planning
- `think` — Analyze complex information, reason about decisions, and plan implementations.

---

## Interacting With the Agent

### Giving a Task

Provide a clear, specific task. The more context, the better. Examples:

**Too vague:**
> "Fix the app."

**Better:**
> "The `/api/users` endpoint returns a 500 error when the `email` field is missing from the request body. Add input validation that returns a 400 error with a descriptive message when required fields are absent."

**Even better (with context):**
> "The `/api/users` endpoint (in `src/routes/users.py`) returns a 500 error when required fields are missing. We use Pydantic for validation elsewhere in the codebase. Add validation consistent with our existing patterns and add a test case in `tests/test_users.py`."

### Asking for Explanations

At any point, you can ask:
- "Why did you make that decision?"
- "What are the trade-offs of this approach?"
- "Can you explain this code?"
- "What would happen if we did X instead?"

### Requesting Revisions

- "Change the function name to `process_items`."
- "Use a dictionary instead of a list for this lookup."
- "Simplify this — it's too complex for what we need."

### Asking for Alternatives

- "Show me two different ways to implement this."
- "What's a simpler version of this solution?"
- "What would this look like if we used async/await?"

---

## Task Lifecycle

```
 CREATED ──▶ IN_PROGRESS ──▶ REVIEW ──▶ COMPLETE
                │                          ▲
                └──── ITERATE/REVISE ──────┘
                │
                ▼
            BLOCKED (needs human input)
```

| State | Description |
|---|---|
| `CREATED` | Task has been received but not started |
| `IN_PROGRESS` | Agent is actively working on the task |
| `REVIEW` | Agent has completed work and is summarizing |
| `ITERATE` | Agent is refining based on feedback or test failures |
| `BLOCKED` | Agent needs clarification or human decision |
| `COMPLETE` | Task is done and verified |

---

## Decision Making

The agent makes decisions using the following priorities:

1. **Correctness** — Code must be functionally correct and handle edge cases.
2. **Consistency** — Follow existing codebase conventions and patterns.
3. **Clarity** — Prefer readable, understandable code over clever code.
4. **Scope** — Stay focused on the task; avoid unnecessary changes.
5. **Safety** — Avoid destructive operations without explicit instruction.
6. **Performance** — Optimize only when necessary and justified.

### When the Agent Is Uncertain

If the agent encounters ambiguity, it will:
- State the ambiguity clearly.
- Propose the most reasonable interpretation.
- Proceed with that interpretation, noting the assumption.
- Flag it for human review.

The agent will **not** silently make arbitrary choices on critical decisions.

---

## Constraints & Guardrails

The agent operates within the following constraints:

### What the Agent Will Do
- ✅ Write, edit, and organize code.
- ✅ Read and analyze existing code.
- ✅ Refactor and improve existing code.
- ✅ Create and update documentation.
- ✅ Create branches, commits, and pull request descriptions.
- ✅ Follow the workflow: read → plan → implement → test → iterate

### What the Agent Will Not Do
- ❌ Push code that has not passed QA testing.
- ❌ Delete large amounts of code without explicit instruction.
- ❌ Modify secrets, credentials, or sensitive configuration.
- ❌ Make irreversible infrastructure changes without confirmation.
- ❌ Bypass security controls or introduce known vulnerabilities.
- ❌ Ignore explicit instructions from the user.

---

## Error Handling & Recovery

When something goes wrong:

### Test Failures
```
Test fails
    │
    ▼
Agent reads the error output
    │
    ▼
Agent traces the root cause
    │
    ▼
Agent applies a fix
    │
    ▼
Agent re-runs the test
    │
    ▼
Repeat until tests pass
    │
    ▼
If still failing: report to user with analysis
```

### Unexpected File States
- Agent will read the current state before making assumptions.
- Agent will not overwrite files blindly.
- Agent will flag unexpected states rather than silently patch them.

### Ambiguous Requirements
- Agent will surface the ambiguity.
- Agent will suggest the most reasonable default.
- Agent will ask for clarification if the decision is high-impact.

---

## Output & Deliverables

For each completed task, the agent produces:

### Code Changes
- All new or modified files, ready to review.
- Clean, well-structured diffs.
- Inline comments where the code is non-obvious.

### Explanation
- A plain-English summary of what was changed and why.
- Notes on any assumptions made.
- Trade-offs or alternatives considered.

### Tests
- Testing through QA workflow to verify functionality.
- Notes on what validation was performed.

### Documentation (when applicable)
- Updated docstrings and inline comments.
- Updated README or relevant docs if public-facing behavior changed.

---

## Configuration

The agent follows a strict workflow:
1. **Read** relevant files from GitHub to understand the existing codebase
2. **Use the think tool** to plan implementation
3. **Write or edit** the necessary files
4. **Hand off to QA agent** for testing
5. **If tests pass**, push and create a PR
6. **If tests fail**, fix the issues and repeat from step 4

**Never push code that has not passed QA.**

---

## Best Practices for Task Prompts

To get the best results from the agent, follow these guidelines:

### Be Specific
- Name the files, functions, or endpoints involved.
- Describe the expected input and output.
- Provide example data or error messages when relevant.

### Provide Context
- Mention the language, framework, or library in use.
- Link to related files or prior work.
- Describe who the end user is and what they need.

### Define "Done"
- What does success look like?
- Are there tests the agent should write or pass?
- Is there a specific interface or API contract to satisfy?

### Specify Constraints
- "Don't change the public API."
- "Keep it under 50 lines."
- "No external dependencies."
- "Must be backward compatible."

### Example Prompt Template

```
## Task
[One-sentence summary of what needs to be done]

## Context
- File(s) involved: [list files]
- Framework/Language: [e.g., Python 3.11, FastAPI]
- Related functionality: [describe anything that touches this area]

## Requirements
- [Requirement 1]
- [Requirement 2]
- [Requirement 3]

## Definition of Done
- [ ] [Acceptance criterion 1]
- [ ] [Acceptance criterion 2]

## Constraints
- [Any limitations or guardrails]
```

---

## Limitations

The agent is powerful but has known limitations:

| Limitation | Notes |
|---|---|
| **Context window limits** | Very large codebases may require selective file loading |
| **No long-term memory** | Each session starts fresh unless context is explicitly provided |
| **Cannot run code locally** | Relies on GitHub file operations and QA agent for testing |
| **Scope creep risk** | Complex tasks may need to be broken into smaller chunks |
| **No human judgment on business logic** | The agent optimizes for correctness, not business decisions |
| **Python and JavaScript/TypeScript only** | Designed to work with Python and JS/TS projects |
| **Docker-based execution** | All code must be runnable in Docker and accessible via the web |

---

## Contributing

If you are improving or extending this agent system:

1. **Document new tools** — Add any new agent tools to the [Tools & Integrations](#tools--integrations) section.
2. **Update constraints** — If behavior guardrails change, reflect them in [Constraints & Guardrails](#constraints--guardrails).
3. **Add examples** — Real-world task examples are invaluable for understanding agent behavior.
4. **Test edge cases** — Document any discovered edge cases and how the agent handles them.
5. **Keep this file current** — `AGENT.md` should always reflect the actual behavior of the agent.

---

## Changelog

| Version | Date | Notes |
|---|---|---|
| 1.0.0 | 2025-01-XX | Initial AGENT.md created |

---

*This document describes the design, behavior, and usage of the autonomous software development agent. For questions, issues, or improvements, open an issue or pull request in this repository.*
