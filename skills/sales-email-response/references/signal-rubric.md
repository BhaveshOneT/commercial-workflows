# Signal Extraction Rubric

## How to Use This File

When analyzing an inbound sales email, scan for signals in each category below. For every signal found, quote the exact phrase from the email. A single email may contain signals from multiple categories — that's expected and helps with accurate classification.

---

## Category 1: Interest Indicators

**Strong signals (high weight):**
- Asking about pricing or cost: "What does this cost?", "Can you send pricing?"
- Requesting a meeting or demo: "Let's set up a call", "Can I see a demo?"
- Mentioning a specific pain point: "We're struggling with X", "Our current process for Y is broken"
- Asking implementation questions: "How long does setup take?", "Does this integrate with Z?"
- Forwarding to decision-maker with endorsement: "Looping in my VP — she'd want to see this"

**Weak signals (low weight — do NOT classify as POSITIVE on these alone):**
- Generic acknowledgment: "Thanks for reaching out", "Interesting"
- Asking a single clarifying question without enthusiasm
- Opening the email (if tracking data is mentioned)
- Replying at all (some leads reply out of politeness)

---

## Category 2: Rejection Language

**Hard rejection (classify NEGATIVE immediately):**
- "Not interested"
- "Please remove me from your list"
- "Do not contact us again"
- "Unsubscribe"
- "We went with [competitor name]"

**Soft rejection (likely NEGATIVE, but check for other signals):**
- "Not a priority right now" (could be timing — check for "reach out later")
- "We already have a solution" (could open to comparison — check tone)
- "Wrong person" without referral (if they refer someone, it's a referral signal)
- "No budget" without timeline (if they say "not until Q3", it's a timing objection)

---

## Category 3: Referral / Deflection

**Active referral (valuable — classify NEUTRAL with routing suggestion):**
- "You should talk to [Name]" + provides contact info
- "I've forwarded this to [Name] who handles this"
- "CC'ing [Name] from our [department]"
- "Let me connect you with the right person"

**Passive deflection (less valuable — still NEUTRAL):**
- "This isn't really my area"
- "I don't handle purchasing decisions"
- "Try reaching out to our [department] team" (no specific person)

---

## Category 4: Meeting Request

**Explicit (strong POSITIVE signal):**
- Proposing specific times: "How about Tuesday at 2pm?"
- Sharing calendar link
- "When are you free this week?"
- "Let's find 30 minutes"

**Implicit (moderate signal — may need confirmation):**
- "We should talk" (without proposing time)
- "Happy to chat" (without commitment)
- "Maybe we can jump on a quick call" (tentative)

---

## Category 5: Objections / Constraints

**Timing objections (NEUTRAL — set follow-up):**
- "Reach out next quarter"
- "We're in the middle of [project], bad timing"
- "Budget cycle is in [month]"
- "Check back in [timeframe]"

**Budget objections (NEUTRAL — may need nurturing):**
- "No budget right now"
- "This would need approval from [person]"
- "We'd need to see ROI first"

**Technical objections (NEUTRAL — may indicate interest):**
- "Does this work with [specific system]?"
- "We have security requirements around X"
- "Our IT team would need to evaluate"
- Note: Technical questions often signal genuine interest — the lead is thinking about implementation

**Contractual objections (NEUTRAL — set long follow-up):**
- "We're locked in with [vendor] until [date]"
- "Our contract renews in [month]"

---

## Signal Conflict Resolution

When signals conflict (e.g., interest + objection), follow these rules:

1. **Interest + Timing objection** → NEUTRAL (not POSITIVE — the timing blocks action)
2. **Interest + Budget objection** → NEUTRAL (interest is real but can't convert now)
3. **Rejection + Referral** → NEUTRAL (the referral is actionable even if this person isn't interested)
4. **Politeness + No substance** → NEUTRAL (politeness is not a signal)
5. **Meeting request + Objection** → POSITIVE (they want to talk despite concerns — that's buying behavior)
6. **Multiple strong interest signals + Zero objections** → POSITIVE (high confidence)
7. **Single weak interest signal + Nothing else** → NEUTRAL (insufficient evidence)
