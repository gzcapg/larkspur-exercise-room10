# Overnight review: Larkspur disruption-care agent

**To:** gzcapg_larkspur-exercise-room10  
**From:** Larkspur client review agent, on behalf of Priya Raghavan  
**Re:** the disruption-care agent you walked us through in our last session  
**Generated:** 2026-09-22 17:22

## Priya's note

> Our vendor says we should just be using your best model.
>
> Why aren't we?
>
> Priya Raghavan, Larkspur Airlines

She sent that before this session opened. She means it. A vendor told her to buy
the biggest model, and she has a number to defend upstairs. Her four questions from
day one are still open. Naming a model answers none of them.

## Still open from day one

| Her question | What she means by it |
| --- | --- |
| **What it costs** | Per resolved contact, against the $6.90 a human contact costs us. |
| **When it is wrong** | The first untrue thing it says, and what happens after that. |
| **Who runs it** | In June, after you have left. |
| **What you left out** | The scope you cut, and why. |

## What the review agent found

Overnight, Larkspur pointed a review agent at your repository. It read the
code. It did not run your agent, and the only file it changed is this one. Each
item below names the file and the line it is about.

**1. agent.py's diff fixes a message-history bug but leaves TONE_ADDENDUM and EXTRA_TOOLS empty.**

The diff changes messages.append to store response.content instead of text_of(response), and moves answer = text_of(response) to after the loop. That's a real correctness fix to the transcript sent back to the model. TONE_ADDENDUM still scans at 0 characters and EXTRA_TOOLS at 0 declared, so no tone shaping or added tool has been built yet.

Run python3 run.py --show-tools and confirm the tool count is still 9 with no local executors.

**2. search_alternatives description grew from the placeholder "search" to 268 characters in the diff, but no eval case exists to check it changed behavior.**

The new text tells the model to only call this tool if the flight is cancelled or the delay exceeds 2 hours, and to use get_flight_status's output to decide. There is no evals/cases.json in the repository, so nothing confirms the model actually withholds the call on a short delay versus a long one.

Run python3 eval_harness.py once cases exist and paste the pass count for a short-delay case that should not call search_alternatives.

**3. readout-trace.json shows 0 tokens read and 0 written for prompt caching on a 13275-token input turn.**

The trace records tokens: 13275 in, 804 out with prompt caching: 0 read, 0 written, hit ratio None. That's a cost line, not a capability line: no cache_control block exists anywhere in agent.py per the static scan. A bigger or smaller model does not touch this number, only adding cache_control would.

Run python3 run.py --tool-tax and paste the per-call token cost breakdown.

**4. The wire run in readout-trace.json only exercised 4 of the 9 tools, none of the write-side ones.**

tools called, in order: lookup_booking, get_flight_status, check_policy, search_alternatives. hold_seat, confirm_rebooking, issue_voucher, escalate_to_human and send_confirmation never appear in this trace, so the confirmation_token gate on confirm_rebooking and the auto-approve threshold on issue_voucher are both unexercised.

Run python3 run.py <PNR> --trace on a booking shaped to reach confirm_rebooking and paste the resulting trace.

**5. Banked gates cover only 1.2, 1.3, 1.4; MAX_TOOL_CALLS = 8 has never been hit or tested in a trace.**

The evidence block lists gates banked: 1.2, 1.3, 1.4, generated 2026-09-22T15:53:38. The one committed trace used 4 API turns and 4 tool calls, nowhere near the cap of 8, so what happens when a real disruption case needs a 9th tool call, an escalate_to_human handoff mid-loop, is not shown anywhere in this material.

Run python3 verify.py 2.1 and paste the result to show what the loop does when it hits MAX_TOOL_CALLS.

## Your four answers

The four lines under `## Priya asked` in your PITCH.md are still empty. They
are one line each and they are not a coding job: cost, what happens when it is
wrong, who runs it in June, and what you left out. Whoever on your side is not
editing agent.py is the right person to write them, and they are the four
things I will ask about first.

## Before our next meeting

> Before our next meeting, tell me: which model should we be on, and how will you prove it is the right call?
>
> Priya Raghavan, Larkspur Airlines

Bring two things. A recommendation, and the measurement behind it. If the model is
not the problem, say so, and bring the number that shows it.

## What this review read

- `agent.py (231 lines)`
- `PITCH.md (unchanged template)`
- `TEAM.md (unchanged template)`
- `readout-trace.json`
- `readout.html (evidence block)`

Reviewer: `claude-sonnet-5`. Static read only: nothing in this repository was executed, and nothing was modified except this file. Larkspur Airlines is a fictional training scenario. Confidential, do not distribute.
