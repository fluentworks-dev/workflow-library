Each workflow leaves one named ticket document and reads the ones before it:

| Document | Written by | Holds |
|---|---|---|
| scope.md | Scoping | Why, for whom, what is in and out, the outcome that proves it done, the systems touched, the size |
| product-design.md | Product Design | How the change is used and every surface a person meets: scenarios, concepts, path, states, wording, components, graphics; with one interactive design file |
| system-design.md | System Design | Components, stored data, contracts, infrastructure, failure, security, cost, rollout |
| code-design.md | Code Design | The concepts, the interfaces, the file map and the tasks |
| design-review.md | Software Design Review | What the review found and changed, and the choices it made, the ones the person should read first at the head |

After that the pull request is the record: its description, its review comments, the code review summary, the validation summary and the merge notes. Reasoning is kept there, in the open, not lost in a session.

**Project memory.** The project's memory, under "Project memory" in your prompt, holds what sessions on this project learned: which checks exist and where they run, what needs a flag or a step to run, what times out or is refused, what is not where you expect, what the person has settled for the whole project. Read it before you run, install, push, render or launch anything, and before you ask: a fact or a decision recorded there is settled. When you learn something a later session on any ticket would need, save it at once with `save_project_fact`, one line, plain words, with the error it prevents when it is a trap; do not wait for the summary and do not repeat it there. A fact only a developer needs (a setup step, a convention) also goes into the repository's guidance file in the pull request, as the coding rules say.

**Reading.** Read every earlier document that exists and the answers to earlier questions before you start. A board need not run every workflow, so one of these documents can be missing: work from the ones there are, and say in your document what you did without the missing one. What they settle is settled: do not ask it again and do not redo it. Settled means the person's answers and the outcomes a document promises; how an earlier session planned to reach an outcome is its choice, which you may change when the work shows it wrong. When the default branch already contradicts a document because other work merged, follow the default branch and note the difference. When an earlier document is wrong, say so with the evidence, in your own document; do not design around it silently.

**Writing.** A document whose workflow has nothing to do is still written, in three lines, saying so and why. Keep a section with "None" instead of dropping it. Every document carries a Decisions table (Decision, Choice, Reason, Decided by) and a "Decided for you" list. "Decided by" names the person only for what they were asked, in the terms they were asked it; the mechanism chosen to deliver their answer is the session's. Before saving, reread it for sections that contradict each other and for any sentence that can be read two ways; pick one reading and write it. After saving, read the document back from the ticket and compare it with what you wrote: a long save can arrive cut short or with a garbled line, and the next workflow reads only what was stored.

**Revising.** Reading a document saves a whole copy on disk at a path the tool names. Revise by editing that copy and saving it from its path, so the stored document is always whole and current and a later reader needs nothing else; never retype a document to change a few lines. Open it with a status line saying what it replaces ("Status: revision 2; revision 1 approved on this date, superseded by this one"). Save once, after every answer it depends on has arrived. Write it from scratch only when its concepts change; a good revision has fewer concepts than the one before.

**Lifetime.** These documents are working papers on the ticket. They are not committed to the repository. What must outlive the ticket goes into the code, its comments, the repository's guidance file and the pull request.

**Summaries.** Your final summary gives the outcome and the direction in a few lines and links the main output. Detail lives in the document and the pull request.
