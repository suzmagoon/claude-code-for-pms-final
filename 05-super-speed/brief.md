# Dispatch: a way back in for quiet responders

Draft for Helen · 9 Oct 2026 · Rook Dispatch PM
Status: proposal. Nothing here is confirmed with Wen Li. Items marked *(to confirm)* depend on her.

## The problem in one line
Once a responder goes quiet, the only thing that brings them back is being asked, and being quiet is what stops them being asked.

## What we know
- 4.2 moved proximity weight 0.45 → 0.60 and recent acceptance 0.40 → 0.25, and cut the ping wait 90 → 60 s.
- Today a missed ping costs the same as a refusal (−0.12). The only way up is taking a callout (+0.08). The list stops at the first yes, so a bottom-ranked responder is rarely asked.
- Four responders (Vesper, The Undertow, Farlight, Meteor Mite) went from about 37 takes a week to 5. They were not habitual decliners before 4.2. From 24 Aug to 6 Sep they got 9 pings between them, all missed or turned down.
- Their handlers keep asking if the responder is "still in the system". Many tickets are open with no answer.
- Marcus asked on 14 Aug whether the new weighting was meant to apply to responders who'd been turning jobs down. The config can't tell them apart, and the question is still unanswered.

## Who it's for
1. **A handler like Kip** (looks after a responder who has gone silent and one who is "on fire"). They can't tell whether the quiet one is broken, ranked low, or just unlucky.
2. **The responder who has gone quiet.** Their phone hasn't gone off in days, and nothing they do can change that.

## What changes for them
**Kip (console)**
- A "gone quiet" state on the responder: *"No pings in 3 days. Ranked low because of 4 missed pings. Dispatch will offer them a re-entry ping."* It explains the cause in plain words, so Kip can act on it or reply to the responder.
- The same information in the responder's detail panel, with a record of recent pings and their outcomes. Today nothing is logged about ranking, so this is the first place anyone can see it.
- Support has something concrete to say to the open quiet tickets.

**The quiet responder (phone)**
- **A way back that doesn't depend on luck.** After a set quiet period, Dispatch offers them a re-entry ping: a callout they are qualified for, where they rank close to the top candidate. Taking it starts lifting their rank. The window and the "close enough" rule are open design questions *(to confirm with Wen)*.
- **A missed ping is not scored as a refusal.** It costs less than a turn-down, and a score that has been low for a while drifts back toward the neutral 0.5 starting point. This is the unresolved 2019 TODO on score decay, which Helen was pointing at.
- Where possible, a ping that didn't reach the phone is not counted against them *(to confirm: the code has no delivery check)*.

## What it deliberately doesn't do
- **Doesn't revert 4.2 or change the proximity weighting.** The change was asked for, and we haven't isolated whether the weighting or the 60 s wait did more damage.
- **Doesn't change the ping wait.** Whether to restore 90 s, or vary it per responder, is a separate test.
- **Doesn't guarantee anyone a share of callouts.** A re-entry ping goes to qualified responders only, and never at the expense of a callout that needs the best-placed responder.
- **Doesn't map cover identities to legal ones** or try to work out who anyone is (Security Policy 4.1). Everything is keyed to the responder record.
- **Doesn't cover the other gaps in the code:** alerting when nobody takes a callout, mutual aid, handler phone app, console asks (dark mode, text size, alert sounds).
- **Doesn't change Supply.** Availability calculation is untouched, so Supply's maintenance scheduling is not affected. This needs checking because Supply reads Dispatch's Responder Availability Record.

## How we'd know it worked
- Each of the four starved responders is pinged at least a few times a week again, in line with the other twelve, rather than 0–3 in two weeks. This is measured per responder, because the weekly aggregate hid the problem.
- Missed pings fall back from 11–15% toward the pre-4.2 ~1%.
- The 83 open tickets get answered, and quiet-responder tickets stop growing.

## Open questions
- Wen Li: each responder's score before and after 4.2, and how a quiet responder recovers today *(to confirm)*.
- Wen Li or Helen: was applying the new weights to decliners a decision, or did it fall out of the config?
- Do scores survive a restart? The code keeps them in memory only.
- Data ends 6 Sep, so we don't know if the four have recovered since.
- How is travel time estimated? Many functions in the code folder are placeholders.

## Before anything ships
1. Marcus asks Wen for the scores and the recovery rules.
2. Sofia sketches the handler "gone quiet" state and the responder re-entry ping.
3. Ravi sets up a per-responder view so we can see whether it works.
4. Nadia and I call Desmond Okafor (The Undertow), Linda Pruitt (Farlight) and Kip.
