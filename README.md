# Decoder Ring
### A legalese translator skill

A Claude Skill that rewrites legal, regulatory, or contractual text in plain language for a specified audience and built around one core design goal: a simplification that quietly drops or softens a real obligation is worse than no translation at all.

## Why this exists

Most "explain this in plain English" prompts produce something readable but unverifiable — there's no way to know whether a condition, deadline, or exception silently disappeared in the rewrite. This skill is built to make that failure mode hard to hit, through a deliberate two-stage verification process rather than a single pass from dense text to plain text.

## How it works

### 1. Identify source and audience
Before translating, the skill confirms what kind of text it's working with (contract clause, statute, internal policy, etc.) and who the output is for. Different text types need different fidelity standards: a contractual deadline needs near-perfect precision; a policy summary has more room to compress. If the audience isn't specified, the skill asks rather than guessing.

### 2. Extract a skeleton before rewriting (silent step)
Before producing any plain-language text, the skill privately lists out everything load-bearing in the source: parties, defined terms, obligations, rights, conditions/triggers, exceptions, consequences, deadlines, and exact numbers. This skeleton becomes the ground truth that the translation gets checked against later, the same "extract first, verify second" pattern used in the daily-planner skill's completion detection, applied here to textual fidelity instead of task status.

**For statutes, regulations, or text not supplied directly by the user**, the skill pulls from a single authoritative source (official code sites, Cornell LII, eCFR) rather than assembling a provision from scattered search snippets; search results often mix different effective dates without making that obvious, so cross-checking sources and noting the "as of" date matters here.

### 3. Translate to the target audience
A preset table maps six audience types (non-lawyer colleague, customer FAQ, executive summary, engineering team, general public, negotiation counterparty) to register, structure, and what's safe to compress versus what must stay.

A few rules apply no matter the audience:
- Obligations, deadlines, and conditions from the skeleton are never dropped or softened — if detail gets cut for brevity, it's cut with a visible pointer back to the source, not silently.
- Conditionals get rewritten explicitly ("unless X" → "this applies, except when X happens") rather than collapsed into an unconditional statement.
- Citations get pulled out of the translated sentence itself and into a footnote-style reference list, so the operative sentence reads cleanly without losing traceability.
- Added context that isn't a direct restatement of the source — e.g., "here's who can actually enforce this" — is labeled as a separate note, not blended into the translation with the same apparent authority. That kind of context is its own substantive claim and deserves its own scrutiny.

A dedicated set of **plain-language formatting rules** (separate from the fidelity rules) governs the mechanics of readability: one idea per sentence, action-first phrasing, active voice with a named actor, verbs instead of nominalizations, numbered lists for sequences vs. bullets for parallel items, and no legal connector words ("notwithstanding," "herein," "shall") surviving into the rewrite. These rules don't affect legal meaning — getting them wrong just defeats the purpose of calling something "plain language."

### 4. Two separate verification passes
This is the heart of the skill, and it runs as two distinct checks rather than one:

- **Fidelity check** — re-derive the obligations, rights, conditions, and deadlines from the *translated text alone*, as if the original had never been seen, and diff that list against the Step 2 skeleton. This catches meaning lost in translation: a condition that became unconditional, a vague deadline where the original was exact, a number that drifted.
- **Accuracy spot-check** — a different, narrower check: confirm every citation, number, defined term, and date in the draft actually matches the source text, rather than a remembered or assumed version. This catches transcription slips, not translation slips — including a specific failure mode worth calling out: if the source text reflects an outdated version of a law, the skill corrects this in the body of the translation itself rather than relegating the correction to a closing note, since a reader who stops at the main text would otherwise walk away with the wrong standard.

Both checks happen silently before the output is shown — the reader sees a clean result, not the verification work behind it.

### 5. Present the output
Formatted per the audience preset, with citations as footnote-style references and any flagged ambiguity surfaced clearly rather than buried.

**Output format is audience-aware too**: for non-lawyer-facing audiences (colleague, FAQ, general public) where the translation covers more than a few obligations or spans multiple sections, the skill defaults to producing a markdown file rather than inline chat text — these are reference documents people return to and forward, not scrollback. Short, single-clause, or explicitly "quick" requests stay inline.

### 6. Disclaimer
Any output going to a non-lawyer audience gets a one-line disclaimer: this is a plain-language summary, not legal advice, and the original document controls in case of conflict.

## What's in this repo

```
SKILL.md   → the full skill definition (Anthropic Skills format)
```

## A note on scope

This skill produces translations, not advice — it's deliberately built to never recommend whether to accept, sign, or act on a given term. That determination stays with a human, and the disclaimer step exists specifically to keep any non-lawyer reader from mistaking a plain-language rewrite for legal guidance.
