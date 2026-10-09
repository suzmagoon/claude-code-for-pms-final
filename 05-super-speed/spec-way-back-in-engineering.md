# Engineering spec: a way back in for quiet responders

Draft v1 · 9 Oct 2026 · Dispatch PM · Companion to `prd-way-back-in.md`
Code reference: `00-rook/code/dispatch-routing/` (single commit from 2 Oct, so no history before 4.2).

**Read this first.** Every number marked `PROPOSED` is my starting value, not a measured or agreed one. Wen Li owns the ranking and has not reviewed this. Items marked `NEEDS DECISION` can't be built until someone answers. Everything else is derived from the code as it stands.

## 1. Goal
When a responder goes quiet, (a) their handler can see that and why, and (b) the system gives them a way back that doesn't depend on being asked first.

Non-goals: no change to proximity/capability weights, no change to `OFFER_TIMEOUT_SECONDS`, no change to the shape of `availability.current_record()` (Supply reads it), nothing that maps cover identity to legal identity (Security Policy 4.1).

## 2. Current behaviour (what we're changing)
- `routing.rank_for_callout()` sorts everyone available by `score()`. Nobody is removed; order only.
- `offer.dispatch()` walks the list top-down and stops at the first `TAKEN`.
- `offer.dispatch()` calls `history.record_declined()` for both `TURNED_DOWN` and `MISSED`: −0.12 each. `offer_to()`'s docstring says this is intentional.
- `history.record_accepted()` is the only way up: +0.08. Scores are clamped to 0.0–1.0, new responders start at 0.5.
- `history._scores` is an in-memory dict keyed by `responder.name`. No persistence, no timestamps, no log of why anyone was ranked where.
- `push_to_device()` returns nothing, so there is no delivery signal.
- Weights since 4.2: proximity 0.60, recent acceptance 0.25, capability 0.15. A score of 0.0 vs 0.5 is worth 0.125 of total score, so a quiet responder can be outranked by a small proximity difference.

## 3. Changes, in build order
Each stage ships separately, behind its own constant in `config.py` (default off until that stage's checks pass). Config ships with the release, so "flag" here means a constant, not a runtime toggle.

### Stage 1: Ranking log + quiet state (no routing change)
**New module `ranking_log.py`.** One record per ping:

| field | notes |
|---|---|
| `responder_id` | the existing responder identifier. Do not add or join any identity data |
| `callout_id`, `sent_at` | |
| `rank_position`, `candidates_count` | position in the list when pinged |
| `score_total`, `score_proximity`, `score_acceptance`, `score_capability` | components as computed in `score()` |
| `outcome` | `taken` / `turned down` / `missed` (and later `undelivered`) |
| `answered_at` | null if missed |

- `offer.dispatch()` writes a record after each `offer_to()`. `routing.score()` needs a variant that returns the components so the log can store them without recomputing.
- **Persistence:** the log must survive restarts. `NEEDS DECISION` (Marcus): where it is stored. Nothing in this folder shows a data store, and the existing `pings` table in the database is a possible home but I can't confirm it is written by this service.
- **Quiet state** is derived, not stored. A responder is *quiet* when they are currently available **and** have had no ping for `QUIET_AFTER_DAYS` (`PROPOSED` 3, from Kip's "phone hasn't gone off in 3 days" ticket theme). Responders marked unavailable are never quiet.
  - Gap: the code has no availability *history*, so "available the whole time" can't be checked. v1 uses "available now". `NEEDS DECISION`: acceptable?
- **Read API for the console** (the console is outside this folder; contract only):
  `GET /responders/{id}/ping-status` returns `{state: "active"|"quiet", days_since_last_ping, last_n_pings: [{sent_at, outcome, rank_position}], current_score, score_reason}`.
  `score_reason` is a short, fixed-vocabulary enum (e.g. `missed_pings_recent`, `turned_down_recent`, `new_responder`) so the console can render plain text. Copy is Sofia's.

Acceptance: for any responder, the log answers "why were they ranked where they were, and what happened" for every ping since deploy; quiet state matches a hand-count on a test fixture; no change to who gets pinged.

### Stage 2: Missed ≠ turned down, plus decay
**`config.py`:** add `MISSED_PENALTY` (`PROPOSED` 0.04; `DECLINE_PENALTY` stays 0.12) and `DECAY_PER_DAY` (`PROPOSED` 0.02) with `DECAY_TARGET = NEUTRAL_SCORE`.

**`offer.py`:** replace the shared `record_declined()` call with `history.record_outcome(responder, outcome)`. `TURNED_DOWN` keeps −0.12; `MISSED` applies `MISSED_PENALTY`. Update the `offer_to()` docstring, which currently says the two are the same on purpose.

**`history.py`:** store `(score, updated_at)` per responder. `recent_acceptance()` applies decay on read: a score *below* `NEUTRAL_SCORE` moves toward it by `DECAY_PER_DAY` per elapsed day, never past it. Scores above neutral do not decay. This needs `now` injectable for tests.

- With the proposed values, a score of 0.0 returns to 0.5 in 25 days with no activity; a take is +0.08 on top.
- **This reverses the 2019 TODO's "against" argument.** `NEEDS DECISION` (Wen Li, Helen): is decay for *missed* pings acceptable even if decay for *turned-down* pings is not? Option: apply decay only to the missed-ping part of the penalty. That needs tracking the two separately; costs more, keeps the 2019 intent for refusals.
- Resolve the TODO in the code with a comment pointing to the decision, not just delete it.
- **Persistence:** scores are in memory, so every restart resets everyone to 0.5 and quietly undoes both the problem and this fix. `NEEDS DECISION` (Wen Li): is that intended? If not, scores need storage too. State this in release notes either way.

Acceptance: unit tests for each outcome's delta; decay never crosses neutral; decay does not affect scores ≥ 0.5; a responder at 0.0 with no pings is at 0.5 after 25 simulated days.

### Stage 3: Re-entry ping
**`config.py`:** `RE_ENTRY_ENABLED = False`, `RE_ENTRY_AFTER_DAYS` (`PROPOSED` 3, same as quiet), `RE_ENTRY_MARGIN` (`PROPOSED` 0.15), `RE_ENTRY_MAX_PER_DAY` (`PROPOSED` 1).

**`routing.rank_for_callout()`:** after sorting, if enabled, find the first candidate that is quiet, fully capable (`capability_score == 1.0`) and whose `score` is within `RE_ENTRY_MARGIN` of the top score. Move them to position 0. The list order is otherwise unchanged and nobody is removed, preserving the README's "ranking is order only" rule.
- At most one promotion per callout, and at most `RE_ENTRY_MAX_PER_DAY` per responder. A responder who misses a re-entry ping isn't promoted again for `RE_ENTRY_AFTER_DAYS`.
- The ping to the phone is a normal ping. No new UI on the responder's side in v1.
- Log field `reason = "re_entry"` so we can measure these separately.
- The 0.15 margin is roughly 11 minutes of travel time at the current weights (0.6 × minutes/45). `PROPOSED`; the right number depends on real score distributions, which we don't have. `NEEDS DECISION` (Wen Li): margin, and whether urgent callouts should be excluded (the code has no urgency field on `callout`, so v1 cannot exclude them).
- A callout is a real incident. Promoting someone within the margin costs the top candidate's slot. Accept this only if the margin stays small; the log lets us check how often the promoted responder was slower.

Acceptance: with the flag off, ranking is identical to today (regression test over recorded callouts); with it on, promotion obeys all four conditions; a promoted responder who takes the callout has `record_outcome(TAKEN)` applied normally.

### Stage 4 (optional): Delivery signal
`push_to_device()` returns a delivery status. If the push provider reports not-delivered, outcome is `undelivered`, with no score penalty, and the log records it. `NEEDS DECISION` (Marcus): does the push provider expose delivery status? If not, drop this stage. It is not needed for stages 1–3.

## 4. Edge cases
- New responder: starts at 0.5, never quiet until `QUIET_AFTER_DAYS` has passed.
- Responder in a region with few callouts: "quiet" can be low demand, not starvation. Compare against regional callout volume before alerting handlers. `NEEDS DECISION`: v1 shows the state with the callout count for the area (Eastgate was the busiest area, and Meteor Mite was still starved, so volume alone isn't a reason to hide it).
- Handler with two responders (Kip): state is per responder; the console shows both.
- Bulk callout (4.1): each ping logs separately; re-entry applies per callout and counts toward `RE_ENTRY_MAX_PER_DAY`.
- Clock/timezone: store UTC; compute days in UTC.
- Rollback: set constants back to current values (`MISSED_PENALTY = DECLINE_PENALTY`, `DECAY_PER_DAY = 0`, `RE_ENTRY_ENABLED = False`) and ship. Old behaviour is fully recoverable.

## 5. Not changing
`WEIGHT_*`, `OFFER_TIMEOUT_SECONDS`, `PROXIMITY_HORIZON_MINUTES`, `availability.py` (including `current_record()` shape), capability matching, anything about who is "available". Supply is unaffected, but confirm with the Supply team before shipping since it reads availability.

## 6. Test plan
- Unit tests: per section above, using an injectable clock.
- Replay test: run Stage 3 against recorded callouts (the `pings` and `responders` data, 29 Jun–6 Sep). Report how many re-entry promotions happen per week and how many would have gone to the four starved responders. This needs ranking scores that don't exist yet, so Stage 1's log has to run first.
- Do not ship Stage 2 and 3 in the same release; measure each separately. 4.2 changed two things at once and we still can't separate them.

## 7. Measurement (per responder, not aggregate)
From the Stage 1 log, weekly per responder: pings, takes, miss rate, rank position, share of pings flagged `re_entry`. Ravi owns the report. Baselines from the CSV: the four starved responders had 0–3 pings in the last two weeks of data; the other 12 had ~11–15% missed pings after 4.2.

## 8. Open decisions (blockers)
| # | Decision | Owner |
|---|---|---|
| 1 | Log storage; is there an existing store | Marcus |
| 2 | Scores persist across restarts? | Wen Li |
| 3 | Decay: allowed for missed only, or all? (2019 TODO) | Wen Li, Helen |
| 4 | Values: `MISSED_PENALTY`, `DECAY_PER_DAY`, `RE_ENTRY_MARGIN` | Wen Li |
| 5 | Quiet = "available now" without availability history | Wen Li, Marcus |
| 6 | Urgent-callout exclusion (no urgency field today) | Wen Li, Helen |
| 7 | Push delivery status exists? | Marcus |
| 8 | Release slot (no 4.3 in the log yet) and effort | Marcus |
| 9 | Console copy and placement of the quiet state | Sofia |
| 10 | Why the four were starved (scores before and after 4.2) | Wen Li |

## 9. Known gaps in the code that affect this work
Placeholders: `push_to_device`, `poll_device`, `withdraw_from_device`, `available_for`, `travel_time_minutes`, `current_record`. We can't see how travel time is estimated, which is the biggest input to ranking.
Scores are keyed by `responder.name`. If two responders can share a name, they would share a score. Use the responder identifier instead (but do not introduce anything that links to a legal identity).
`offer_to()` busy-polls in a tight loop until the deadline; unrelated to this work, noted in passing.
