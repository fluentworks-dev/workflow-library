# Workflow library

System-provided workflows, agents and rules for [Devel Central](https://develcentral.com).

The workflows here are **external workflows**. A board column runs one in place: nothing is copied
into the organization, and the column stays linked to this repository's `main` branch. A new native
project picks a board template (see [Templates](#templates)), and each template column starts on its
external workflow. Any column can also pick an external workflow from its **Workflow** menu, under
**External: &lt;Template&gt;**.

Every session reads the workflow, its agent and the agent's instructions as they are on `main` when
the session launches, and keeps that version until it ends. External workflows are read only in the
app. To change one, a member duplicates it: the organization gets its own editable copy of the
workflow, the agent that runs it and any rules that target them, named "Name (Copy)" when the name
is taken. A copy never changes when this repository does.

The app reads the `main` branch through `raw.githubusercontent.com`, which caches files for about
five minutes, so a change merged here reaches the app within a few minutes. Rename a workflow rather
than removing it: a column bound to a removed workflow starts nothing, and its past sessions show an
unknown workflow.

## Namespaces

| Folder | Shown as | Workflows |
| --- | --- | --- |
| [`default/`](default/) | Default workflows | Scoping, Design, Plan, Implement, Review, Functional test, Merge |

Each top-level folder holding a `namespace.json` is a namespace in this layout. Devel Central reads
only `default/` today; a new namespace folder does not appear in the app yet.

## Layout

```
<namespace>/
  namespace.json
  workflows/<workflow>/
    workflow.json
    stages/<file>.md
  agents/<agent>/
    agent.json
    instructions/<file>.md
  rules/<file>.md
```

Folder names under `workflows/` and `agents/` are keys: lowercase letters, digits and dashes
(`implement`, `software-engineer`). Workflows and rules refer to each other by these keys. Every
`file` path is relative to the JSON file that names it, ends in `.md` and stays inside the
namespace (no `..`). Markdown bodies are copied as written, trimmed of surrounding blank lines.

### namespace.json

```json
{
  "name": "Default workflows",
  "workflows": ["scoping", "design", "plan", "implement", "review", "merge"],
  "rules": [
    { "name": "Ship small", "file": "rules/ship-small.md", "appliesTo": "workflows", "targets": ["implement"] }
  ],
  "templates": [
    {
      "key": "software-development",
      "name": "Software development",
      "columns": [
        { "name": "Backlog", "type": "backlog" },
        { "name": "Implement", "type": "started", "workflow": "implement" },
        { "name": "Merge", "type": "completed", "workflow": "merge" }
      ]
    }
  ]
}
```

| Field | Required | Meaning |
| --- | --- | --- |
| `name` | yes | The namespace's name. |
| `workflows` | yes | Workflow keys. A workflow folder not listed here is ignored. |
| `rules` | no | Rules that target workflows or agents of this namespace; see below. Defaults to none. |
| `templates` | no | Board templates; see below. Defaults to none. |

### workflows/&lt;workflow&gt;/workflow.json

```json
{
  "name": "Implement",
  "description": "Build the ticket as scoped and designed upstream.",
  "agent": "software-engineer",
  "model": "opus",
  "permissions": "auto",
  "stages": [
    { "name": "Understand", "file": "stages/1-understand.md" },
    { "name": "Build", "file": "stages/2-build.md" }
  ]
}
```

| Field | Required | Meaning |
| --- | --- | --- |
| `name` | yes | The workflow's name. When the organization already has one by that name, a duplicate is named "Implement (Copy)", then "Implement (Copy 2)". |
| `description` | no | One paragraph: what the workflow is for. |
| `agent` | yes | The key of the agent under `agents/` that runs it. Several workflows may share one agent. Duplicating a workflow copies its agent each time. |
| `model` | no | `inherit` (the member's Settings default, the default), an alias such as `opus` or `sonnet`, or a full model ID. |
| `permissions` | no | `auto` (the default) or `ask`. Auto needs a model that can run it. |
| `effort` | no | `inherit` (Claude Code decides, the default) or a level: `low`, `medium`, `high`, `xhigh` or `max`. An app that does not know a level still reads the library, shows the level as written, and asks to be updated before it duplicates the workflow. |
| `takes` | no | The ticket types the workflow takes, as a list of type ids: `"task"`, `"spike"`. Columns running the workflow take only these, for example `"takes": ["spike"]`. Leave it out to take every type. An app ignores an id it does not know, and reads a list with no id it knows as every type. |
| `stages` | yes | At least one, in order. Each stage's Markdown says what the agent does in it. |

### agents/&lt;agent&gt;/agent.json

```json
{
  "name": "Software Engineer",
  "description": "Builds changes in the codebase.",
  "instructions": [
    { "name": "Software Engineer: working discipline", "file": "instructions/working-discipline.md" }
  ]
}
```

| Field | Required | Meaning |
| --- | --- | --- |
| `name` | yes | The agent's name; a taken name gets the same "(Copy)" suffix as a workflow. |
| `description` | no | One paragraph: who the agent is. |
| `instructions` | no | The agent's own instructions, in order. Every session the agent runs gets them. |

### Rules

A rule is Markdown in `rules/`, listed in `namespace.json` with the workflows or agents it targets:

| Field | Required | Meaning |
| --- | --- | --- |
| `name` | yes | The rule's name. |
| `file` | yes | Its Markdown, relative to the namespace folder. |
| `appliesTo` | yes | `workflows` or `agents`. |
| `targets` | yes | Workflow keys or agent keys of this namespace. |

A rule is copied with any workflow or agent it targets when that workflow is duplicated, and targets
only the copies made by that duplicate. The copy belongs to the member who made it, like any rule
they write. Rules here do not apply to sessions running the external workflow itself; only the
organization's own rules that apply to every session do. Organization-wide rules are not part of a
library.

### Templates

A template is a board a new native project can start from. The app lists the templates in the
**Board** dropdown of New project, in this order, followed by its own Default board (today's columns,
with no workflows), which is not a template here. Creating a project from a template makes its
columns once and sets each column's workflow; nothing links the project to the template afterwards,
so changing a template here changes only the projects created after.

| Field | Required | Meaning |
| --- | --- | --- |
| `key` | yes | Folder-style key: lowercase letters, digits and dashes. Unique among the templates. |
| `name` | yes | Shown in the app's Board dropdown and as the "External: &lt;Template&gt;" group in a column's Workflow menu. |
| `description` | no | One sentence. |
| `columns` | yes | The columns, in order. Each has a `name`, a `type` (`backlog`, `unstarted`, `started`, `completed` or `canceled`) and an optional `workflow`, a key from `workflows`, that the column runs. |

A template needs at least one `backlog` or `unstarted` column and one `completed` column, and every
`workflow` must be listed in `workflows`.

## Adding a workflow by hand

1. Make `default/workflows/<key>/workflow.json` and its stage files.
2. Name an existing agent key, or add `default/agents/<key>/agent.json` with its instructions.
3. Add the key to `workflows` in `default/namespace.json`, where it should appear in the list.
4. To offer it in a board template, name its key as a column's `workflow` in `templates`. A
   workflow that no template names is still read, but the app does not list it.
5. Merge to `main`. Within a few minutes the workflow shows on the app's Workflows page and in every
   column's Workflow menu.

If any file is missing or does not follow this layout, the app says external workflows can't be
reached, so check the JSON and every `file` path before merging.
