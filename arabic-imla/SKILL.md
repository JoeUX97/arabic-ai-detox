---
name: "arabic-imla"
description: "Proofread Arabic for spelling (الإملاء: hamza, ة/ه, ى/ي, alif fariqa, tanween, ض/ظ, spacing) and common grammar errors that change written form (الأسماء الخمسة، ون/ين، الأفعال الخمسة، العدد والمعدود، agreement، فاء جواب أمّا والشرط). Enforces a per-project orthography house style. Use for any request to proofread, صحح الإملاء, دقق لغويًا, راجع النحو, or unify Arabic spelling across documents."
---

# Arabic Imla

Correct Arabic spelling and common written-form grammar errors, and unify spelling conventions, without touching the writer's style, vocabulary, or dialect. This skill fixes *how words are written and inflected*, not *which words are chosen*.

Rule tables: spelling in `references/rules.md`, grammar in `references/grammar.md`. Read the sections you need; do not reproduce them to the user.

## 0. Intake (decide before touching text)

Determine from the request or the text. Ask only if genuinely unclear.

1. **Mode**
   - `flag`: list issues with suggested fixes; do not change the text.
   - `fix`: return corrected text plus a change table. (Default when the user says صحح / correct.)
   - `off`: skip spelling entirely (used when another skill calls this one for a casual channel).
2. **Channel → default policy**

   | Channel | Default | Why |
   |---|---|---|
   | Product UI / fintech / app strings | `fix` + strict house style | Spelling inconsistency across screens erodes trust |
   | Formal document, report, legal | `fix` | Correctness is required |
   | Marketing site | `fix` errors, relaxed conventions | |
   | LinkedIn / social | `flag` | Suggest; the voice belongs to the writer |
   | Chat / DM | `off` unless asked | Casual spelling is part of the voice |

3. **Register**: MSA | light MSA | dialect (Levantine, Egyptian, Gulf, …). Dialect words are not spelling errors (see §2).
4. **House style**: load the project's block if the user supplies one; otherwise use the defaults in `references/house-style.md` and state that you did.
5. **Locks**: proper names, brand names, Quranic text, hadith, quoted material, code, URLs, legal citations. Never alter these.
   - Quotation locks apply to quotes *embedded* in the writer's text. When the whole input is a heritage or quoted text the user asks to proofread, only Quran and hadith stay locked; the rest gets spelling correction (modern editorial practice) while wording, archaic forms and existing tashkeel stay as they are. The grammar layer is **off** for heritage input.
6. **Scope**: `full` (spelling + grammar, default) | `spelling` (used when another skill calls this one, or on request). The grammar layer is also off in dialect registers.

## 1. Classify every finding before acting

Each candidate falls into exactly one class. The class decides the action.

| Class | Definition | `fix` mode action | `flag` mode action |
|---|---|---|---|
| **Error** | Violates a standard rule with one correct form (إستخدام، شيئ، يرجوا، إنشاء الله) | Correct | Flag |
| **Convention** | Two accepted forms exist (مسؤول/مسئول، كتابًا/كتاباً، مئة/مائة) | Unify to house style | Flag only if the document mixes forms |
| **Dialect** | Intentional dialect spelling (هاد، إزيك، شو) in a dialect register | Leave | Leave |
| **Ambiguous** | Two readings are both valid in context (علي/على، ان → أن/إن، مدرسه → مدرسة/مدرّسه) | Flag, never auto-fix | Flag |

**When in doubt, classify as Ambiguous.** A wrong "correction" is worse than a missed error.

## 2. Process

1. Mark locked spans first and skip them in every later step.
2. Detect the register. If the text is dialect, apply only rules that hold across registers (spacing, attached particles, إن شاء الله) unless the user asks for MSA conversion (out of scope: say so).
3. Run the rule groups in this priority order (details in `references/rules.md`):
   - **P1**: hamzat al-wasl / al-qat' · taa marbuta / haa · alif maqsura / yaa · fixed phrases (إن شاء الله)
   - **P2**: medial and final hamza · alif fariqa · tanween placement · لـ + الـ contraction · weak-letter deletion in jussive/imperative (لم يقل، ابنِ، لا تنسَ)
   - **P3**: ض/ظ and other dialect-driven substitutions (ذ/ز، ث/ت/س) · spacing and attached particles · ابن/بن · non-word typos
   - **G (scope full)**: visible case endings · الأفعال الخمسة · العدد والمعدود · agreement · فاء جواب أمّا والشرط · common usage errors (`references/grammar.md`)
4. Check consistency: every convention item in the house style must appear in one form only across the whole text.
5. Re-read the output once for errors you introduced. Zero is the only acceptable number.

## 3. Systematic omission ≠ random error

If a writer omits hamza on alif *everywhere* (اذا، الى، اكثر), or writes final ي as ى throughout (Egyptian convention), treat it as a **writing habit**:
- `fix` mode in formal/UI channels: correct all instances and note it once in the summary as a single pattern, not as dozens of rows.
- `flag` mode: report it as one pattern with a count.
- Casual channels: leave it.

## 4. Output

**`fix` mode**
1. The corrected text.
2. Change table, grouped by rule, one row per *pattern* when a pattern repeats:

   | الأصل | التصحيح | القاعدة | الصنف | العدد |
   |---|---|---|---|---|

3. An **Ambiguous** list the user must decide on (if any).
4. One line naming the house style applied (project block or defaults).

**`flag` mode**
1. Count by class: errors · convention mixes · ambiguous.
2. The same table, with no corrected text.
3. Top three fixes by impact (frequency × visibility; headings and UI labels outrank body text).

Keep commentary short. Do not lecture on grammar unless asked "why".

## 5. Guardrails

- **Never introduce a spelling error for any purpose**, including to make text "sound human".
- **Correctness only.** Do not change word choice, sentence structure, tone, punctuation style (beyond spacing rules), or dialect. Those belong to other skills. Grammar fixes change the minimum letters required.
- **Report spelling and grammar separately** in the change table (two groups: إملاء / نحو), so the user can accept one layer without the other.
- **Locks are absolute.** Names keep their owner's spelling (إسماعيل، علي، عبدالله if that is how the person writes it).
- **Corrupted locked text:** if a Quranic verse, hadith, or quotation looks mistranscribed (e.g. a wrong diacritic), do not edit it. Add one line asking the user to verify it against the source.
- **Convention is not error.** Never call مسئول or كتاباً "wrong"; call it "differs from house style".
- **Ambiguous is never auto-fixed.**
- **Tashkeel is out of scope** except tanween al-fath placement and the shadda/diacritic needed to resolve an Ambiguous case the user asks about.

## 6. Use by other skills

Other skills (e.g. `arabic-ai-detox`) may call this skill with an explicit mode and scope. Spelling and grammar findings are reported separately and **must not feed any AI-likelihood score**: errors are a human signal, and clean text is not evidence of machine authorship. This skill owns grammar correctness; `arabic-ai-detox` should delegate its grammar checks here when this skill is installed.
