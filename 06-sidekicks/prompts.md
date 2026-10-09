# 06 · Sidekicks — prompts

**Context:** You joined Rook two weeks ago as PM on Dispatch.
Release 4.2 shipped on 12 August, just before you arrived, and
landed badly. You've spent five sessions on it: finding out what
went wrong, then building the fix nobody had — a working prototype,
by hand, in one sitting, for one room.

At the end of the session, type wrap up, and Claude Code saves the
prompts you wrote yourself below — not the starter prompt — updates
CLAUDE.md, and saves your work to GitHub. By Module 6 this file is a
prompt library built from your own questions.

---

### 1.
Use the rook-database connector to rebuild our weekly metrics export as 00-rook/data/callout-history.csv. One row per responder per week, for every week starting Monday 29 June through the week starting Monday 31 August 2026. Columns, in this order: week_starting, responder, handler, pings_sent, pings_taken. Use the pings table: pings_sent counts every ping that week, pings_taken counts the ones whose outcome is taken. Get each responder's handler from the responders table. Include weeks where a responder got no pings at all, with 0 in both columns. Sort by week, then responder. Save only the file, don't analyse it, and tell me how many rows you wrote.

### 2.
Use the rook-wiki connector to open the Customer interviews database under the Research page. Save each interview's full page text as its own file in 00-rook/feedback/interviews/, named after the handler: aunt-dot.txt, ambrose.txt, halloran.txt and kip.txt. Copy the text exactly as it is; don't summarise or reword anything. Tell me when the four files are saved.

### 3.
Use the rook-wiki connector to read every page under Product briefs. Save each one as a text file in 06-sidekicks/briefs/: bulk-callout.txt, handler-phone-app.txt, requisition-approval-chains.txt and routing-override-audit-log.txt. Copy the text exactly; don't summarise or reword anything. Tell me when the four files are saved.

### 4.
review-checklist on the requisition approval chains brief

### 5.
How do I use this skill int he future?

### 6.
/review-checklist/https://github.com/suzmagoon/claude-code-for-pms-final/blob/main/05-super-speed/brief.md

### 7.
/review-checklist/https://github.com/Klarson-622/claude-code-for-pms-final/blob/main/05-super-speed/brief.md

### 8.
Review the Klarson-622 version:

### 9.
Schedule review-checklist to run every Monday morning, and let me know what it finds. Nothing needs to be ready for it to fire today. I'm setting the habit, not waiting on the result.

### 10.
How would I point this skill at a different folder?

### 11.
disable this scheduled skill

### 12.
Is my latest prototype saved?
