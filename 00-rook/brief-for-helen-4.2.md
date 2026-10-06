# Dispatch 4.2: what the data shows and what I propose

For: Helen Achebe · From: Dispatch PM · Data: Rook database, 29 Jun – 6 Sep 2026

## Summary
Acceptance fell sharply when 4.2 shipped on 12 Aug and has only partly recovered. The
data does not support "mostly seasonal". The drop is mainly missed pings, which points at the
ping wait cut (90 → 60 s) rather than the proximity change, but I have not yet separated
the two. I'm asking for your agreement on a plan to find out, and for a decision on the
Q3 items that slipped.

## What the data shows
| | Before 4.2 (29 Jun – 11 Aug) | After 4.2 (12 Aug – 6 Sep) |
| --- | --- | --- |
| Pings taken | 76.6% | 64.0% |
| Pings missed | 2.3% | 18.0% |
| Pings turned down | 21.1% | 18.0% |
| Pings per callout | 1.23 | 1.39 |

- Weekly acceptance held at 75–78% for six weeks before 4.2, so there is no seasonal slope
  leading into August. After: 54% (week of 10 Aug), 66%, 67%, 73% (week of 31 Aug).
- Turned-down pings fell, so responders aren't refusing more. They are running out of time.
- Four responders (Vesper, The Undertow, Farlight, Meteor Mite) have nearly stopped
  receiving pings; most others held steady or rose per week. The weekly aggregate hides this.
  It is consistent with proximity weighting starving responders far from callout hotspots,
  but that is my inference.
- Support tickets rose from about 6–8 a week to 20–32. All 25 filed in the week of
  31 Aug are still open. Themes: "ping moved on before they could answer", "phone hasn't gone
  off in 3 days", "much quieter than usual".
- Callouts per week dipped (~140 → 110–127). I can't yet say whether that is seasonal.

## What I don't know
- Whether the timeout, the weighting, or both drive the effect.
- Whether recovery continued after 6 Sep (the data ends there).
- Why the four quiet responders were dropped, without a written description of the ranking.

## Proposal
1. **This week:** with Wen Li and Marcus, test the 90 s wait on its own (flag or config), and
   have Ravi report acceptance per responder, with missed and turned-down split out.
2. **Then:** I bring you a recommendation (restore the wait, rebalance the weighting, or both).
   I am not proposing to revert 4.2: proximity was a requested change and reverting trades one
   group of unhappy responders for another.
3. **Alongside:** Wen Li and I write up how responders are ranked, which doesn't exist today.
4. **Metric:** report missed and turned-down separately, plus a "responders receiving almost no
   pings" view, so a skew like this shows up in the weekly report.

## Decision I need from you
Q3 slips. Priya's handover says this conversation never happened.
- **Availability Confidence** (committed for 4.2, not shipped): still a commitment, or moved?
- **Requisition approval chains** (Supply, committed for 4.3, no 4.3 in the release log):
  who owns it now that Priya has left?
- Roadmap last reviewed 30 Jun; I'd like to reset Q4 with you once 4.2 is stable.

## Risks if we wait
Open tickets keep growing; a responder going quiet for weeks may leave the platform
(revenue is per active responder); and the weighting may keep starving some responders.
