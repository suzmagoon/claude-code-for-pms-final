# PRD: A way back in for quiet responders (Rook Dispatch)

Draft · 9 Oct 2026 · Author: Dispatch PM · For: Helen Achebe
Status: proposal, not agreed. Nothing here is confirmed with Wen Li, Marcus or Kip.
Evidence tags: **[measured]** from the database or CSV · **[observed]** from tickets, interviews or the code · **[inferred]** our reading, unconfirmed · **[blocked]** needs a named person.

## 1. The person and the moment
Kip looks after two responders in Eastgate: The Gale, who is "on fire", and Meteor Mite, who has gone silent. Meteor Mite's phone hasn't gone off in days. Kip can't tell whether she is broken, ranked low or unlucky, and nothing he can do changes it. Kip filed zero tickets in the whole window, so Support never heard from him.

Next time she goes quiet, Kip should see this in the console: *"Gone quiet. No pings in 3 days. 4 recent pings missed. Ranked low as a result. A re-entry ping is planned."* Meteor Mite should get asked again instead of waiting indefinitely.

One line: **tell the handler their responder has gone quiet, why, and what Dispatch will do about it.**

## 2. Evidence
| Claim | Tag |
|---|---|
| Acceptance flat at 75–78% to 3 Aug, then fell at 4.2 (76.8% → 64.7%, weeks 29 Jun–3 Aug vs 10–31 Aug) | measured |
| Missed pings ~1–2% → 11–21% after 4.2 | measured |
| Vesper, The Undertow, Farlight, Meteor Mite fell from about 37 takes a week to 5; the other 12 gained about 7 | measured |
| None of the four recovered: 9 pings between them from 24 Aug to 6 Sep | measured |
| Nothing in the data distinguishes the four (area, handler, pre-4.2 acceptance) | measured |
| 4.2 changed weights (proximity 0.45 → 0.60, recent acceptance 0.40 → 0.25) and the ping wait (90 → 60 s) | observed (code) |
| A missed ping scores like a refusal (−0.12); the only way up is a take (+0.08); a score-0 responder needs about 7 takes to reach 0.5 | observed (code) |
| The list stops at the first yes, so a bottom-ranked responder is rarely asked | observed (code) |
| No delivery check; scores in memory only; nothing logged about ranking | observed (code) |
| 107 tickets after 12 Aug (83 open); 45 are new themes, all open (vanishing pings 15, gone quiet 30) | measured |
| A rank spiral explains the four | **inferred** |
| Weighting did more damage than the 60 s wait, or the reverse | **blocked** (not isolated) |
| "Mostly seasonal" (Priya's hypothesis) | not supported: callouts fell about 21%, but the acceptance drop and the four-responder pattern aren't explained by it. No prior-year data exists, so it can't be fully closed |

## 3. The decision
Two questions need an answer from Helen, with Wen Li's input:
1. Should a missed ping cost the same as a turn-down?
2. Should a responder who has gone quiet have a way back that doesn't depend on being asked first?

History: Wen's 2019 TODO on score decay is unresolved. Marcus asked on 14 Aug whether the new weights were meant to apply to responders who'd been turning jobs down; the config can't distinguish them, and nobody has answered. The four starved responders were not habitual decliners before 4.2.

Owner of the decision: Helen. Owner of the answer on intent: Wen Li (or Helen/Priya). **[blocked]**

## 4. What we'd build
1. **Gone-quiet state in the console.** A marker on the responder row and an explanation in the detail panel: days since last ping, recent outcomes, why they are ranked low. Needs ranking data to be logged for the first time.
2. **Re-entry ping.** After a quiet period, offer a callout the responder is qualified for and where they rank close to the top candidate. Normal ping on the phone. Never offered where the callout needs the best-placed responder.
3. **Scoring change.** A missed ping costs less than a turn-down. Low scores drift toward neutral (0.5) over time, resolving the 2019 TODO.
4. **Delivery check** (if feasible): don't count a ping against a responder if it never reached the phone.

Quiet period length, the "close enough" rule, the new miss penalty and the decay rate are all open design parameters **[blocked: Wen Li]**.

## 5. Scope and non-goals
Does not: revert 4.2; change proximity weighting; change the ping wait; guarantee anyone a share of callouts; map cover identities to legal ones (Security Policy 4.1); change availability calculation; add alerts for untaken callouts, mutual aid, the handler phone app, or console asks (dark mode, text size, alert sounds).

## 6. Prototype
Clickable console and phone screens: **not built yet**. Planned: Kip's console view of Meteor Mite and The Gale, and Meteor Mite's phone ping.

## 7. Success measures
Measured **per responder**, because the weekly aggregate hid this problem.
- Each of the four is pinged at a rate in line with the other 12. Baseline: 0–3 pings in two weeks.
- Missed pings fall from 11–15% toward the pre-4.2 ~1%.
- Open quiet-responder tickets are answered and stop growing.
- Re-entry pings: share taken, and what happens to rank afterwards.

Baseline caveat: data ends 6 Sep, so we don't know whether the four have recovered since. **[blocked: Ravi, refreshed data and a per-responder view]**

## 8. Risks and dependencies
- **Supply** reads the Responder Availability Record. The brief changes ranking, not availability, but this needs checking with the Supply team.
- **Wen Li** is the only source on the ranking, and many functions in the code folder are placeholders (including travel-time estimation).
- **Re-entry misfires:** offering a callout to someone who shouldn't have it. Mitigated by the qualified-and-close-to-top rule.
- **Two changes at once** is what made 4.2 hard to read. Plan to ship and measure the scoring change and the re-entry ping separately.
- **Handlers may not use the marker.** Kip filed no tickets, so we can't assume he'd act on it. Needs a conversation.
- **Security Policy 4.1:** text not yet read. Keep everything keyed to the responder record.

## 9. Rollout
Routing config ships with the release, not as a runtime setting. Target release: **[blocked: Marcus, effort and slot; no 4.3 in the release log yet]**. Proposed order: (1) logging and the console marker (no routing change), (2) scoring change, (3) re-entry ping, each measured before the next. Rollback: revert the release; scores are in memory, so a restart resets them.

## 10. Open questions and owners
| Question | Owner |
|---|---|
| Score per responder before and after 4.2; how a quiet responder recovers today | Wen Li, via Marcus |
| Was applying the new weights to decliners a decision? | Wen Li / Helen |
| Do scores survive a restart? | Wen Li |
| Weight vs 60 s wait: which did more damage? | Marcus + Wen Li (change one at a time) |
| Do the four have pings after 6 Sep? | Ravi |
| Would Kip use a "gone quiet" marker, and what would he do with it? | Kip (call), Sofia (design) |
| Why did these four get starved? | Wen Li |
| Effort and release slot | Marcus |
| Replies to open quiet tickets (Linda Pruitt, Desmond Okafor first) | Nadia |
