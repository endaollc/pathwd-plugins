---
name: pathwd-pathways
description: "PATH WD pathways: what a pathway is and what its steps do, which pathways a user can run on a record or from the dashboard, running a pathway screen by screen (pathway-run start, submit, act), what each kind of screen asks and how it is answered, execute-pathway for pathways without screens, and reading a run's log. Covers requests like \"run the Win Job pathway on Job 12345\" or \"what would this pathway ask me for?\"."
---

# Pathways in PATH WD

A pathway is a guided, step-by-step process a user runs on a record or from the dashboard, such as "Win Job" or "Send to Accounting". Its steps show screens (questions, forms, choices), save data, record workflow steps, fire actions and create or link records. Pathway screens save real data when they are submitted.

On the PATH WD connector, `path_pathways` reads pathways and runs (which pathways are available, a pathway's map, a run's state and log) and `path_run_pathway` starts and answers runs. A run moves one screen at a time: it pauses at each screen that needs input, `pathway-run submit` answers the paused screen and continues the run, and `pathway-run act` performs a non-advancing action on it, such as a lookup search. `execute-pathway` runs a pathway on PATH's headless runner, the way scheduled pathways and integrations run, with no screens. Building and editing pathways is done in PATH's Designer.

The account, session and the connector's tools are described in the **pathwd-guide** skill.

## Guides

Step-by-step workflows:

- [pathways (workflow)](references/workflows/pathways.md): Use when the user wants to find, list, check availability of, run or test pathways in PATH.

Reference files:

- [Pathway screens: how to answer each one](references/pathway-screens.md): A pathway run (`pathway-run start`) runs every automatic step and stops at each screen that needs a person.
- [Pathway steps: every step type and what it does](references/pathway-steps.md): A pathway is built from steps, and every step is one of PATH's step types: Edit Record, Fork, Send Email and so on.
- [Pathways: What Each Screen Asks and What Answering It Does](references/pathway-concepts.md): A pathway is a guided, step-by-step process, such as "Select Winning Quote" or "New Full Quote", that walks a user through a piece of business in a fixed order.

## Commands

| Command | Tool | What it does |
|---------|------|--------------|
| [`execute-pathway`](references/commands/execute-pathway.md) | `path_run_pathway` | Run a pathway on a record on PATH's headless runner, the way scheduled pathways and integrations run: no screens, so it suits a pathway whose steps… |
| [`pathway-run act`](references/commands/pathway-run-act.md) | `path_run_pathway` | A non-advancing action on the paused screen, e.g. a lookup search: --set search=Acme [--set page=2] |
| [`pathway-run available`](references/commands/pathway-run-available.md) | `path_pathways` | Which pathways a user could start from a launch point (a record's Pathways tab, the dashboard, a folder or a task), each with its designer context… |
| [`pathway-run cancel`](references/commands/pathway-run-cancel.md) | `path_run_pathway` | Cancel a paused run (like closing the web modal) |
| [`pathway-run list`](references/commands/pathway-run-list.md) | `path_pathways` | List saved pathway runs, newest first |
| [`pathway-run log`](references/commands/pathway-run-log.md) | `path_pathways` | A run's trace: steps, busy messages, every server request and reply |
| [`pathway-run map`](references/commands/pathway-run-map.md) | `path_pathways` | Map a pathway before running it (read-only, nothing runs): every step in order with what its screen shows and asks for (fields, choices, question… |
| [`pathway-run show`](references/commands/pathway-run-show.md) | `path_pathways` | Show a run: the screen it is waiting on, or how it ended |
| [`pathway-run start`](references/commands/pathway-run-start.md) | `path_run_pathway` | Start a pathway run, exactly as the web app runs it; stops at the first screen that needs input |
| [`pathway-run submit`](references/commands/pathway-run-submit.md) | `path_run_pathway` | Answer the paused screen (only what that screen allows) and continue the run |
| [`run-step`](references/commands/run-step.md) | `path_run_pathway` | Run a workflow step on a record (triggers milestone evaluation) |
