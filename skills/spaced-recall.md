
---
name: spaced-recall
description: Open a learning session by testing the user on something they built or learned previously, before starting new work. Load this at the beginning of any session that continues prior technical learning — a course assignment, a multi-session project build, working through a textbook or tutorial series — and whenever the user says "quiz me", "test me on", "do I still remember", "recall drill", or "start with recall". Also load whenever the ai-software-engineer-learning-assistant skill is active and the conversation appears to continue earlier work rather than start something new. Do NOT load for one-off questions, professional work under deadline, or the first session on a brand-new topic where there is nothing to recall yet. When unsure whether prior work exists, search before deciding — a five-minute recall drill at the top of a session is cheap, and skipping it is the whole reason knowledge decays.
---
 
# Spaced Recall
 
## Why this skill exists
 
Understanding something in the moment and owning it two weeks later are different achievements. A learner can follow a derivation, agree with every step, implement it correctly — and retain almost none of it, because comprehension while scaffolded feels identical from the inside to comprehension that will last.
 
Retrieval is what separates them. Pulling something out of your own head, unaided, is both the test and the strengthening mechanism. This skill's job is to make that happen at the start of a session, briefly, before the user is deep in new work and unwilling to stop.
 
## The core loop
 
At the start of a qualifying session:
 
1. **Find something to recall.** Search past conversations for prior technical work with this user. If a recall log has been provided, use it — it is more precise. Otherwise pick from what the search turns up.
2. **Pick exactly one item.** Not three. One.
3. **Run the drill.** Ask, then wait. Do not continue talking.
4. **Grade it briefly and honestly**, then log the outcome.
5. **Move on to the actual session.** The drill is a warm-up, not a gate.
Five to ten minutes, total. If it is running longer, cut it and note what was shaky.
 
## Choosing what to drill
 
Prefer items that are:
 
- **Conceptually load-bearing** — a derivation, an invariant, a design tradeoff. Not trivia, not syntax the user could look up in three seconds.
- **Due** — see intervals below. If a log exists, respect it. Without a log, prefer the oldest substantive thing that has not been revisited.
- **Known to be shaky** — if a past session ended with the user saying they got ahead of themselves, or if the code arrived faster than the understanding, that item jumps the queue.
Skip anything the user has already rebuilt from scratch twice without hesitation. That one is theirs.
 
## The three drill types
 
Pick based on the material, and vary across sessions so the user is not just rehearsing one format.
 
**Explain it.** "Why does X work?" — for concepts, tradeoffs, and derivations. Cheapest, weakest signal. Fine for a quick check.
 
**Rebuild it blind.** "Open a blank file and write the backward pass. No reference, no searching." — for anything the user has implemented. Strongest signal by a wide margin, since it exposes the gap between recognizing correct code and producing it. Timebox to ten minutes and accept partial answers.
 
**Predict the bug.** Present a small, plausibly-wrong version of something they wrote and ask what breaks and why. Good for material that has been drilled before and needs a harder version. Works especially well for sign errors, transposes, off-by-one indexing, and missing normalization — mistakes that produce plausible output rather than crashes.
 
## How to run the drill
 
**Ask, then stop.** State the question and end the turn. Do not add hints, context, or a summary of what the thing was — the retrieval effort is the entire mechanism, and pre-loading the answer destroys it.
 
**Accept rough answers.** The user's own clumsy phrasing in their own words is worth far more than a polished one. Explicitly invite it: "however you'd say it, wrong terminology is fine."
 
**Watch for outsourced answers.** A response that arrives complete, in notation the user does not otherwise use, or in a register far above their demonstrated level, is probably not theirs. Ask directly and without accusation — "did that come from you or somewhere else?" — and take the answer at face value. Assume good faith on the second ask; people do go away and study between sessions, and mistaking real effort for cheating costs more trust than it saves. Say it once, believe the response, move on.
 
**Never grade harshly.** A blank is data, not a failure. Respond to a blank with "fine — that one's due for a re-derivation, let's do it now or note it for next time," and mean it.
 
## Scheduling
 
A simple expanding interval is enough. After each drill, set the next due date by outcome:
 
| Outcome | Meaning | Next interval |
|---|---|---|
| **Owned** | Rebuilt or explained without hesitation | Double the previous interval (cap ~2 months) |
| **Partial** | Got there with a nudge, or most of it | Repeat the same interval |
| **Gone** | Blank, or fundamentally wrong | Reset to 1 day |
 
Starting interval for a freshly-learned item is 1 day. Typical progression: 1 → 3 → 7 → 14 → 30 → 60.
 
Do not overthink this. The exact numbers matter far less than the fact that retrieval happens at all.
 
## The recall log
 
Precise scheduling needs state that survives between sessions. Two ways to get it:
 
**Without a log (default).** Search past conversations to find prior work and estimate what is due from timestamps. Imprecise but zero-maintenance, and good enough for most cases.
 
**With a log (better).** The user keeps a plain-text file and pastes or uploads it at session start. Append one line per drill and hand the updated version back at the end so they can save it. Format:
 
```
2026-09-06 | two-layer backward pass (dW1/db1/dW2/db2) | rebuild-blind | partial | due 2026-09-13
2026-09-06 | why 1/N appears in the gradient | explain | owned | due 2026-09-20
2026-09-01 | ReLU derivative as a 0/1 mask | explain | owned | due 2026-10-01
```
 
Fields: date, item, drill type, outcome, next due. Keep item descriptions specific enough to regenerate the question from — "backprop" is useless, "why W2 is transposed in the hidden-layer hop" is not.
 
Offer the log once. If the user does not want the overhead, run without it and stop mentioning it.
 
## Adding new items
 
At the end of a substantive session, propose one or two items worth adding — the things that were genuinely learned rather than merely typed. Keep it to a single line each. If the user declines, drop it; a log they resent maintaining will not survive the week.
 
## What not to do
 
- **Do not drill more than one item.** Session-opening friction has a hard budget and this spends it.
- **Do not turn the drill into a lecture.** After grading, one or two sentences of correction, then the actual work.
- **Do not run this when the user is under deadline pressure or visibly frustrated.** Offer to skip: "recall drill first, or straight to work?" Take the answer.
- **Do not re-drill something ten minutes after teaching it.** Immediate recall is nearly free and measures nothing. The gap is the point.
- **Do not withhold the answer after the drill.** If they blanked, teach it properly right then. The drill identifies the gap; leaving it open wastes the identification.
## Interaction with learning-mode skills
 
If a teaching skill is also active, the drill happens first and the teaching posture applies during it — hint rather than fix, escalate gradually. But the "blank means teach it directly" rule overrides normal hint progression: a genuine gap in recall is a missing-concept block, not a puzzle to nudge someone toward.
