# Dispatch priority list (ranked by impact of resolving it)

Sources: Rook database (to 6 Sep for pings, 7 Sep for tickets), wiki Customer interviews
(2–5 Sep), CLAUDE.md next steps, `brief-for-helen-4.2.md`. Ranking is my judgment, based on
how many responders it touches and how much it costs if left. Items marked *(inference)*
are not proven by the data.

| # | Item | Why it ranks here | Evidence | Needs |
|---|---|---|---|---|
| 1 | **Separate the 60 s ping wait from the proximity weighting, then fix what's hurting** | Touches every responder. Missed pings went from ~2% to 11–16% for everyone, and the cause is still unknown. Items 2, 3 and most of 5 depend on it. | 45 open tickets (15 vanishing, 30 quiet); 3 of 4 handlers on vanishing pings; weekly miss rate steady at ~13% after 4.2 | Wen Li, Marcus, a release slot (routing config ships with a release) |
| 2 | **Recover the four starved responders** (Farlight, The Undertow, Vesper, Meteor Mite) | A quarter of the roster is effectively cut off, and revenue is per active responder. Fixing item 1 may not be enough: missed pings lower rank, so they may stay stuck *(inference)*. | Pings down ~70% to 3–5 a week, 53–64% of their pings missed vs ~13% elsewhere; 1 ping/week by early Sept | Wen Li to confirm how long a rank penalty lasts |
| 3 | **Answer the open tickets and tell the affected handlers what's happening** | Cheap, and stops confidence draining now. 83 tickets open; handlers say responders ask whether they're still "in the system". | Quiet-ticket handlers (Linda Pruitt, Desmond Okafor, Yusuf Demir, Simone Fischer) filed 69 tickets between them; none of the 45 core tickets is closed | Nadia Hoffmann |
| 4 | **Escalate the safety-flavored Supply tickets to their owner** | Not Dispatch, but cracked vest plates, a slow grapple line in cold weather and unanswered failure reports are physical risks to responders. Owner of requisition approval chains is unclear. | Tickets 3074, 3092, 3032, 3101; Halloran's 11-day plate wait | Helen to name the owner |
| 5 | **Per-responder reporting: missed and turned-down split, plus an "almost no pings" view** | Detects a skew like this in a week instead of a month, and shows whether items 1–2 worked. The weekly aggregate hid it (73% by 31 Aug). | Ravi's per-responder view is an open to-do | Ravi Menon |
| 6 | **Get Helen's decisions on the Q3 slips** (Availability Confidence, requisition approval chains, Q4 reset) | Unblocks roadmap, but doesn't change the live problem by itself. | Roadmap last reviewed 30 Jun; no conversation yet | Helen Achebe |
| 7 | **Handler alerts:** distinct sound per responder, unmissable live-callout indicator, notify the handler when a ping lands | 3 of 4 interviews. Helps handlers notice a callout, but won't help a responder who loses the race to a timeout, so it follows item 1. | Dot, Ambrose, Kip; ticket 3031 | Sofia Marino |
| 8 | **Write "how routing priority works" with Wen Li** | Prevents the next surprise and explains why four responders were dropped. Ranked low on impact but worth starting now: it feeds items 1 and 2. | Open to-do; no written description exists | Wen Li |
| 9 | **Larger status badge text and a warning when a filter resets** | Cheap, visible wins; 2 of 4 interviews on text size, and two sources ask for the reset warning. | Dot, Ambrose; tickets 3013, 3142, 3044, 3134 | Sofia Marino |
| 10 | **Persistence leftovers and duplicate pings:** filters saved on the wrong computer or user, no "clear all", the same ping arriving twice | Real but small. 14 filter tickets and 3 duplicate-notification tickets. | Tickets 3042, 3046, 3066, 3085; 3007, 3016, 3040 | Marcus Oyelaran |
| 11 | **Backlog:** dark mode, screen reader support, handover notes, mark-on-leave, export history, account and admin friction | Consistent before and after 4.2, so not part of this problem. Screen reader support is the one I'd move up first. | Dark mode: Kip, 4 tickets; screen reader: 2 tickets | Triage later |

## Not ranked

- Handler phone app (Exploring, Q4) and mutual aid (unsupported): not mentioned in the feedback.
