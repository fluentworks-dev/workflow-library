**First, scope.md's "Design workflows" line.** It is a decision. When it says Product Design has nothing to do, read the ticket, its comments and the earlier documents, then save the three-line `product-design.md` below and finish, without running the product or reading the design system, unless those documents name a surface that changes, by the definition under "Do"; then design that surface only, and say in the document which surface scope.md missed.

**Read, in this order**

1. The ticket, its comments, `scope.md` and the answers to questions earlier sessions asked. What they settle is settled. When there is no `scope.md`, there is no "Design workflows" line to follow: work from the ticket and its comments, and say so in the status line of `product-design.md`.
2. The repository's guidance file and its design documentation: where tokens, components, icons and other graphics live, how they are generated, which terms the product uses.
3. The product as it is today, wherever this ticket will change it. Run it or open its preview and go through the current path yourself, including one failure. For output without a screen, run the command or trigger the message and copy the exact text. For a capability a developer or an operator uses, write the call or edit the setting yourself.
4. Two or three existing surfaces of the same kind as the one you will change.

**Do**

- List every surface a user will meet differently because of this ticket. A surface is what a user of the product meets: a screen, a page, a dialog, a message or an error, an empty state, an email, a notification, a command's output, a setting an operator edits, a policy a visitor reads, a tool's description or answer an agent reads, release notes. Source comments, README and contributor documents, CI output and workflow files are the repository's documents, not surfaces: Code Design and the build own them, and they never bring a ticket into this workflow.
- For each, record who the user is, what it shows today with the exact current wording, and which pieces of the design system it uses, graphics included.
- Name the kind of user each surface serves: an operator, a user of a web product, a non-technical user of a platform, an engineer or a system downstream, a future maintainer. When a surface serves two, design for the one with the least context.
- Note what you find on the way that the ticket did not ask for: a misaligned control, two styles for the same error, a page that does not fit a phone. Under about two hours of building, it goes into this design, without asking. Larger, it is one line for a new ticket, unless it belongs to what this ticket delivers; then it goes in the batch with what it costs, doing it now first.
- Decide how much there is to design. A change that follows an existing pattern gets one version and no alternatives. A change that is wording only (a sentence, a message, a tool's description, a paragraph) is designed as exact text in `product-design.md` and gets no design file.
- If no surface changes, save the three-line `product-design.md` below and finish. Build no file.

**Template: surface inventory**

| Surface | Kind | User | Today | Changes |
|---|---|---|---|---|
| Upload files | Screen | Project member | One file at a time. On failure: "Upload failed" | Several files; a state per file; failures listed with reasons |
| File too large | Error | Project member | Fails after the upload with "Internal server error" | Refused when chosen, with the limit and what to do |
| `upload` command | Command-line output | Engineer with a script | Prints nothing; exit code 0 even when a file failed | One line per file; exit code 1 when any failed |

**Template: nothing to design**

```
# Product design: PRJ-52 Move run records to the new store
Status: no surface changes. Design file: none.
Reason: the change replaces storage behind the same pages, sentences and commands; every one keeps its output.
```

**Questions to ask here**

- Who is this for? What can they not do today?
- Did you run it, or are you reading the code?
- What does it say right now, exactly?
- Is there already a screen that does this? Can we reuse it?
- Where is the design system? What does the page next to it use?
- Is the command-line output part of this? The email?
- Does anything actually change for the user? If not, say so and finish.
