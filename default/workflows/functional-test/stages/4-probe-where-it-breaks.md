The expected path works. Now attack it, starting where a failure costs most.

For each step a person or caller takes, ask the seven questions and run the ones that apply: nothing, many, wrong, broken, again, back, someone else. Then:

- Every limit at, one below and one above.
- Every repeatable operation twice. Every operation with two writes, interrupted between them and then run again; compare with an uninterrupted run.
- Old data against the new code; the previous released consumer against the new provider; the default branch's code against the new stored shape.
- Every failure behavior the designs promise: cause the failure, then check what is kept, what the person is told and what recovers.
- Every refusal: the status, the sentence, and that nothing was written. The test that pins one is named for it: `refuses a value too large to be read back rather than truncating it`.
- Two callers at once on anything that reads and then writes.
- The logs and the output of all of the above, searched for the secret, the token and whole records. The rule for logs: "Tag values must never be logged; keys are fine"; "stop logging the entire raw row ... risks leaking secrets/PII".
- Another person's permission level on every new read and write.

For two writes that can fail between them, find the sentence that states their order and test it. It looks like this: "Keeping the create-first order. Both orders have a partial-write window, but they fail on different paths". When no sentence states the order, that is a finding. Practice: delivery at least once plus an idempotent consumer is the workable form of exactly-once, so assert that applying twice equals applying once.

Give the exploration a charter and a limit. The charter is one line: what you attack, with what, looking for what. Stop an area when three probes in a row find nothing new, and write down what you did not probe and why.

Record each probe in one line: the question, what you did, what should happen, what happened.

```
again | staged the same file twice | one stored object, one attachment | as expected | held
wrong | a name of 81 characters through the generated client | 422 with the server's sentence | 200, name cut to 80 | broke
someone else | attach as an account without access to the project | not found, no hint that the project exists | as expected | held
```

A probe that breaks something this change did not touch is still a finding. Run it in the worktree on the parent commit to learn whether it already existed, and carry it to the next stage either way. Do not probe a library: if the only thing a probe could break is someone else's package, drop it.

### Questions to ask here

- What does it do the second time?
- What if it dies between the two writes? Does the next run heal it, or lose rows?
- What does an old client see?
- Empty? Ten thousand? One over the limit?
- What does the person read when it fails? Can they act on it?
- Who else can see this? Should they?
- Is the secret in the log?
- Does this test check gzip itself, or how the code uses gzip?
