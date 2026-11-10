Do this stage only when the ticket asks to patch what people see: hide a message, add a retry button, relabel a state, clamp a number, catch an error in the interface, raise a timeout, add a limit. Otherwise write "No symptom patch: stage skipped" in your notes and go on.

1. Reproduce the symptom, or find it in logs or traces. Record what you ran and what you saw. Read the traces before forming a theory: fixes committed on a theory for an unreliable test get reverted once the traces show that the machine was starved, not the components.
2. Follow it from the surface into the code, the data or the runtime that produces it. Read-only. Stop when you can name the likely cause with file and line.
3. Ask why until the answer is something that can be changed (the five whys). Stop there. Do not design the fix.
4. Find when it started. Read the history of the lines involved; a recent change that introduced it is the cause to name.
5. Size both, each as a rough range: the patch as asked, and the fix of the cause.
6. Write what each leaves behind. The patch: what stays broken underneath, and who still meets it. The fix: what else changes for people.

Then write the three options for the next stage:

| Option | What changes for people | What it leaves | Rough size |
|---|---|---|---|
| Fix the cause | The failure stops | Nothing known | a range |
| Patch as asked | People can get past it | The cause, and who still meets it | a range |
| Both | The failure stops, and the rare remaining case has a way out | Nothing known | a range |

Recommend the fix of the cause unless it is far larger than the value or carries real risk, and say which of the two applies. When the patch is right for now, say plainly that it only stops the bleeding, then name the durable fix and its owner.

If you find no cause beyond the symptom, say so in one line ("Traced to src/routes/exports.py:88; no cause beyond the missing control") and scope the patch.

If the cause sits in a repository nobody named, do not follow it there. Record what you saw, name the owner, and flag it to the person in the next stage.

The notes this stage leaves, filled in:

```
Symptom: "Export failed" on the orders page. Reproduced with a 60,000-row list: fails at 60 s.
Evidence: 41 failed exports in the last 30 days (request log); 38 had more than 50,000 rows.
Cause: the export is produced inside the request, and requests are cut off at 60 seconds
  (src/routes/exports.py:88; the limit is set in deploy/gateway.yaml:14).
Started: with the first lists above 50,000 rows. No change in the code introduced it.
Patch as asked (Retry button): 2 to 3 hours. A list above 50,000 rows fails on every retry.
Fix the cause (exports of any size finish): 1 to 1.5 days. People stop seeing the failure.
Recommendation: fix the cause. The patch offers a retry that cannot succeed in 38 of 41 cases.
```

**Questions to ask here**

- Why does it happen?
- Can we fix it without adding new machinery?
- Does the patch only stop the bleeding? What is the durable fix?
- If they retry, does it work, or fail again the same way?
- Did you read the traces, or is this a guess?
- Is the cause ours, or in someone else's system?
- When did it start? What changed?
