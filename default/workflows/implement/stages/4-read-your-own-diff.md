Read the whole diff against the default branch once, as a stranger would, before anyone else does. Judge whether each piece is necessary before judging whether it is right.

- Does every file, type, function, setting and option need to exist? Could the change be smaller, or be a deletion?
- Is there a line that does not trace to a task: debug output, a scratch file, a tracker log, a state database, a data sample, a generated file the repository does not keep, an unrelated edit, reformatting?
- Does each new name use full words, carry its unit, and match the name the designs use?
- Does each number have a name, a unit and a reason?
- Does each comment say why, and is it true of the final code? Delete a comment that justifies a path you abandoned.
- Does each error come from the earliest layer and name the rule and the value? Is there a broad catch away from a survival boundary, or a success flag that can never be false?
- Does each log line carry identifiers only?
- Would each test fail if the behavior broke? Does it assert exact arguments? Does it test your code and not the library?
- Do the guidance file, the README and the docstrings match the code?
- Would a person still meet the old behavior anywhere: a parallel surface, a consumer of a changed contract, documentation, a fixture?
- Is a working document committed (a design, a plan, a runbook made for this change)? Remove it in its own commit unless the repository's policy keeps it, and move what must last into the guidance file or a comment: "The spec, implementation plan, and staging runbook were working artifacts for building this change, not repo documentation." Then search the README and the guidance file for its path, so no link to a deleted document dangles.
- Did you edit a design document the repository keeps? Revert that. It is a historical artifact: "`docs/specs/` holds pre-implementation design specs as historical artifacts, not living docs". The guidance file is what stays current.

Fix what you find, each fix as its own commit named for what it fixes.

For the three or four tests that matter most, run a mutation check: break the behavior by hand, watch the test fail, restore the code. Write down which mutation each test catches ("Verified it fails with the offset reset removed from the runs handler and passes with it restored"); it goes in the test plan.

Do not hand your diff to a subagent for review. The Code Review workflow reviews it from a fresh context.

### Questions to ask here

- Is this required, or optional? If optional, simplify it for now.
- Does this script need to be stored and committed now? If not, remove it.
- Is this still used? If not, remove it.
- Is this file too small to be its own file?
- Does this comment describe the code, or the version before the revert?
- Which line here is not about the ticket?
- If I break this on purpose, which test goes red?
- Is this the smallest size that meets the need? A sandbox should take the smallest.
