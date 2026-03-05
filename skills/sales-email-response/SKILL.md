---
name: sales-email-response
description: "ALWAYS use this skill when a user needs help responding to, analyzing, or triaging an inbound sales email or prospect reply. This includes ANY situation where someone received a reply from a lead or prospect and wants to draft a response, classify intent (positive/neutral/negative), or figure out next steps. Use even if the user does not say sales -- if they paste an email from someone they cold-emailed, a lead asking about pricing, someone requesting list removal, or a contact referring to a colleague, this skill applies. Also triggers on: check inbox for lead replies, fetch a prospect email, pull up a thread. Fetches emails via M365 Outlook connector or works with pasted text. Provides structured analysis with confidence scoring, evidence quoting, CRM actions, and a draft reply. Do NOT use for cold outbound from scratch, drip sequences, CRM pipeline management, support tickets, or bulk email."
---

# Sales Email Response Skill

## Overview

This skill analyzes inbound sales email threads and produces a **classified, evidence-backed draft reply**. It reads the thread, extracts intent signals (interest, rejection, referral, objection), classifies the reply, and drafts a single contextual response — all with human-in-the-loop control.

The workflow: Fetch or receive email thread → Extract signals → Classify intent → Branch by classification → Draft response (with real calendar availability for POSITIVE) → Structured output → Human reviews and sends from Outlook.

**Output:** A structured analysis block with classification, confidence score, quoted evidence, routing suggestion, missing data flags, and a ready-to-send draft reply.

---

## When to Use This Skill

**ALWAYS invoke this skill when the user's request matches ANY of these patterns:**

- User pastes or references an inbound sales email thread
- User asks to check inbox, fetch, or pull up a prospect's email reply
- User says "reply to this lead", "draft a response", "how should I respond"
- User asks to classify a sales reply (positive, neutral, negative)
- User mentions Close CRM email threads or lead responses
- User says "triage this email" or "what should I say back"
- User names a specific person/company and wants to respond to their email
- User has a prospect reply and needs help crafting the next message

**Do NOT invoke for:**

- Writing cold outbound emails from scratch (different workflow)
- Building multi-email drip sequences or cadences
- CRM data entry, pipeline management, or deal creation
- Non-sales emails (support tickets, internal comms, vendor replies)
- Bulk email operations or mail merge

---

## Hard Boundaries

These are non-negotiable constraints. Violation of ANY boundary must halt execution and explain why.

1. **No automatic sending** — All drafts require human review and manual send
2. **No CRM modification** — Do not create, update, merge, or delete any CRM records
3. **No task deletion** — Do not remove CRM tasks or activities
4. **No opportunity creation** — Do not create deals or pipeline entries
5. **No lead merging** — Do not merge or deduplicate lead records
6. **No data fabrication** — If data is missing, flag it with `[MISSING]`. Never guess company size, revenue, industry, or contact details
7. **No multi-email sequences** — This skill drafts ONE reply per invocation
8. **No attachment handling** — Do not open, download, or process email attachments

---

## Inputs

### Email Thread (Required)

**Preferred: Fetch from Outlook via Microsoft 365 connector.**

If the M365 connector tools are available (`outlook_email_search`, `read_resource`):
1. Use `outlook_email_search` to find the relevant email — search by sender name, company, subject, or keywords the user provides
2. Use `read_resource` on the email result to load the full thread content
3. If multiple matches, present a short list and ask the user which thread to analyze

If the user names a specific person or company ("the email from Sarah at BrightPath"), search for that directly. If they say something general ("check my inbox for new lead replies"), search for recent unread emails and present candidates.

**Fallback: Pasted email text.**

If M365 tools are not available, or the user pastes an email directly, use the pasted text. If the input is unclear or incomplete, ask the user to paste the full thread or provide a sender name to search for.

### Lead Metadata (Optional)

- Sender name, role, company (if visible in CRM or email signature)
- Deal stage, lead source, or prior interaction notes
- Any context the user provides verbally ("this is a warm lead from the webinar")

---

## Workflow

### Phase 1: Email Retrieval & Input Validation

**If M365 connector is available and user hasn't pasted an email:**

1. Determine what to search for from the user's request (sender name, company, subject keywords)
2. Call `outlook_email_search` with relevant search terms
3. If results found, use `read_resource` to load the full thread
4. If multiple results, present a numbered list with sender, subject, and date — ask user to pick one
5. If no results, ask the user to refine the search or paste the email manually

**Then validate:**

1. The thread contains at least one inbound message (not just outbound)
2. The content is a sales-related email (not support, internal, or vendor)
3. There is sufficient context to analyze

If any check fails, ask the user for clarification. Do NOT proceed on assumptions.

---

### Phase 2: Signal Extraction

Read the **most recent inbound message** from the thread. Also scan prior messages for context (who initiated, what was offered, how many touches).

Extract signals across five categories. For each signal found, **quote the exact language** from the email as evidence.

#### Reference File Loading

Before extraction, read the signal rubric:

- `references/signal-rubric.md` — Classification criteria, signal categories, edge cases, and examples

#### Signal Categories

**Interest Indicators:**
- Asking about pricing, features, timelines, or next steps
- Requesting a demo, meeting, or call
- Forwarding to a colleague or decision-maker with endorsement
- Mentioning a pain point that aligns with the product
- Asking "how does this work" or "can you do X"

**Rejection Language:**
- "Not interested", "please remove me", "wrong person"
- "We already have a solution", "not a priority right now"
- "Do not contact", unsubscribe requests
- "We went with [competitor]", "no budget"

**Referral / Deflection:**
- "You should talk to [name]", "forwarding to..."
- "This isn't my area, but [person] handles this"
- CC'ing someone new without commentary

**Meeting Request:**
- Explicit time suggestions, calendar links, availability sharing
- "Let's set up a call", "when are you free"
- Proposing a specific date or time window

**Objections / Constraints:**
- Budget concerns, timing issues, internal approval needs
- "We're locked into a contract until [date]"
- "Maybe next quarter", "circle back in [timeframe]"
- Technical concerns, integration questions, security requirements

---

### Phase 3: Classification with Confidence Scoring

Classify the reply into exactly one category. Assign a confidence score (0–100) based on signal strength and clarity.

#### Classification Rubric

**NEGATIVE** — Confidence must exceed 80% to assign:

| Score Range | Criteria |
|-------------|----------|
| 90–100 | Explicit rejection + removal request. Zero ambiguity. |
| 80–89 | Clear rejection language, no interest signals present. |
| Below 80 | Do NOT classify as NEGATIVE — fall through to NEUTRAL. |

Examples: "Not interested", "Please remove me from your list", "We went with another vendor", "Do not contact us again"

**NEUTRAL** — Default when signals are mixed or ambiguous:

| Score Range | Criteria |
|-------------|----------|
| 70–100 | Clear referral, explicit timing constraint, or specific objection. |
| 50–69 | Mixed signals, polite deflection, or unclear intent. |
| Below 50 | Very ambiguous — flag low confidence in output. |

Examples: "Can you reach out next quarter?", "Forwarding to our CTO", "We're evaluating options", "Interesting but timing isn't right", out-of-office auto-replies

**POSITIVE** — Confidence must exceed 80% to assign:

| Score Range | Criteria |
|-------------|----------|
| 90–100 | Explicit meeting request or buying signal with specifics. |
| 80–89 | Clear interest + asks a specific question about product/pricing/timeline. |
| Below 80 | Do NOT classify as POSITIVE — fall through to NEUTRAL. |

Examples: "I'd love to set up a call", "Can you send pricing?", "This looks like exactly what we need — let's discuss", "Are you free Thursday?"

#### Classification Guardrails

- **When in doubt, classify as NEUTRAL** — never inflate
- A single interest signal in an otherwise cold reply → NEUTRAL
- Politeness alone ("Thanks for reaching out") → NOT positive
- Referral without endorsement ("Try [person]") → NEUTRAL
- Auto-replies and out-of-office → NEUTRAL
- "Interesting" without follow-up question → NEUTRAL
- Asking to be contacted later is an objection → NEUTRAL, not NEGATIVE

---

### Phase 4: Response Drafting

Branch based on classification. Read the response templates before drafting:

- `references/response-templates.md` — Draft patterns per classification, tone guide, structural rules

#### NEGATIVE Path

1. Quote the specific rejection evidence from the email
2. Draft a graceful closure (3–4 sentences max):
   - Thank them for their time (1 sentence)
   - Acknowledge their decision without arguing
   - Leave the door open with a single low-pressure line
   - Sign off warmly
3. Suggest: "Consider marking this lead as Lost/Negative in Close CRM"
4. Do NOT try to overcome the objection or re-pitch
5. Do NOT suggest deleting the lead or any CRM records

#### NEUTRAL Path

Identify the sub-type first, then draft accordingly:

**Referral:**
- Draft a warm intro request to the referred person
- Reference who referred you and the original context
- Keep it to 3–4 sentences

**Objection (budget, timing, contract):**
- Acknowledge the constraint without dismissing it
- Provide ONE relevant proof point or brief value statement
- Suggest a low-commitment next step (e.g., "happy to send a quick overview you can review when timing is better")
- Do NOT push for a meeting

**Timing issue ("reach out later"):**
- Acknowledge the timeline
- Suggest: "Set a follow-up reminder in Close CRM for [date]"
- Draft a brief value-add message to send when re-engaging

**Information request:**
- Answer the question directly and concisely
- Include ONE follow-up question to advance the conversation

**Auto-reply / Out-of-office:**
- Note the expected return date if provided
- Suggest: "Wait until [return date + 1 day] and re-send"
- Do NOT draft a reply

#### POSITIVE Path

1. Extract visible company context: company name, sender role, company size/industry (if in signature or thread)
2. Check for routing: is this the decision-maker, or should someone else join?
3. Flag any missing critical data with `[MISSING]`:
   - Company name → `[MISSING: company name]`
   - Sender role → `[MISSING: sender role]`
   - Use case or pain point → `[MISSING: specific need]`
4. Draft a meeting response (under 120 words):
   - Brief thank-you (1 sentence — not gushing)
   - Acknowledge their specific interest or question
   - Suggest the next step (call, demo, or meeting)
   - **Time slots — use real availability when possible:**
     If `find_meeting_availability` is available, call it to get 3 open slots
     that match any preferences the prospect mentioned (e.g., "Tuesday or Thursday afternoon").
     Format each as:
     ```
     - [DAY], [DATE] at [TIME] [TZ]
     ```
     If `find_meeting_availability` is NOT available, use `[PLACEHOLDER — check your calendar]`
   - Booking link: `[INSERT_BOOKING_LINK]`
   - Sign off

---

### Phase 5: Structured Output

Return the result in exactly this format. Do not deviate from this structure:

```
────────────────────────────────────────────────
SALES EMAIL ANALYSIS
────────────────────────────────────────────────

Reply Classification:  [NEGATIVE | NEUTRAL | POSITIVE]
Confidence:            [X]%
Sender:                [Name, Role — Company] or [Name — unknown role/company]

Key Evidence:
  - "[exact quote from email]" → [signal type]
  - "[exact quote from email]" → [signal type]

Suggested Routing:     [Stay with current owner | Route to [name/role] | N/A]
Missing Data:          [list fields, or "None"]
Suggested CRM Action:  [e.g., "Mark as Lost" | "Set follow-up for Q3" | "None"]

────────────────────────────────────────────────
DRAFT RESPONSE
────────────────────────────────────────────────

Subject: [Re: existing thread — do not change]

[draft email body]

────────────────────────────────────────────────
ACTION REQUIRED: Review draft above, edit if needed, then manually send.
────────────────────────────────────────────────
```

---

## Response Writing Guidelines

All drafts must follow these rules regardless of classification:

- **Write like a peer, not a vendor.** No "I hope this email finds you well." No "synergy" or "leverage." No "just circling back."
- **Every sentence must earn its place.** If removing it doesn't weaken the email, cut it.
- **Match the sender's energy.** 2-sentence reply → 3-sentence response. Not 5 paragraphs.
- **One CTA per email.** Don't ask them to book a call AND check out a case study AND reply with availability.
- **Mirror their language.** Use the terminology and tone from their reply.
- **No subject line changes.** Always reply in the existing thread.
- **No signature block.** The user's email client handles signatures.
- **No "sent from AI" disclaimers.** The draft should read as if the user wrote it.

---

## Edge Cases

| Scenario | Handling |
|----------|----------|
| Thread has no inbound reply (only outbound) | Ask user: "I only see outbound messages. Is there an inbound reply to analyze?" |
| Multiple inbound replies since last outbound | Analyze the MOST RECENT inbound only. Note older unreplied messages in output. |
| Reply is in a foreign language | Classify based on content. Draft response in the SAME language as the inbound reply. |
| Reply is clearly spam or automated marketing | Classify as NEGATIVE. Suggest ignoring — no draft needed. |
| Reply contains sensitive data (passwords, financials) | Flag: "This reply contains potentially sensitive information. Review before forwarding." |
| Thread is extremely long (10+ messages) | Focus on the last 3 exchanges for context. Summarize older history in 1 sentence. |

---

## Microsoft 365 Connector Tools

This skill uses the following M365 connector tools when available in the environment:

| Tool | Used For |
|------|----------|
| `outlook_email_search` | Find prospect emails by sender, subject, or keywords |
| `read_resource` | Load full email thread content from search results |
| `find_meeting_availability` | Get real calendar slots for POSITIVE response drafts |

If these tools are not available (e.g., running outside Cowork, or connector not configured), the skill falls back to pasted email text and placeholder time slots. All other functionality works identically.

---

## File Paths (Relative to Skill Directory)

```
sales-email-response/
├── SKILL.md                          # This file
└── references/
    ├── signal-rubric.md              # Signal categories, edge cases, examples
    └── response-templates.md         # Draft patterns per classification
```
