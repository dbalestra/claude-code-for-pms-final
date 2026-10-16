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

Session 2 wrap-up (13 Oct 2026):
- Pings since 12 Aug: missed share 2.3% to 18.0%, turned-down flat. Vesper, The Undertow, Meteor Mite and Farlight fell from about 12 to 3-5 pings a week and missed 53-64%; the other twelve gained about 26 a week between them, total volume roughly flat, which undercuts "seasonal". Ashgrove and Halfmoon filed many quiet tickets but look like the pack by rate. This replaces the earlier "four" and the far-vs-near theory as the main explanation.
- My replay of `history.py` over the pings puts those four at 0.84-1.00 on 12 Aug and at the floor by 6 Sep, with misses clustered 12-16 Aug. It is a model, not production scores. A miss is scored like a decline although the glossary says they are recorded separately.
- Tickets (12 handlers) and interviews (4 handlers, 3 never filed) barely overlap: tickets size problems, interviews find silent groups. Ambrose's incident is in both (ticket 3043). Recount: quiet is 32 tickets all from four handlers, too-fast is 13 across 9 responders (earlier lines said 30 and 11).
- Working problem statements: (1) the 60s wait expires pings; (2) miss-as-decline with no decay pushes responders out of rotation; amplifier: handlers have no alert or explanation and all 32 quiet tickets are open. Tickets have no reply field and pings have no answer time.
- Still open: Ravi's answer times, score history and last August baseline; Wen Li or Marcus on whether miss-as-decline was intended; capability tags per responder (only Vantage and Ironvale known). Asks to Ravi, Wen Li, Marcus, Helen, Nadia and Sofia are drafted, not sent.

Session 3 wrap-up (14 Oct 2026):
- Headline for leadership: 4 of 16 responders (Vesper, Meteor Mite, The Undertow, Farlight) now get 1 ping or fewer a week; before 4.2 nobody got under 6. Before 12 Aug the four missed ~3% of pings like everyone else; after, 59% against 14% for the other twelve. Misses spiked 12-14 Aug, "too fast" tickets started 12 Aug, "quiet" tickets only 17 Aug.
- Nothing cuts a responder off at score 0 (the code removes nobody; their late pings were all first pings). They lose first place on their own area's callouts instead: first-ping share fell from 75-85% to 13-25%. Back from 0 needs 12 accepts to reach 0.9, and break-even is a 60% accept rate; no decay or reset exists. These are my arithmetic and a replay, not production scores.
- Callouts with no taken ping rose 5.5% to 11.1%; the increase is all callouts whose last ping was missed (3 to 23), turned-down endings stayed about 1 a day. Two-thirds of failures by the home-area responder end with no second ping; every area has one assigned responder except Eastgate (two). Callouts also fell from about 20 to 17 a day. Not known whether handler overrides covered them.
- Vesper, Meteor Mite, The Gale and Sgt. Bulwark have never filed a ticket (ticket `filed_by` names are not the wiki handler names, except Mr. Ambrose). Ticket volume decays faster than missed pings, so it is a poor gauge. My subject search found 22 quiet tickets against the earlier 32.
- No file or wiki page explains why the wait went 90s to 60s. There are no answer times in any table; turned-down pings take 7-40s and misses show 62s to the next ping. The idea that the four are slow answerers (60-90s) is untested. Priya or Wen Li may know the reason.
- Next: Ravi for answer times on taken pings, score history, availability history and override coverage; Marcus and Wen Li on restoring about 90s, scoring a miss apart from a decline, and a score reset; Nadia and the silent handlers (Aunt Dot, Kip, Halloran). Charts are in `ping-charts.png`.

Session 4 wrap-up (14 Oct 2026):
- Routing code (`00-rook/code/dispatch-routing/`): 4.2 changed only `config.py` (wait 90s to 60s; weights proximity 0.45 to 0.60, acceptance 0.40 to 0.25). Weights are global, so it applied to everyone, including responders who turn jobs down. The only score drops are `offer.py:30` calling `history.py` `record_declined` (-0.12, misses included); the only rise is +0.08 per taken ping. No decay, reset or manual fix; scores sit in an in-memory dictionary, so a restart could wipe them and I can't tell if the 12 Aug deploy did.
- Who decided the weights and why is still unanswered. The 4.2 release page says wide-area responders asked; Priya's handover says wide-geography responders unhappy that nearby people went unoffered, "three quarters" of waiting. Neither names who chose the numbers or mentions turned-down or missed pings. Marcus asked on 14 Aug; Wen Li (staff engineer) never answered on the page.
- Pings table against the code backs the "early misses, no recovery" idea: the four (Farlight, The Undertow, Meteor Mite, Vesper) missed 55% on 12-16 Aug against 21% for the rest; since 20 Aug they got 17 pings, took 0, missed 13, while the other twelve took 281 of 395. My replay has the four at 0.32-0.68 by 16 Aug and about 0 by 6 Sep; others dipped and recovered by 23 Aug. Turn-downs stayed flat.
- Not shown: that the four are slow answerers (no answer times; they missed 1-4% under 90s and took 4 of 5 on 15 Aug), or that a score of 0 means no pings (score is only 25%; a perfect score is worth about 19 minutes of travel).
- Fix ideas, in order: score a miss apart from a decline (`offer.py:30`, `history.py`, new value in `config.py`), let scores ease back (the 2019 TODO), a higher floor, a one-time reset for the four, then revisit the wait and weights once Ravi's data is in.
- Drafted, not sent: a one-question direct message to Wen Li (copy Marcus) on whether the change was meant to cover decliners, and a request to Ravi for all 16 scores, answer times and override coverage with the four called out at the bottom.

Session 5 wrap-up (16 Oct 2026):
- Helen (Director of Product) asked for a "what we'd build instead" one-pager and something clickable before anyone touches the code. Saved in `05-super-speed/`: `brief.md` (page 1 for Helen, page 2 a record for future readers), `brief-slide.pptx`, `prototype.html`. `brief-way-back.md` and `brief-way-back-slide.pptx` are older duplicates.
- Direction chosen: automatic recovery first (a missed ping costs less than a turn-down, scores ease back over days, a higher floor; the floor is 0.0 in `config.py` today), a handler "gone quiet" notice, and a logged manual restore only as a backstop. Rejected: a "guaranteed look" (sends callouts to worse-placed responders), a quiet config change, and reverting 4.2.
- Preferences: never tell a responder their standing or that they were "out of rotation"; the phone only shows that pings arrive. Product text is plain and present tense, with no history. Docs should make sense to someone reading months later.
- Prototype amounts are placeholders: taken +8, turned down -12 (current code), missed -6 (my guess), floor 0, all figures made up. The missed-ping value, the floor and the "gone quiet" threshold still need Ravi's data and Wen Li or Marcus.
- Still open: Helen's answer on replacing the quick fix and on Availability Confidence; Sofia on the notice and phone design; all brief numbers come from my replay, not Ravi's data.

Session 6 wrap-up (16 Oct 2026):
- Built a reusable skill, `review-checklist` (`.claude/skills/review-checklist/SKILL.md`): six checks (owner, success, scope matches, problem before fix, fix is customer experience, teams involved) plus nine success-metric questions. It reports a verdict, a table and the lines to fix.
- Ran it on `05-super-speed/brief.md`'s namesake in another person's repo (`drredhook/...`, not mine; I first reviewed my local copy by mistake). Always check which repo a link points to. That brief came back Ready with fixes: no owner for the whole work, no numeric targets or dates, a re-entry slot against a "no guaranteed pings" non-goal, scoring values ahead of customer experience, and phone, console and storage teams unnamed or not agreed.
- My own `05-super-speed/brief.md` was reviewed once, before the teams check was added: owner, success, scope and customer-experience wording were all partial; problem-before-fix passed. It has not been rerun on the six-check version.
- Scheduled `monday-review-checklist`: runs every Monday about 9:06 am on my local brief and reports what changed since last time. It only fires while the app is open. First run 19 Oct.
- The pasted "scheduled run" output is the file `06-sidekicks/scheduled-run-output.txt` (four briefs, one flag each: no owner, no success measure, scope grows, solution before problem). It ships with the course; it is not output from my Monday task.
- `final-report.html` (repo root) is the single-page report on 4.2: findings and exact prompts per module, the live prototype, and the review-checklist skill.
