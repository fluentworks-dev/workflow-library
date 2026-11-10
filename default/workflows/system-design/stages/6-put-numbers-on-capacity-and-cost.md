**Numbers first.** List every freshness, limit, size, retention, timeout and window in the design, each with a number, a unit and a reason; mark one chosen by feel as chosen by feel. A cap states the ceiling it is safe under: the count is well below 999, results are cached for 10 minutes, and the cap is revisited if the count grows past that threshold. A derived number shows its derivation: 9 = 2 x vCPU + 1 for a 4-vCPU plan. For each limit that refuses, write what the user is told to do next. Numbers of the kind a design carries: query results cached 90 seconds and secrets 8 hours; files rotated at 7000 MB; one upload refused above 5 GiB with a message telling the user to split the file; future-dated records ignored as a guard against clock skew; a 72-hour run requirement checked by extrapolation from a 21-minute sample.

**Capacity.** For each new component or store, one line each, with the arithmetic: the load at the typical and the peak hour and what drives it; bytes per record, records per day, retention and the stored size after a year; calls to each dependency per request against its limit; the usage at which the first limit is reached, and what happens there. Round hard: the goal is to find the limit that is one order of magnitude away.

**Cost.** Run this method on anything that adds or changes a cost path.

1. **Cost paths.** List what costs money each time it runs and what decides how often it runs: database queries and the loops, timers and polls that issue them; endpoints and functions; caches; background jobs; file storage and transfer; emails and other paid third-party calls; builds and CI jobs. When there are none, write "Cost: no cost path added or changed" and skip the rest of this stage.
2. **Scalability.** For each path, say how its work grows: with each user, organization, open client and stored row, and with time. Flag work that grows with history and not with what is live: a read of every row ever written, full rows where a few columns would do, a read with no limit, one query per item in a loop, a lookup with no index. Flag anything that keeps a database from going idle: a poll that never goes quiet, a quiet check that one stuck row can defeat, a loop that counts no-op actions as work, a client poll that reaches a shared database every few seconds. The shape to remember: a read of every finished row's full content on each tick keeps a serverless database awake around the clock, and grows with every row ever written.
3. **Theoretical minimum.** Before estimating, write the least the feature needs, so the estimate cannot shape it: the rows and columns a user sees or a decision uses, how often they can change, and from that the fewest queries, bytes, calls and wake-ups per hour, with nothing happening while nothing changes. Every gap above the minimum is waste to remove or a cost with its reason written.
4. **Baseline.** Where an existing path changes, measure today's numbers on the default branch against test data, never production data: queries per tick or request, rows and bytes returned, calls per hour with the client open and idle.
5. **Stored data and paging.** Look for a value computed on every read that could be written once, a join a denormalized column would remove, a missing index, a "changed since" mark that lets a poll skip unchanged rows. Name what each saves and what it costs. A stored derived value is a second home for a fact, so compute it until a measurement says the read cannot afford that. A list that grows with history and is only partly shown is a first page with a way to ask for more.
6. **Estimate.** A month at one user with the product open all day, a team of 3, and 100 organizations; fixed cost split from cost per user; each price with its source and date; the usage at which a plan allowance is exceeded.
7. **Change the design** so the efficient shape is the one specified, and name the test that counts queries, rows or calls.

Template for the section, with a filled example:

```
### Numbers
| What | Number | Reason |
| Export expiry | 7 days | Survives a weekend; chosen by feel |
| Stale `running` row | 15 minutes | 3 times the slowest export measured (4 min 50 s for a 5 GB project) |

### Cost paths
| Path | Paid per | Runs when | Grows with |
| Export worker run | Compute second | A user requests an export | Project size |
| Stored export file | GB a month | Until the 7-day expiry | Exports of the last 7 days, not history |
| Status poll | Database query | Every 5 seconds while the dialog is open and the export is running | Open dialogs; stops when the status is final |

### Minimum
One query per poll returning one row and two columns (status, expires_at); no query when no dialog is open; one file per export.

### Estimate (prices: the provider's storage pricing page, read 2026-11-04)
| Size | Fixed a month | Per user a month | Total a month |
| 1 user | 0 | under 0.01 USD | under 0.01 USD |
| Team of 3 | 0 | under 0.01 USD | 0.02 USD |
| 100 organizations | 0 | 0.03 USD | about 9 USD |
Allowance exceeded at: about 2,000 exports of 250 MB held at once.

### Decided and asked
Decided: polling stops when the status is final; the bucket, about 9 USD a month at 100 organizations in the existing account, under the money thresholds. Asked: nothing.
```

**Ask the person**, in the session's batch, only when the money line of the rule on when to ask makes the cost theirs (a new vendor, service or plan; or, inside an existing account, more than about 5 US dollars a month per user at 100 organizations or about 50 US dollars a month of fixed cost), or a migration drops or rewrites data. The question says in plain words what the thing is and what it costs at one user and at 100 organizations; the options are: keep it, the cheaper alternative and what it gives up, or drop it. Work that keeps a database awake or grows with stored history is not asked: change the design to the efficient shape (step 7). An additive nullable column or table, a first page with a control to show more, exact values computed on the server, and a cost under the thresholds are decided and recorded with their number; name the cost in your summary.

**Sizes.** A sandbox gets the smallest size and the shortest retention, with the monthly price beside it (for example, about USD 900 per month for the brokers), the words "sandbox data is disposable", and the trade that size makes (for example, a single availability zone means an outage of that zone takes the sandbox down). An environment that keeps data gets hardened inputs. A cost the reviewer should weigh becomes one line for the pull request, such as a reviewer note that the idle cost is about 110 US dollars a month while services are parked.

## Questions to ask here

- is this the smallest instance type? a sandbox should need no more.
- what does this cost a month when nobody is using it?
- does this read every row ever written, or only what is live?
- does anything here keep the database awake?
- where does 15 minutes come from? measured, or feel?
- does this need to run hourly, or is daily enough?
