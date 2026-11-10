Ask these of every request, in this order. Write down the documented answer, never the question:

1. Who is this for, and what can they not do today?
2. What is the point of this? What would someone notice if it shipped, and how would anyone know?
3. Does something that already exists solve it?
4. What is the smallest thing that gives them that?
5. What must it never do?
6. What does it touch, and who relies on what it touches?
7. How big is it, and is that worth it?

- When a ticket states a fact ("people cannot do X", "it is slow", "nothing uses it", "it is small"), check it before you scope on it: try it, read the logs, search the consumers, open the code. A scope built on an unchecked fact is precise about the wrong thing. One named consumer with a real need outweighs "users want this".
- When a written rule and the practice disagree, follow the practice where the gap is deliberate and the written rule where the gap is a slip.
- When a rule is practiced only on new work, scope it for new work. Never fold a sweep of old code or old documents into a feature ticket: that is a file-wide cleanup for a separate pull request. A sweep is its own ticket, with its own value to justify.

Reach for these named practices where they fit; the direction of the project wins over all of them: jobs to be done, five whys, Chesterton's fence, outcomes over output, a success measure with a guardrail measure, cost of delay, RICE (keep the coarse scale of high, medium, low), pre-mortem, reversible against irreversible decisions, YAGNI, walking skeleton, MoSCoW, INVEST, Given-When-Then, Hyrum's law, expand and contract, golden paths. Two things here go beyond the common templates and stay: anti-goals and named seams.
