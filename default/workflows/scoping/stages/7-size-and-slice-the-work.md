1. **Estimate in two parts**, each a range.
   - Agent time: doing the work across the workflows that follow.
   - Person time: reviewing, answering, and what an agent cannot do (access, approvals, a check on a device or on real equipment, applying infrastructure, a release).
   Name the main source of uncertainty in one line.
2. **Check the unit.** Does it land as one reviewable pull request per repository, in roughly one to two human days end to end, with a goal that running something can prove? Write that proof, in words if the command does not exist yet: "the export of a 200,000-row list finishes, and the file has 200,001 lines".
3. **If it is larger, slice** (INVEST is the test for each slice). Each slice delivers something a person or a consumer can use or check, passes its checks alone, and has its own outcome and proof. Order them: first the loop that lets the work be checked (a way to run the thing, scripts an agent can run, a test harness, lint and type checks, CI on every pull request; on an existing project, only the gaps), then one case end to end (the walking skeleton), then the rest in dependency order. Say which slice this ticket is, and list the others in order, at most three ahead. Create no tickets. A dependency order for one service, for example: the query foundation before the entities, then the definitions, then the composed views, then tests and documentation; a storage seam shipped with no behavior change before the new backends behind it; a definition deployed with (or before) the pipeline change that reads it.
4. **If a trial decides the scope**, make the trial the first slice: what is tried, the criterion that decides, the time limit. For example, several prototypes of a storage format, each on its own branch with its own short design, closed within days in favor of the simplest. The criterion is operational: its files can be recovered with a standard shell tool, and a sample of minutes extrapolates to a requirement of days.
5. **If the work is a platform or a tool for others**, its use for real is a slice of its own: it runs through its own pipeline, with throwaway applications of unlike kinds, before anyone is told it exists. For example, two of them, one with native modules and one static page, pushed through before the announcement; what breaks is the bug list.
6. **Check the proportion** against the value from "Decide what is worth building". When the size is out of proportion, put it in the batch, with the smaller option that captures most of the value first. Do not shrink the scope silently.
7. **List what only a person can do**, as steps, so that the estimate of person time shows its reasons.
8. **Take out what is not work.** Files that should not be committed (run logs, state files, data samples, scratch scripts, notebooks named after a person) are named under Out and are not part of the size. A one-time tool is deleted once it has run. Generated files the repository commits by policy are counted apart.

The two sections this stage produces, filled in:

```
Estimate
- Agent time: 1 to 1.5 days across design, build, review and validation.
- Person time: 1 to 2 hours: one decision (taken), a read of the designs, a review of the pull
  request, one check in staging with a real 200,000-row list.
- Uncertainty: whether finished files fit in the existing storage (Assumption, see Extent).
- Proof: an export of a 200,000-row list finishes; the file has 200,001 lines; the request log
  shows no export cut off at 60 seconds.

Slices
- This ticket: exports of any size finish (O1 to O4). One pull request, under two days; not
  sliced further.
- Later, not ticketed: a Retry control, once the 3 other failures have a named cause.
```

When the work must be sliced, the form is, filled in:

```
Slices
1. (this ticket) One export of one list type finishes at any size. Proof: a 200,000-row
   order list exports to a file of 200,001 lines.
2. The same for the two other list types. Proof: one export of each, line counts match.
3. A person sees their running and finished exports in one place. Proof: start two exports,
   leave the page, return, both are listed with their state.
Later, not ticketed: export formats other than CSV.
```

**Questions to ask here**

- Is this the smallest slice that proves it? In a sandbox, is the smallest of everything enough?
- Can it land as one pull request someone can review in one sitting?
- What does someone run to know it works?
- What can only a person do here? How long will that take them?
- Is the size worth the value? What is the cheaper version?
- What comes first, so that the rest can be checked?
- Is the estimate inflated by files that should not be committed at all?
