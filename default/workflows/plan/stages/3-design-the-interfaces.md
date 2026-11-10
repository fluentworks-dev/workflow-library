Work in this order.

1. **Usage first.** Write two or three real call sites, or the steps of the person's path from product-design.md, as they should read when the change is done. They open code-design.md under "What it looks like", before any abstraction is explained. If writing a schema needs more than the examples in this document, the design has failed.
2. **Look for the form that adds nothing.** Can an existing mechanism do it with different configuration, such as the same worker on a different queue instead of separate worker modes? Can it be done by deleting code, for example removing `version_history.py`, `_find_open_drafts`, the `except IntegrityError` branch and the re-read after create? Does the framework, the standard library or a sibling repository already have the primitive? Write down what you found.
   - Build no abstraction for one use, and no option "just in case". When porting or building a library, build only what that pipeline needs.
   - When you decline to build for scale, write the number and the trigger: "a hard limit of 1,000 is a safe practical ceiling for line items per order in this system".
   - A design improves by losing concepts. Each revision may remove one (a layer, an overlay, a step, a library), while the surface that is specified but deferred grows.
   - The one exception is configuration that the operator of physical equipment reaches for: there, more options give more ways to recover when things go wrong.
3. **Sketch at least two structurally different shapes** that serve the usage, one of which adds the least: no new file, no new type, no new setting. Compare them:

| | Shape A | Shape B |
|---|---|---|
| What it adds | | |
| What it removes | | |
| What a caller sees | | |
| Where the rule is enforced | | |
| Where the next change of this kind goes | | |
| Effect on existing consumers | | |

   Choose the shape that hides more behind a smaller interface (the deeper module) and that you would have built had the requirement existed from the start. Record the shape that lost and why. Reject a shape where one piece of state has two writers, one task has two ways to be done, one list lives in two places, or a new flag must be kept in step with an old one.
4. **Specify each interface as real code**: the signature, the types, what it returns (what the caller needs, not a boolean), and what it raises for each case. Arguments are keyword-only where a call would otherwise need its signature to be read. For each new setting give the name with its unit, whether it is required, and its default with the reason. An option nothing needs yet is left out; an escape hatch that must exist defaults to empty so existing behavior does not change.
   - Explicit beats implicit, even at the cost of repetition. The keyword form is for the reader: it does not require remembering signatures, and it lets a signature grow without reordering anyone's call.
   - A unit of configuration writes out every input it takes: nothing arrives by invisible inheritance, so reading one unit file tells the whole story, and a new unit that forgets an input fails loudly on `terraform validate` rather than silently mistagging. The trade is accepted: literals give up some protection against drift for one visible mechanism.
   - Every declared variable is described and validated, and the validation message says why the constraint exists.
5. **Place each piece.**
   - A rule is enforced where every caller passes: enforcing it in the manager makes the invariant hold for every caller, and the router just forwards.
   - A layer does not know its children's concerns: a manager of shared definitions does not need to know anything about images when it only cares about files. If such a check is necessary, expose it from the child typed model layer instead.
   - A shared type moves up: a type used by more than one module moves to a parent folder.
   - Input is validated where it enters, and nothing repeats a rule an upstream service owns.
   - Pure logic sits in modules with no framework imports, with thin adapters around them. A module shared across deployables stays dependency-free and says so: "Kept dependency-free so the proxy (middleware bundle) and the standalone presence server can share it".
6. **Design for the next contributor**, who opens one file, copies the nearest example and takes the shortest path that compiles. Say what is left to the builder ("`argparse` is enough").
7. **Check the contracts.** For anything a consumer relies on (an endpoint, a stored shape, an event, a file format, a configuration name), the change is additive, or system-design.md already sequenced the break. A break that system-design.md missed goes under "Open questions for the Software Design Review".
8. **Check state that survives a restart.** For each operation that changes state: what happens when it runs twice, and when the last run stopped at any point. Derive each step from the state that exists, not from what the last run meant to do. Make what a run leaves behind (a lock, a temporary file, a claimed job) recognizable as stale. Give two writers their own file, key or record before reaching for a lock. Order two writes so a failure between them repairs itself on the next run, and write the order down: "Write the line items before the order summary so a failure between the two writes leaves the summary without `is_complete=True`, making the next run self-healing".
9. **Check efficiency** only where the path is hot or the data can grow: no repeated reads, no call per item in a loop, no unbounded list. When you decline to build for scale, write the number and the trigger for revisiting.

Decide technical choices yourself and record the notable ones in the Decisions table as decided by you. Ask the person only when a shape changes what a person can do or see beyond what product-design.md settled.

### Questions to ask here

- Can it be fixed without new machinery? What does the simpler shape remove? List it.
- Is this required, or optional? Can it be simplified for now?
- Can a new option, such as a worker count, wait until we know we need it?
- Does a reader need the signature to understand this call?
- Why does this return a boolean? What does the client actually need back?
- Same response type for two methods? One of them does not know whether the job succeeded.
- Where is this enforced? In the router? Move it to the manager so it holds for every caller.
- Does the upstream service enforce this already? Then this constraint can go.
- What happens when this runs twice?
- Does this work in the other region or cloud partition? The other environment, the other backend.
