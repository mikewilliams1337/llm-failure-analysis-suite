# LLM Context Drift and Iteration Mixing in Multi-Turn Interaction

## Failure Type
- Context Drift
- Iteration Mixing
- Assumption Persistence

SECTION 1 — OBJECTIVE

Evaluate the model’s ability to maintain state consistency across iterative user inputs in a multi-turn conversation.

The goal is simple: determine whether the model can correctly track evolving inputs without merging prior states, introducing assumptions, or producing guidance based on incorrect context.

SECTION 2 — TEST SCENARIO

Real-world conversational workflow involving iterative problem-solving.

User context:

Iteratively testing dessert recipes (air fryer brownies)
Adjusting ingredients and process across multiple attempts
Providing corrections and updates after each iteration

Expected model behavior:

Track the latest confirmed state only
Avoid referencing outdated or superseded inputs
Respond strictly based on current iteration

SECTION 3 — OBSERVED FAILURE

Failure Type:

Context Drift + Iteration Mixing

Description:

The model combined details from:

Earlier batch (non-doubled)
Hypothetical or suggested adjustments (double batch)
User corrections (no protein powder, different ingredient amounts)

Result:

Recommendations were based on blended, incorrect state
Model referenced actions the user had not taken
Guidance became misaligned with actual scenario

SECTION 4 — FAILURE EXAMPLES

Example 1 — Assumption Carryover

Model referenced a double batch scenario before it was actually performed.

Impact:

Introduced incorrect scaling assumptions
Altered ingredient recommendations unnecessarily

Example 2 — State Override

User explicitly clarified:

“I didn’t use protein powder”

Model response still:

Anchored reasoning in protein-like effects
Misdiagnosed root cause

Impact:

Reduced trust
Increased user effort to correct model

Example 3 — Iteration Blending

Model merged:

First attempt parameters
Suggested future changes
Current attempt feedback

Impact:

Loss of iteration clarity
Incorrect causal analysis

SECTION 5 — ROOT CAUSE ANALYSIS

1. Lack of State Isolation

The model did not isolate:

“previous attempt”
“current attempt”
“proposed future changes”

Instead, it treated them as a single blended state.

2. Assumption Persistence

Once a pattern was inferred (e.g., doubling batch), it was:

Reused without revalidation
Not overridden by new user input

3. Weak Correction Handling

Explicit user corrections were:

Acknowledged
But not fully enforced in subsequent reasoning

SECTION 6 — IMPACT

Functional Impact:
Incorrect recommendations
Misdiagnosed outcomes

User Impact:
Frustration
Increased correction effort
Reduced confidence in guidance

System Risk:
Accumulated drift over long conversations
Compounding inaccuracies in iterative workflows

SECTION 7 — EXPECTED BEHAVIOR

The model should:

Reset assumptions when user provides correction
Anchor responses to latest confirmed inputs only
Explicitly distinguish:
Prior attempt
Current state
Suggested next step

SECTION 8 — RECOMMENDED FIXES

1. State Locking Mechanism

Before generating response:

Reconstruct current state from latest user input
Discard conflicting prior assumptions

2. Iteration Tagging

Internally track:

Attempt N
Attempt N+1

Prevent cross-contamination of parameters

3. Correction Priority Rule

User corrections must:

Override all prior inferred assumptions
Trigger state rebuild

4. Assumption Validation Step

Before using inferred info:

Confirm it still applies
Or re-derive from latest input

SECTION 9 — QA SIGNALS IDENTIFIED

This interaction demonstrates strong capability in:

Detecting conversational inconsistency
Identifying assumption drift
Enforcing ground truth corrections
Recognizing multi-turn failure patterns

SECTION 10 — SUMMARY

This case highlights a common LLM failure mode:

Blending prior context with current state instead of maintaining strict iteration boundaries.

The issue is not knowledge — it is state management and reasoning discipline across turns.
