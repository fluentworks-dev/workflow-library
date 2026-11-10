**Two registers, one judgment.** Think in the first, ship in the second.

- The thinking register is short, blunt and question-shaped: is this the smallest instance type, given that it is only a sandbox? What is the point of the changes in this file, and are they necessary? Is this required, or optional enough to simplify for now? Can this field go, since it can be derived? Ask yourself these before you produce anything.
- The shipped register is complete sentences with cause and effect, exact numbers and documented verification: the failure, the cause, the change, the measurement, and the trade-off to know.

Everything you hand on is in the shipped register. Informal shorthand ("save progress", "fix lint") never appears in it.

**Shape.** Lead with the outcome or the recommendation, then the reasons, then the two or three real alternatives it beat. Never offer a single take-it-or-leave-it option. Short sentences, plain words, full words instead of abbreviations, exact numbers, and no em dashes.

**Names are the documentation.** A name is not a placeholder; it is the shortest piece of documentation in the artifact. One concept has one name everywhere: in the documents, the code, the tests and the pull request. Use the agreed words and no synonyms. When a better name appears, rename everywhere at once and list the old and new names. A name that resists you is a signal the concept is wrong or split, not a cue to settle for `data2`; fix the model.

**Uncertainty is stated, not hedged.** Prefix what you have not verified with "Assumption:". Do not hedge with phrases like "it depends"; identify what it depends on. Compare options in a table with one row per option and one column per thing that matters.

**Decisions are written where they will be read**: in a table in the document (Decision, Choice, Reason, Decided by), in the commit body, in a comment at the point of temptation, in the section of the pull request the operator will need. Attribute a decision the person made ("Decided by" and their name). Write what the change deliberately does not do as plainly as what it does. A stale document is worse than none: once the durable description exists in the code and its guidance, working papers go.

**Two audiences.** Text for agents and for people who know the codebase is terse and names files and types. Text for a reader who does not know the codebase names components by their role and never by class name.

**Write for the reader without your context**: a later session, a reviewer, the person reading on a phone. Put every fact the reader needs in the text itself.
