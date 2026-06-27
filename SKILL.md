---
name: decoder-ring
description: >
  Use this skill whenever the user wants legal, regulatory, or contractual text rewritten
  in plain language for a specific audience. Trigger phrases include: "translate this clause",
  "explain this in plain English", "simplify this for a non-lawyer", "what does this actually
  say", "rewrite this for [audience]", "summarize this contract section", "make this readable",
  or any request to take dense legal/regulatory text and make it understandable — even if
  phrased casually (e.g., "what is this paragraph even trying to say"). Always use this skill
  when the input is legal or regulatory text and the goal is a clearer rewrite, not legal advice.
---

# Decoder Ring
### (a legalese translator skill)

Rewrites legal, regulatory, or contractual text in plain language for a specific audience — while preserving every obligation, deadline, condition, and right in the source, and flagging anything ambiguous rather than silently resolving it. The core design goal is fidelity: a simplified version that quietly drops or softens a real obligation is worse than no translation at all.

---

## Step 1: Identify the source and the audience

Before translating, confirm two things. If either is missing or unclear, ask rather than guess:

1. **What kind of text is this?** (contract clause, statute/regulation, court order, internal policy, terms of service, a regulator's letter, etc.) — this affects how strict the fidelity requirements are. Contractual obligations and regulatory deadlines require near-perfect fidelity; a policy summary has more room for compression.
2. **Who is this for, and what will they do with it?** This determines register, structure, and what can safely be cut. See the audience presets in Step 3.

If the user hasn't specified an audience, ask: "Who's this for — a colleague who isn't a lawyer, a customer-facing FAQ, an executive summary, an engineering team, or something else?"

**For statutes, regulations, or other text not supplied directly by the user:** pull the current text from a single authoritative source (e.g., the official Code site, Cornell LII, eCFR) via direct fetch, rather than assembling the provision from multiple search-result snippets. Search snippets frequently mix different effective dates (e.g., an "as of 1999" codification alongside the current one) without making this obvious. If more than one source must be consulted, cross-check that they agree before treating any version as current, and note the "as of" date of the source actually used.

---

## Step 2: Extract the legal skeleton before rewriting

Do this step silently, before producing any translated text. For the source text, identify and list:

- **Parties** — who is bound, who benefits
- **Defined terms** — anything capitalized or specially defined, and what it means
- **Obligations** — who must do what, and by when (every "shall," "must," "will," and "agrees to")
- **Rights** — what each party is entitled to do or receive
- **Conditions and triggers** — "if," "unless," "provided that," "except as," "subject to" — these are the most common place meaning gets lost in a rewrite
- **Exceptions and carve-outs**
- **Consequences** — what happens on breach, default, late performance, or failure to act
- **Deadlines and durations** — exact dates, time periods, notice windows
- **Numbers** — dollar amounts, percentages, thresholds — copy these exactly, do not round or approximate without saying so

This skeleton is the source of truth you'll check your translation against in Step 4. Keep it even if you don't show it to the user.

---

## Step 3: Translate to the target audience

Use the skeleton from Step 2 as the content; change only the register, structure, and level of detail based on audience.

| Audience | Register | Structure | What to keep | What to cut |
|---|---|---|---|---|
| **Non-lawyer colleague** | Conversational, second person where natural | Short paragraphs or bullets | Every obligation/deadline that affects them | Citations to other sections, defined-term boilerplate they don't need to know |
| **Customer-facing FAQ** | Friendly, plain, short sentences | Q&A format | What it means for the customer, in their terms | Internal-only obligations, cross-references |
| **Executive/board summary** | Direct, outcome-first | Lead with the bottom line, then supporting detail | Risk, cost, timeline, decision needed | Procedural detail, defined-term mechanics |
| **Engineering/product team** | Plain, technical-adjacent | Structured as requirements ("must," "must not," "by [date]") | Every functional requirement and constraint | Legal reasoning, citations |
| **General public / consumer notice** | Simple, short sentences, minimal jargon | Short paragraphs, active voice | The single most important takeaway, clearly first | Nuance that doesn't change the consumer's action |
| **Counterparty (negotiation context)** | Neutral, precise | Mirrors original clause structure | Exact scope of what's being proposed/changed | Internal commentary or framing that reveals strategy |

### Rules that apply regardless of audience

- **Never drop or soften an obligation, deadline, or condition** identified in the skeleton. If the audience doesn't need the detail, summarize it accurately rather than omitting it silently — say "there are additional conditions in the original; see [reference]" rather than just leaving them out.
- **Move citations out of the sentence, not just out of the document.** For non-lawyer-facing audiences, cross-references to other sections (e.g. "as defined in section 1681g(f)(2)(A)") should not sit inline in the translated sentence — pull them into a footnote-style reference list (per Step 5) so the operative sentence reads cleanly and the citation is still traceable.
- **Convert conditionals explicitly.** "Unless X" becomes "this applies, except when X happens." "Provided that" becomes "as long as." Don't collapse a conditional into an unconditional statement.
- **Watch for double negatives and exceptions-to-exceptions** — these are the single most common source of meaning loss in simplification. Read the sentence twice before rewriting it.
- **Preserve exact numbers, dates, and thresholds.** Never round a percentage or approximate a deadline.
- **Flag ambiguity instead of resolving it unilaterally.** If a term in the source is genuinely unclear, say so explicitly: "This term is ambiguous in the original — I've interpreted it as [X], but you should confirm with counsel before relying on this."
- **Keep defined terms consistent.** If the source defines a term, use the same plain-language equivalent for it every time it appears, rather than varying the phrasing.
- **Separate translation from added context.** If you add practical or legal context that isn't a restatement of the source text — e.g., who can enforce a provision, who can sue over it, what typically happens in practice — present it as a clearly labeled, separate note rather than blending it into the translation with the same authority. This kind of context is itself a substantive legal claim and is often more complicated than a single sentence can hold accurately; treat it with the same care as the translation, not as a casual aside.

### Plain-language formatting rules (mechanics, not fidelity)

These govern *how* the rewrite reads. They're separate from the fidelity rules above — getting these wrong doesn't lose legal meaning, but it defeats the entire point of calling something "plain language."

- **One idea per sentence.** Cap most sentences around 20 words. Where the source chains several conditions or actions with semicolons, split them into separate bullets rather than one long sentence with sub-clauses.
- **Lead with the action.** Open each obligation with a bolded short label or verb phrase (e.g., "**Don't report what you know is wrong.**") so a reader skimming the page can grab the obligation without reading the full sentence.
- **Verbs over nominalizations.** Replace noun-ified actions with verbs: "the furnishing of information" → "when you furnish information"; "submission of a notice of dispute" → "when a consumer submits a dispute notice."
- **Active voice, named actor.** Use active voice and name who's doing what, except in the rare case where the source itself leaves the actor genuinely unspecified — don't paper over that ambiguity by inventing an actor.
- **Numbered lists for sequences, bullets for parallel items.** If steps happen in order or are conditional on each other, number them. If obligations are independent of each other, use bullets instead.
- **No legal connector words in the rewrite.** Translate "notwithstanding," "provided that," "herein," "the foregoing," "shall" into plain equivalents — "despite," "as long as," "in this document," "what's above," "must."
- **Define every acronym or term of art on first use**, even ones common in the source domain (e.g., CRA, furnisher) — spell it out once, then use the short form consistently.
- **Cap paragraph length.** No more than 2–3 sentences per paragraph. If a paragraph is doing more work than that, it's a sign the content should be a list instead — break it out.
- **Cap individual bullet/list-item length too.** A bullet that runs past ~30–40 words or covers more than one idea should split into a sub-list or into separate bullets. Density inside a bullet defeats the purpose as much as density in a paragraph does.
- **Use white space deliberately.** One obligation or idea per visual block (paragraph, bullet, or short block under a sub-header). If a section is producing a wall of text, add a sub-header or break it into smaller named pieces rather than letting it run long.

---

## Step 4: Fidelity check (self-verification)

After producing the translation, do a second pass: re-derive the obligations, rights, conditions, and deadlines from **your translated text alone** — pretend you've never seen the original — and compare that list against the skeleton from Step 2.

| Comparison result | Action |
|---|---|
| Everything in the skeleton appears in the translation, scope and conditions intact | Pass — proceed to present |
| Something is missing entirely | Add it back before presenting — do not present an incomplete translation |
| Something is present but the condition/scope changed (e.g., a conditional obligation now reads as unconditional, or a deadline is vague where the original was exact) | Fix the wording — this is the most common and most serious failure mode |
| A number, date, or threshold doesn't match exactly | Fix immediately — this is never acceptable to approximate silently |
| A legal standard or phrase in the source is outdated relative to current law (e.g., text pulled from an older codification) | Fix it in the body of the translation itself before presenting. Do not present the outdated formulation and relegate the correction to a closing note — by the time the reader reaches a note, they've already absorbed the wrong standard. |

Do not skip this step for contractual or regulatory text, even under time pressure — it's the difference between a useful tool and a liability.

### Accuracy spot-check (silent, before presenting)

This is a different check from fidelity above. Fidelity verifies the *meaning* survived the rewrite. This verifies the *literal facts in your draft* are correct — it catches transcription slips, not just translation slips.

Before presenting, go back through your draft and check, against the actual source text (not your memory of it):

- Every **citation** (section, subsection, paragraph letter) — confirm it points to the provision it claims to.
- Every **number** — dollar amount, percentage, day count, deadline — confirm it's the exact figure in the source, not a rounded or recalled approximation.
- Every **defined term** — confirm your plain-language equivalent matches what the source actually defines, not a nearby or similar-sounding concept.
- Any **date or effective-date claim** — confirm against the source, especially for statutes that have been amended; a duty you describe as current should actually be the current version, not a superseded one.

If you're not fully confident about any of these without rechecking, recheck it — re-fetch or re-search the source rather than presenting it as settled. Do this silently; don't narrate the check to the reader, just make sure the final output passes it before you show it.

---

## Step 5: Present the output

- Lead with the audience-appropriate format from Step 3.
- For non-lawyer-facing audiences, consider adding a short "what this means for you" line at the top if the text implies an action or deadline for the reader.
- If useful and the user hasn't said otherwise, offer the original clause numbering or short excerpts as footnote-style references, so a reader can trace any plain-language sentence back to its source if they need to verify something.
- If you flagged any ambiguity in Step 3, surface those flags clearly rather than burying them — e.g., a short "Notes" section at the end.
- **Default to a markdown artifact instead of inline chat text when:** the audience is a non-lawyer colleague, customer-facing FAQ, or general public/consumer notice, AND the translation covers more than about 3 obligations or spans multiple sections. These are reference documents people return to, navigate, and forward to others — they belong as a file, not scrollback. For a single clause, an executive summary, or anything the user frames as quick or short, keep it inline unless they ask for a file. When producing the artifact: keep the original section numbering as headers so it's navigable, put a short "what this means for you" line at the top, and move citations into a "Source references" section at the very end rather than inline.

---

## Step 6: Disclaimer

For any output going to a non-lawyer audience (customer-facing, general public, or internal non-legal colleague), include a brief disclaimer: this is a plain-language summary, not legal advice, and the original document controls in the event of any conflict or discrepancy. Keep this short — one sentence — so it doesn't undercut the goal of making the text approachable.

---

## Notes & edge cases

- **Multi-clause or multi-section documents**: process section by section, keeping the original numbering as a reference key, rather than producing one undifferentiated summary. This makes the fidelity check in Step 4 tractable and lets the reader navigate back to source.
- **If the source text is itself ambiguous or internally inconsistent**: don't paper over this in the translation. Note the inconsistency rather than picking one reading and presenting it as settled.
- **If asked to translate into a specific reading level** (e.g., "explain like I'm not a lawyer" vs. "explain like I've never read a contract before"): treat this as a register/vocabulary adjustment only — the fidelity rules in Steps 3–4 still apply at every reading level. Simpler vocabulary is not an excuse to drop conditions.
- **Negotiation context is sensitive**: when translating a counterparty's proposed redline for an internal audience, don't add strategic commentary unless asked — keep the translation neutral and accurate, and offer analysis as a clearly separate, optional section.
- **This skill produces a translation, not advice.** It should never recommend whether to accept, sign, or act on a given term — that determination stays with the human, and the disclaimer in Step 6 should make this explicit for any audience that might otherwise read the output as advice.
