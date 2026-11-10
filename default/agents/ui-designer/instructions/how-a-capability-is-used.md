A product design starts from use, not from screens. Before any surface, settle who reaches for the capability, in which situation, and what using it looks like. A capability is anything the product lets someone do: a feature on a page, a command, an endpoint and its client, a setting, a file someone can open.

**Start from a named use**

- Every capability names who uses it and for what, the way a change names its consumer: to support the orders page in the store, for backfill jobs. A capability with nobody named is a question for the person, not something to design.
- Say what it promises in the user's words, with no mechanism in the sentence: build your application locally, ask for it to be deployed to the cloud, and the platform takes it from there, with no cloud or operations knowledge required.
- State the problem as someone meets it: nobody can tell where a customer's address comes from, or which systems hold it, without reading pipeline code.
- Order the work by what it gives the user: first they can reach the thing themselves without asking anyone, then it is useful to someone specific, then it is general.
- Build for the user in front of you and say that it is narrow. Widen the capability at its second use case, not before: if a second use case appears, the pattern can be extracted then.

**Show the use before the model**

- Examples first: what using it looks like, numbered, before any concept is explained. If writing a schema needs more than the examples in the design document, the design has failed. A user who needs more than the examples needs a different design, not more help text.
- Write the scenarios, not only the usual one: the first time, the failure, nothing there, far too much there. A design says how it behaves in different scenarios.
- A capability with no screen is shown the same way: the command and its output, the call as the caller writes it, the lines added to a configuration file.

**Fewer things to know**

- List every concept the user must hold to use it. Each revision removes one: what can be derived, merged into something they already know, or left for later comes out.
- A fact has one home. The user states a thing once; a second place to define the same property is something a design must never have. What the product can work out it computes and never asks.
- A concept matches a thing in the user's world and carries their word for it.
- Make the wrong use impossible instead of explaining it: a choice from the closed set where the set is closed, a required value with no silent default where a wrong one would point at the wrong place, a refusal at the first moment with a message that names the thing. Typos must surface rather than being silently ignored.

**The smallest capability**

- Before adding a feature, check whether an existing one, used differently, already does it: often the fix needs no new machinery, and the same worker on a different queue already solves the problem.
- Before building a mechanism, check whether the platform or the technology already has it. A one-off need goes inside something already deployed.
- No option, mode or setting until it is known to be needed.
- Give the size it serves a number and design for that: for example, a hard limit of 1,000 items per project as a safe practical ceiling. Say what the user sees past it.
- What is left out is a seam, named so nobody builds around it, with what the user meets in its place.

**When there are two ways to use it**

- Lead with the one you recommend, show two or three real alternatives with what each costs the user, and state the strongest objection to your own recommendation before you commit.
- When only trying can settle it, build both small, try them, and keep the simpler one.
- When the ways differ in what a user can do, the choice is the person's. Ask, in the workflow's one batch of questions.
