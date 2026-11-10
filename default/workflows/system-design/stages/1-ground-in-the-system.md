**First, scope.md's "Design workflows" line.** It is a decision. When it says System Design has nothing to do, read the ticket, its comments, scope.md and product-design.md, save system-design.md in the three lines below and finish, without cloning, searching consumers or drawing the system, unless those documents name a component, stored data, a contract, infrastructure, configuration, identity, a cost path or a release order that changes; then design that only, and say in the document what scope.md missed.

Read, in this order: the ticket and its comments, scope.md, product-design.md, and the questions earlier sessions asked with their answers. Read the documents that exist; when one is missing, say in your summary that you designed without it.

Then read each repository that scope.md names:

1. The repository's guidance file and its README: commands, architecture, conventions, terminology, operational warnings.
2. How it is deployed: workflows, deploy scripts, infrastructure definitions, and how migrations are applied (by the deploy, by a workflow after it, or by hand).
3. Its configuration: the settings object, the templates, which values select an environment.
4. Its stored data: schemas, models, migrations, index definitions.
5. Its contracts: the committed API description, published SDK versions, events, files other systems read, configuration names.
6. One or two existing designs of the same kind. Where code you will change looks deliberate (a guard, a retry, a number), read the commit that added it and record what must be preserved.

Record every fact you will rely on with its file and line and where you verified it (which branch, which installed version). These become the Constraints section. Write a fact you could not verify as "Assumption:" with what it depends on.

**Find the consumers.** For each contract the change might touch, search the sibling repositories for callers and list them. Do this now, before any design. It decides what "additive" has to mean.

| Contract | Consumer | Where | Pins |
|---|---|---|---|
| `GET /v2/projects/{project_id}/files` | The backend for the web application | `src/services/files.py:88` | SDK 0.5.3 |
| Table `files`, column `association_name` | The nightly ingestion job | `pipeline/load_files.py:41` | Reads the column by name |

**Draw today's system** as it concerns this change, as a text diagram at two levels of the C4 model: the people and outside systems (context), then the deployable pieces and stores with the calls between them (containers). Go deeper only inside the one container the change reshapes.

```
project lead -> web application -> backend for the frontend -> asset service -> document store
                                                                            \-> object store
nightly ingestion job -> document store (reads)
```

**Decide whether there is system design to do.** There is when the change adds or alters any of these: a component, a process or a scheduled job; stored data or its schema; a contract another system or repository relies on; infrastructure, configuration or secrets; identity or permissions; a cost path; the order in which things must be released.

If none of these change, save system-design.md in three lines and finish:

```
Status: final, revision 1
No system design is needed: <what the change is> touches no component, stored data, contract, infrastructure, configuration, identity, cost path or release order.
Checked: <the files and contracts you read that prove it>.
```

Filled example:

```
Status: final, revision 1
No system design is needed: the ticket rewords three validation messages in the project form and touches no component, stored data, contract, infrastructure, configuration, identity, cost path or release order.
Checked: src/routers/v2/projects.py and openapi.json (the messages are in no response schema); the two consumer repositories, searched for each old sentence, match on none of them.
```

## Questions to ask here

- what is the point of the changes in this file? are they necessary?
- who calls this today? searched the consumer repositories, or guessing?
- is that verified on the default branch, or remembered?
- where is the configuration for this environment set?
- how do migrations get applied here: the deploy, a workflow, or a person?
- does this ticket need any system design at all?
