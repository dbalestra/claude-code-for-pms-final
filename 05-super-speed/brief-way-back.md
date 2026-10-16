# Page 1 — Brief: a way back for responders who've stopped getting callout offers

For Helen Achebe · from the Dispatch PM · draft, 16 Oct 2026 · nothing built or decided yet

## The problem
When an incident comes in, Dispatch offers the callout to responders one at a time, best-ranked first. Each offer is a **ping** on the responder's phone. Since release 4.2 (12 Aug), some responders have stopped receiving pings at all. Four of 16 (Vesper, Meteor Mite, The Undertow, Farlight) went from about 12 pings a week to 1 or fewer. Callouts that nobody took doubled, from 5.5% to 11.1%.

The cause, as best I can tell: an expired ping (nobody answered in time) lowers a responder's ranking as much as a turn-down does, and the ranking never recovers on its own. A responder who ranks low gets fewer pings, so they get no chance to earn the ranking back. Release 4.2 cut the wait from 90 to 60 seconds, so more pings expired. It also made distance count for more in the ranking, which pushed these responders further down.

## Who it's for
- **Handlers of quiet responders** (e.g. Kip with Meteor Mite, Aunt Dot with Vesper). Today they can't see that their responder has stopped getting pings, or why.
- **The responder** whose phone has gone silent.

## What changes for them
1. **The handler is told.** The console shows: "Meteor Mite has gone quiet. 1 ping this week, usually 12. The last 5 expired unanswered; none were turned down." One action: *Restore their standing*.
2. **An expired ping counts for less than a turn-down.** A turn-down is a decision. An expiry may just mean the phone was in a pocket.
3. **Rankings recover with time.** A low acceptance record eases back toward neutral over days. Today it takes about 12 taken pings to climb back.
4. **The responder's phone goes off again** for callouts where they're the right person.

## What it deliberately doesn't do
- **Doesn't send callouts to a farther or weaker-fit responder to give someone a turn.** The best-placed responder is still asked first.
- **Doesn't revert 4.2's distance weighting.** Wide-area responders asked for it.
- **Doesn't change the 60-second wait yet.** That waits for answer-time data.
- **Doesn't excuse turning jobs down.** A turn-down still lowers ranking.
- **Doesn't touch the Responder Availability Record** (Supply reads it), availability, capability tags, or the 45-minute distance limit.
- **Doesn't identify anyone.** Cover names only (Security Policy 4.1).

## How we'd know it worked
No available responder under about 3 pings a week for more than 2 weeks; the four's expired share back near everyone else's; untaken callouts back toward 5.5%.

## What I need from you
Is this the right replacement for a quick number change? And does Availability Confidence still stand as a Q3 commitment?

<div style="page-break-after: always"></div>

---

# Page 2 — Record: evidence, reasoning and open questions

*For anyone reading this months from now. No written description of how Dispatch ranks responders existed before this, and that gap is part of why this problem went unnoticed. This page records what we knew, what we guessed, and why we chose what we chose.*

## How ranking works today (from the routing code, `00-rook/code/dispatch-routing/`)
- **Score** = proximity + recent acceptance + capability match. Before 4.2: 0.45 / 0.40 / 0.15. After: 0.60 / 0.25 / 0.15. Proximity is 1 minus travel minutes over 45, so zero beyond 45 minutes.
- Everyone available stays on the list; only the order changes. Offers go down the list one at a time.
- **Acceptance score** starts at 0.5. +0.08 per taken ping, -0.12 per turn-down **or expiry** (`offer.py:30` calls `record_declined` for both). No decay, no reset (a 2019 code comment flags this; still open).
- The weights are global, so 4.2 applied equally to responders who turn jobs down. The glossary says expiries are "recorded separately" from turn-downs; the score doesn't.
- Scores sit in an in-memory dictionary, so a restart could wipe them. I can't tell whether the 12 Aug deploy did.

## What we know vs. what we infer
**Observed (my queries of the pings table; Ravi Menon, data analyst, to confirm):**
- Before 12 Aug nobody got fewer than 6 pings a week. After, four got 1 or fewer.
- Their expiry rate went from about 3% to 59%, against 14% for the other twelve.
- Expired-last-ping callouts rose from 3 to 23. Turn-down endings stayed flat at about 1 a day.
- Support tickets: 32 "phone never goes off" tickets since 12 Aug, all open, none before. "Gone before I could answer" tickets: 13.

**Inferred, not proven:**
- The four's scores are near the floor. This is from my replay of the code over the pings, not real scores.
- They are not simply slow responders. Untestable: no table records answer time.
- A "seasonal" explanation doesn't fit. Total ping volume was flat; it moved from four responders to the other twelve.

**Unknown and unrecorded:** who chose the new weights, whether they were meant to cover responders who turn jobs down, why the wait went to 60s, and whether handler overrides covered the lost callouts. The engineering manager asked on 14 Aug; Wen Li (staff engineer, owns the code) to answer.

## Options considered
| Option | Outcome |
| --- | --- |
| Change one config number, ship today | Rejected. Fixes the symptom with no record of why; that's how this arose. |
| Revert 4.2 | Rejected. Undoes what wide-area responders asked for; doesn't add recovery. |
| "Guaranteed look": always offer a quiet responder one callout, even if someone nearer exists | Rejected 16 Oct. Sends callouts to worse-placed responders for fairness; the goal is people reaching incidents fast. Revisit only if recovery proves too slow and untaken callouts stay high. |
| Handler notice + expiry scored apart + score recovery | **Proposed.** |

## Open questions
| Question | Owner |
| --- | --- |
| All 16 current scores, answer times, override coverage, August baseline | Ravi Menon |
| Was an expiry meant to count as a turn-down? Were the new weights meant to cover decliners? | Wen Li, Marcus Oyelaran (engineering manager) |
| Why 90s to 60s? | Priya Raghunathan (left 21 Aug), Wen Li |
| Console notice and phone view design | Sofia Marino (product designer) |
| Q3 commitments, including Availability Confidence | Helen Achebe |

## Decision log
| Date | Decision | By |
| --- | --- | --- |
| 16 Oct 2026 | Pursue handler notice, separate scoring for expiries, and score recovery. Rejected guaranteed-look. | Dispatch PM, pending Helen's review |
