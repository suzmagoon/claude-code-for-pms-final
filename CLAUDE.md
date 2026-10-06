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
