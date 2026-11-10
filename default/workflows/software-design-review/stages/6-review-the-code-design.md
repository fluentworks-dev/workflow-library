Judge the concepts and the interfaces before the file map and the tasks.

**Concepts.**

- Each concept is named in the words people already use for the thing, has one responsibility and one name across every document.
- No fact has two homes. No invalid state can be written.
- Two things that mean different things do not share a type because their fields match today. Two things that mean the same thing do not have two names.
- A concept whose meaning changes says so.
- A name that resisted its author (`data2`, a name with a number, a name ending in "helper") marks a concept that is wrong or split.

**Interfaces.**

- Usage is shown first: two or three real call sites.
- The shape hides more behind a smaller interface. One piece of state has one writer; one task has one way to be done.
- One response type per method, and a method reports only what it knows: a method that publishes an update cannot say whether the whole job succeeded. A method returns what its caller needs, not a boolean.
- The error behavior of each interface is written. A helper asks for the least it needs.

**Placement.**

- Each piece is in the layer and module that own its concern. A parent does not import its children; a type used by more than one sibling moves up.
- No vendor type crosses the boundary it sits behind. Pure logic is separate from adapters.
- The change matches the neighbours. A new pattern, abstraction or naming convention has a written reason, or it is a finding: do not introduce patterns, abstractions or naming conventions that are not already present in the codebase.
- The next change of the same kind has an obvious home. Nothing is fixed that should be left to the builder.

**Size.**

- A helper of a few lines with one caller is inlined; a block that is reread is extracted and named.
- A file with one constant used once does not exist. A ladder that maps keys to values is a table. Repeated strings are an enumeration.
- An existing primitive is reused before a new model is added.
- Duplication between two different concepts stays, with a comment on both copies, until a third consumer appears.

**Names.** Full words, the unit in the name, a verb for a function (functions perform actions), the origin in the name when it matters.

**Global constraints.** The plan opens with the branch, the repository's commit and pull request conventions, where tests go and how they are written, the checks that gate the pull request and who runs them (CI on every push, Software Validation once with counts; a task runs only its own test), and the style rules. Check each against the repository as it is, not as a document says it is.

**Tasks.**

- Grouped by logic and dependency. Each leaves its own test passing and is one commit, with its subject written now.
- Each is a loop: write the test, see it fail for the right reason, write the code, see it pass, commit. The core of each is real code, with no placeholder.
- What one task produces is what the next consumes, by exact name and type.
- Every changed line traces to the task: no adjacent cleanup, no rename of untouched lines, and a rename that is needed is its own commit.
- The closing tasks exist: the swap of any development pin, and the reread of every comment and description against the final diff.

**Tests.**

- The edge cases most likely to hurt are each pinned to a named test.
- Only the outermost seam is faked, and assertions name the exact arguments and the exact body class.
- A rule that must hold for every route or every adapter has a test that walks the table, or a parity suite. A port is proven by parity with the old code, which is the specification.
- No test exercises a library instead of the code. The tests run in CI.

**Coverage and efficiency.** Every outcome in scope.md maps to tasks, out of scope is listed, and the verification commands are exact. Where data can grow or a path is hot: no repeated reads, no call per item in a loop, no unbounded list. Nothing else is optimized.

**Questions to ask here**

- What is the point of this rename?
- What do these abbreviations mean? Spell the names out.
- Can these helpers of a few lines, each used once, be inlined into the code?
- Can this if-else ladder be a constant lookup table?
- Is there an existing model, such as a `Point3` type, to reuse instead of a new one?
- Does this test exercise the library itself, the way a test of gzip would, rather than how we use it?
- Could a newcomer guess where this lives?
