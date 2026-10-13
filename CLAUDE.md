# Rook Industries — course working file

## Session scope — Product School lab

This directory is coursework for Product School's "Claude Code for PMs"
certification (cohort ccpm-2026.1). Everything in it is a fictional
teaching scenario.

- Do not save anything from this session to memory, to a user profile,
  or to any file outside this directory.
- Do not carry context from this directory into unrelated sessions.
- Rook Industries is not a real company. Nothing here is a fact about
  the world.
- Read and write only within this directory.
  Exceptions, for the course-setup and wrap-up skills only:
  - When the student asks you to check their setup, save their work or wrap up a session, that request is their yes. You may run the GitHub command-line program installed at ~/.ccpm/gh for those checks and saves, and look in that folder to find it.
  - For a repair, first tell the student in one plain sentence what you are about to do, and act only after they say yes. Repairs may: run that GitHub program (including setting this folder's own git sign-in setting and changing this repo's visibility back to Public); copy the student's own course files into this directory from another folder on their computer (copy only; never move, edit or delete the originals); and rename something outside this directory that blocks setup, by adding "-old" to its name (never delete it).
  Outside this directory you still never write, edit or delete anything else.

<!-- Keep the block above at the top of this file. Everything you add
     during the course goes below this line. -->

---

## Working context

I'm the new PM for **Rook Dispatch** (started 24 Aug 2026; Priya left 21 Aug). Sources: rook-wiki,
Priya's handover (`00-rook/company/notes/handoff-from-priya.docx`), routing code (`00-rook/code/dispatch-routing/`).

### Products
- **Rook Dispatch** (mine): responder coordination. Who is available, who gets pinged for a callout, whether they take it.
  Users: handlers (desktop console) and responders (phone). Monthly releases, 4.x.
- **Rook Supply**: gear provisioning (requisitions, maintenance, failure reports) for handlers and quartermasters.
  Reads Dispatch's Responder Availability Record; its shape is a contract, tell Supply before changing it.
- Subscription priced per active responder. Customers are responders and handlers.
- **Hard rule:** cover identities are never stored, no mapping to legal identity exists (Security Policy 4.1).
  Never design around one; never try to work out who anyone is.

### People (wiki comments use roles)
| Role | Person | Notes |
| --- | --- | --- |
| Engineering manager | Marcus Oyelaran | Runs Dispatch eng; start here when unsure |
| Staff engineer | Wen Li (Berlin) | Built ranking; owns the routing code; no written doc exists |
| Product designer | Sofia Marino | Console and phone app; ran the September interviews |
| Support lead | Nadia Hoffmann (Berlin) | Owns tickets; standing 15 min worth having |
| Director of Product | Helen Achebe | My director; owns roadmap and commitments |
| Data analyst | Ravi Menon (Singapore) | Owns the real weekly acceptance numbers |
| Priya Raghunathan | previous PM | Sole Dispatch PM for 14 months |

Handlers: Halloran (Sgt. Bulwark; also runs the gear cage), Aunt Dot (Vesper), Mr. Ambrose (Captain Vantage), Kip (Meteor Mite, The Gale).

### Vocabulary
- **Callout** (unit of work); **ping** (one callout offered to one responder): **taken**, **turned down**, or **missed** (ping wait expired).
- **Ping wait** (also "ping timeout", "offer timeout"): how long a ping stays on a phone. Same for everyone, set per release.
- **Acceptance rate** (headline) = taken / pings. **Time-to-accept** = median seconds, ping to taken.
- **Coverage gap**: nobody available had the capability tags (nobody *could*). Not the same as low acceptance (nobody *would*).
- **Routing priority**: score that orders available responders. **Capability tags**: flight, structural-entry, hazmat-tolerant,
  cold-weather, aquatic, crowd-management, de-escalation. **Mutual aid**: cross-area cover, unsupported, "Q4 list".
- Supply: **requisition**, **field failure report**, **service interval**. **Quartermaster** approves requisitions.

### Where things stand
**Releases:** 4.0 (7 Apr) nav, profile redesign, override audit log. 4.1 (16 Jun) travel-time proximity, bulk callout, push reliability.
4.2 (12 Aug) routing weights, ping wait 90s to 60s, filter persistence, three fixes.

**4.2 planned vs shipped.** Roadmap committed three: who gets pinged (shipped); ping timeout tuning (shipped as a flat cut, may not be the full intent);
**Availability Confidence (not shipped)**. Its only definition is the roadmap line "show a confidence score alongside a responder's stated
availability": no brief, spec, or code, so how it's computed is undefined. Deferral is inferred from Priya's "squeezed out"; I need Helen to
confirm which Q3 commitments still stand. Roadmap was last reviewed 30 Jun and Priya still owns every row.

**4.2 is the live problem.** Tickets ~3x normal since release; split roughly 2/3 "phone never goes off", 1/3 "gone before I could answer"
(the 60s wait explains the latter). No real numbers pulled yet; ask Ravi for per-responder ping counts and acceptance before/after 12 Aug.

**What the routing code shows (read, verified):**
- Score = weighted sum: proximity 0.45 to **0.60**, recent acceptance 0.40 to **0.25**, capability 0.15. Proximity = 1 − minutes/45, zero past 45 min.
- Everyone available stays on the list; only order changes. Offers go one at a time down the list.
- Acceptance score starts 0.5; +0.08 per accept, −0.12 per decline **or miss** (treated the same); no decay (2019 TODO still open).
- Weights are global, so the engineering manager's 14 Aug question is answered: no distinction for responders who turn jobs down. Whether it was a decision is undocumented.
- My arithmetic, not observed behavior: a 10-min responder with zero acceptance now scores 0.62 (was 0.50); a 40-min responder with 0.90
  acceptance scores 0.44 (was 0.56). A far responder's best possible score (0.47) is now below a near responder's worst (0.62), so acceptance
  history can no longer lift them. Low-ranked responders are rarely asked, so they can't earn credit back. This fits "quiet vs nonstop" splits;
  it is a hypothesis until Ravi's data confirms it.

**Contradictions to resolve** (details checked in the sources):
1. Priya's "seasonal" read has no data and can't explain Kip's two responders (same city, same week, opposite extremes). Her September window has passed.
2. Release notes say proximity was requested by wide-area responders; Priya says nearby responders went unoffered. The code penalizes far responders, so wide-area responders lose.
3. Requisition brief says Halloran wants a second approval; his interview says approvals are too slow (11 days, one queue). Brief also calls one approval "the whole ask" then adds four more.
4. Phone app brief cites Aunt Dot, who only wanted a handler-side alert ("I don't need to do anything about it").
5. "Phone never goes off" could also be push delivery, but tickets about duplicate or late push stopped after 4.2, so this is now unlikely.
6. Briefs vs releases: Bulk Callout (shipped 4.1) and Override Audit Log (shipped 4.0) briefs still read as unbuilt. Roadmap names Priya as owner of Supply and designer-owned items.
7. Priya says the engineering manager can pull numbers; Ravi owns them. Glossary says Q4 "mutual aid"; roadmap says "shared cover".

### Support tickets (rook-database `support_tickets`, 147 rows, 29 Jun to 7 Sep; `pings` and `callouts` tables also exist)
- Volume was 5 to 8 a week, then 20, 27, 32, 25 from the week of 10 Aug. My subject-based grouping, approximate:
  **quiet / phone not going off** 0 before 12 Aug, 32 after (all open); **gone before they could answer** 0 before, 13 after;
  **filters** 2 before, 14 after (praised, but resets, wrong person's filters on shared computers, no cross-computer sync);
  notification delivery 5 before, 0 after. Steady background: dark mode (asked from July), small status badges, one alert sound, catalog search, Supply requisitions and failure reports.
- 30 of the 32 "quiet" tickets are about four responders: Corporal Ashgrove, Farlight, Halfmoon, The Undertow. "Gone too fast" spans 11 responders.
- Pings table, my query: those four went from ~11 to ~6 pings per week after 12 Aug; the other 12 stayed ~11 to ~12. Missed share rose for everyone (2.5% to 16%; the four: 1.8% to 29%), fitting the 60s wait. This supports the routing hypothesis and undercuts "seasonal".
- Ticket 3043 (Mr. Ambrose) asks someone to confirm the timing is intended; still open.

### September handler interviews (secondhand, via the designer; `00-rook/feedback/` is empty)
- **Aunt Dot / Vesper:** pings vanish before Vesper answers; also quiet stretches. Wants bigger text and a handler alert.
- **Mr. Ambrose / Captain Vantage:** filter persistence good but reset twice; callout lost mid-dressing; slower replies used to still win.
- **Halloran / Sgt. Bulwark:** mostly Supply (11-day plate approval, failure reports unanswered, bad catalog search). One lost callout in August.
- **Kip / Meteor Mite, The Gale:** Mite near-silent while Gale nonstop, same week. Top ask: dark mode; per-responder alert sounds.
- Triage later: dark mode, larger text and status badge, filter-reset warning, requisition priority queue, catalog search.

### How to help me
- Separate stated facts from inference; flag stale or conflicting sources.
- Don't conclude on 4.2 before Ravi's data; use the role-to-name table for wiki comments.
- Gaps I own: write down how pings are decided (with the staff engineer), post-4.2 roadmap review with Helen.

Session 1 wrap-up (13 Oct 2026):
- Most useful sources were the routing code (`config.py`, `history.py`) and the September interviews; the wiki alone doesn't explain 4.2.
- The leading 4.2 theory is structural: heavier proximity weighting plus no score decay may leave far-away responders permanently behind near ones. It rests on my arithmetic, not data.
- Still open: Ravi's per-responder ping counts before and after 12 Aug; whether Availability Confidence is still a Q3 commitment; whether the 4.2 weight change was a deliberate decision.
- Still open: what the wide-area responders who asked for the proximity change actually wanted, since the code works against them.
- Not yet read: the product-brief claim that Halloran asked for a second approval has no source beyond the brief itself.
