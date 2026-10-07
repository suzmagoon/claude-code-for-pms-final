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

Sources: Priya's handoff (`00-rook/company/notes/handoff-from-priya.docx`, 21 Aug 2026), the
Rook wiki and the Rook database (both read-only, queried 6 Oct 2026). Database data ends
**6 Sep 2026**. Facts below are sourced; items marked *(inference)* are not.

**The user** is the new PM for Rook Dispatch, reporting to Helen Achebe.

### Company and products
Rook sells software to independent masked responders and the handlers who look after them
(subscription, priced per active responder). HQ Site Aleph; offices Berlin, Singapore.
Monthly release train (4.x). **Confidentiality is contractual:** responder cover identities are
never stored; never design anything that maps a cover identity to a legal one (Security
Policy 4.1) and never try to work out who anyone is.
- **Rook Dispatch** (the user's): ranks available responders for a callout, pings them one at a
  time until someone takes it. Handlers use the web console; responders use a native phone app.
  Routing config ships with the release, not as a runtime setting.
- **Rook Supply**: gear requisitions, maintenance, failure reports. Reads Dispatch's
  Responder Availability Record, so changes to how Dispatch calculates availability land in
  Supply's maintenance scheduling.

### People
- **Helen Achebe**, Director of Product (Site Aleph) — owns roadmap and commitments.
- **Marcus Oyelaran**, Eng Manager, Dispatch (Site Aleph) — candid; first stop; can pull numbers.
- **Wen Li**, Staff Engineer (Berlin) — built the ranking logic; the only real source on it.
  Was away 14–24 Aug, i.e. straight after 4.2 shipped.
- **Nadia Hoffmann**, Support Lead (Berlin) — owns the tickets; hears complaints first.
- **Sofia Marino**, Product Designer — owns console and phone app; ran the September handler interviews.
- **Ravi Menon**, Data Analyst (Singapore) — weekly acceptance-rate reporting.
- **Priya Raghunathan**, previous Dispatch PM, left 21 Aug; owned every Q3 roadmap item.

### Vocabulary (wiki glossary)
- **Responder** (phone; not an employee) / **Handler** (console; looks after a responder or small group).
- **Callout** → **ping** (one callout offered to one responder) → **taken**, **turned down**, or **missed**
  (no answer before the **ping wait** ends; both send it to the next responder).
- **Acceptance rate**: pings *taken* ÷ all pings (turned down and missed both count against it).
  Headline metric, reported weekly *in aggregate*. **Time-to-accept**: median seconds ping→taken.
  **Coverage gap**: no available responder had the needed **capability tags** (flight, structural-entry,
  hazmat-tolerant, cold-weather, aquatic, crowd-management, de-escalation) — "nobody could", not "nobody would".
- **Routing priority**: the ranking score. Inputs: proximity (travel-time estimate), availability,
  capability match, recent acceptance history (turning down/missing a ping lowers future rank).
- **Mutual aid**: cross-area cover; unsupported, on the Q4 exploration list.

### Where things stand
**Releases:** 4.0 (7 Apr: nav, profile redesign, override audit log noted) · 4.1 (16 Jun:
travel-time proximity, bulk callout, push reliability) · **4.2 (12 Aug):** proximity weighted up,
**ping wait cut 90→60 s**, console filters persist, 3 fixes. No 4.3 in the release log yet.

**Q3 roadmap** (last reviewed 30 Jun — stale; Q3 is over): 4.2 items *Change to who gets pinged*
and *Ping timeout tuning* shipped. **Availability Confidence** (confidence score beside a responder's
stated availability) was Committed for 4.2 and is absent from the release notes — the item that
"got squeezed". **Requisition approval chains** (Supply, 4.3) Committed, unshipped. *Handler phone app*
and *Shared cover between responders*: Exploring, Q4. No conversation yet with Helen on which
slipped items still stand.

**Briefs:** Bulk Callout (12 Jan; nobody has picked it up; no baseline), Routing Override Audit Log
(5 Mar; small, ready to build), Handler Phone App (8 Sep; Sofia exploring for Q4; prompted by
Aunt Dot), Requisition Approval Chains.

**The 4.2 problem — the data does not support "mostly seasonal"** (Priya's hypothesis):
- Acceptance was flat at 75–78% for 6 straight weeks (29 Jun–9 Aug), then fell at 4.2: week of
  10 Aug 54%, then 66%, 67%, **73% (week of 31 Aug)** — recovering but below baseline. Overall 76.6% before vs 64.0% after.
- **Missed pings went from ~2% to 13–21%**; turned-down fell (21% → 14–18%). So the drop is
  mostly timeouts, which fits the 60 s ping wait *(inference; not isolated from the weighting change)*.
- Pings per callout 1.23 → 1.39. Callouts/week fell ~140 → 110–127 post-4.2 (could be seasonal; no earlier slope).
- **Distribution skew:** pings to Vesper (Old Town), The Undertow (Harborside), Farlight (Uptown) and
  Meteor Mite (Eastgate) collapsed (e.g. Farlight 75 pings in the ~6 weeks before, 11 in the ~3.5 after); most others held or
  rose per week (Nightwell 95 → 70 pings, ~15/wk → ~19/wk), and Eastgate's other responder The Gale stayed busy. Consistent with proximity weighting starving
  responders away from callout hotspots *(inference)*. Weekly aggregate acceptance hides this.
- **Tickets:** ~6–8/week before → 20–32/week after; 25 of 25 filed the week of 31 Aug are still open.
  Themes: "ping moved on before they could answer", "phone hasn't gone off in 3 days", "much quieter
  than usual". Filter-persistence tickets exist but are low-severity noise (as Priya said); Ambrose wants a warning when a filter resets.
- Interviews (2–5 Sep, console redesign): Dot — responder loses the race to the phone; quiet weeks
  and fast-vanishing ones. Ambrose — closest call yet, "slower response used to still land the job".
  Kip — one responder silent, the other "on fire". Halloran — Supply complaints (11-day requisition, no feedback on failure reports, weak catalog search). Asks: dark mode, bigger text, per-responder alert sounds.

**Open to-dos:** talk to Helen on the Q3 slips; write the missing "how routing priority works"
doc with Wen Li; decide whether to test timeout vs weighting separately; Ravi's per-responder view.

### Still unknown
Routing weights; exact ping-wait/ranking change mechanics; September (only ~1 week of data);
whether callouts went unanswered; Q4 plan; Security Policy 4.1 text.

### Added at end of Module 1 session
- Priya's handoff named roles only; the wiki Team directory and database gave names. The wiki and the database are the sources that mattered: the database showed the 4.2 drop is not mostly seasonal (missed pings ~2% → ~18%, four responders nearly cut off).
- The wiki roadmap, releases log and handler interviews matter more than the handoff for what was committed and what handlers feel; the Q3 roadmap was last reviewed 30 Jun.
- Draft brief for Helen is at `00-rook/brief-for-helen-4.2.md`; prioritized next steps: separate timeout vs weighting, talk to Helen on Q3 slips, triage tickets with Nadia, per-responder metrics, write the ranking doc with Wen Li.
- Open: timeout vs weighting not isolated; no data after 6 Sep; owner of requisition approval chains (4.3) unclear; nothing yet confirmed with Helen or Wen Li.

### Added at end of Module 2 session
- Interviews are in the wiki "Customer interviews" database; tickets are in the database `support_tickets` table (147 rows), not in feedback folders. 40 tickets before 12 Aug (all closed); 107 after (83 open). 45 of those are new themes, all open: pings that vanished before the responder could answer (15) and responders gone quiet (30).
- Pings data: Farlight, The Undertow, Vesper and Meteor Mite fell ~70% (about 12 → 3–5 pings/wk) with 53–64% of their pings missed vs ~13% elsewhere, starting the 4.2 week; likely a rank spiral because misses lower rank *(inference)*. Callout demand per area stayed roughly flat, and Meteor Mite fell while The Gale (same area, Eastgate) rose. Ashgrove and Halfmoon, who filed ~29 tickets, fell only ~20–24%; Dot's and Kip's responders filed no tickets, so ticket volume does not track severity.
- Interviews vs tickets: handlers raise vanishing pings and console asks (dark mode, text size, alert sounds); tickets are dominated by quiet responders and persistence side effects. Halloran said maintenance scheduling improved, but ticket 3109 (29 Aug, open) says it booked a suit on a marathon day.
- Candidate top 3: isolate ping wait vs weighting; handler alerts (distinct sounds, live-callout indicator, notify handler); larger status text plus a filter-reset warning. Nothing confirmed with Helen or Wen Li.
- Open: callouts with no taken ping rose from ~4–12/wk to 14–17/wk after 4.2 (noisy, includes coverage gaps); no way to link a ticket to a specific ping; September data after 6 Sep; what Support has told the quiet-ticket handlers (ask Nadia).
- Ranked priority list (by impact of resolving it) is at `00-rook/priority-list.md`: routing fix, recover the four starved responders, answer open tickets, escalate Supply safety tickets, per-responder reporting, then Helen's Q3 decisions, handler alerts, routing doc, small console fixes.
- Weekly ticket counts: ~6–8 before 12 Aug, then 20, 27, 32, 25 (weeks of 10, 17, 24, 31 Aug). The brief for Helen's "starved because far from callout hotspots" line is not supported: Meteor Mite's area (Eastgate) is the busiest and The Gale in the same area rose, so the brief needs updating.
- Twelve handlers filed all tickets, each for one responder; Desmond Okafor (The Undertow, 21) and Linda Pruitt (Farlight, 19) are the heaviest and the best people to call first. Only Mr. Ambrose is in both tickets and interviews.
- Interviews alone would have misled: they point to console asks (dark mode, text size, alert sounds) and a rosy view of filter persistence and Supply maintenance, while tickets plus the pings table show the real problem is routing (vanishing and starved pings). Interviews explain why it matters and found Vesper and Meteor Mite; tickets show scale and timing; only the pings table shows cause and severity. Use all three together.
- Module 6 session: saved raw source material for later work: `00-rook/data/callout-history.csv` (160 rows = 16 responders × 10 weeks, 29 Jun–31 Aug, from the pings and responders tables; Farlight has 0 pings in the week of 31 Aug), the four September interviews as text in `00-rook/feedback/interviews/`, and the four product briefs in `06-sidekicks/briefs/`.
- The Requisition Approval Chains brief says its ask is only a second sign-off over $2,000, then adds four more features (route-around after 48 h, cross-site dashboard, handler visibility, replacing Halloran's spreadsheet); scope and phasing are marked TBD, and its owner is "the Rook Supply team".
- Responder-to-handler mapping is in the CSV: Kip handles both Meteor Mite and The Gale; every other responder has one handler.
- Module 6 re-run (7 Oct 2026): rebuilding `callout-history.csv` straight from the pings and responders tables gave 160 rows identical to the saved file, so the export is reproducible.
- The `handlers` table has 15 rows against 16 responders, because Kip looks after both Meteor Mite and The Gale.
- Setup check passed on 7 Oct 2026: both the Rook wiki and database connectors answer. Nothing new learned about the 4.2 problem; still nothing confirmed with Helen or Wen Li.
- Module 3 session (7 Oct 2026), from `callout-history.csv`: acceptance 76.8% before 4.2 vs 64.7% after (weeks 29 Jun–3 Aug vs 10–31 Aug; the week of 10 Aug straddles the release). Takes fell about 25 a week (132 → 107) and pings per taken ping rose 1.30 → 1.55. Without the four starved responders it is 77.2% → 68.5%, so they explain about a quarter of the drop; the other 12 were hit too (missed pings ~1% → 11–15% from 12 Aug).
- The four starved responders (Vesper, The Undertow, Farlight, Meteor Mite) lost 37 → 5 takes a week (−86%), while the other 12 gained about 7. Nothing in the database or wiki distinguishes them: area, handler, pre-4.2 acceptance and turned-down rates, ping order and night pings all look like everyone else's. The responders table holds only name, handler and area, with no tags or history, so why these four is still unexplained.
- Tickets and data agree on timing: first post-4.2 tickets are 12 Aug (6–8 a week before, 20 → 27 → 32 → 25 after); vanishing-ping tickets come first, "phone never goes off" tickets from 15 Aug, peaking the week of 24 Aug. They disagree on who: Vesper and Meteor Mite filed no tickets, while Halfmoon and Corporal Ashgrove filed about 11 quiet tickets despite only a 20–24% ping fall.
- Kip, Aunt Dot and Halloran filed zero tickets in the whole 10-week window, including the six weeks before 4.2 (Mr. Ambrose filed one); the other 11 handlers filed 3–4 each before 4.2. Tickets undercount their responders, so call those handlers directly.
- The 4.2 wiki page has an unanswered 14 Aug comment from Marcus asking whether the proximity change was meant to apply to responders who'd been turning jobs down (the config doesn't distinguish); Wen Li said they'd look after 24 Aug. Still open: ranking weights and before/after scores per responder, and the missed vs turned-down split for the four.
- Seasonality check (7 Oct 2026): the CSV does not support Priya's "just August being quiet". Acceptance was flat to 3 Aug and stepped down in the 4.2 week, pings sent stayed flat while takes fell, and the loss is concentrated in four responders. Callouts did fall about 21% (≈147 → 116 a week, database), so some quiet is real, but it doesn't explain the acceptance drop. No prior-year data exists in either source, so "every August" can't be tested; last year's pings would be the only way to close it.
