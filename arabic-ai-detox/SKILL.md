---
name: "arabic-ai-detox"
description: "Detect and remove AI-generated tells from Arabic text (calques, filler vocabulary, decorative tashkeel, mixed punctuation, MSA register drift) while preserving the writer's voice. Use for any Arabic text the user wants humanized or audited."
---

# Arabic AI Detox

Remove machine signals from Arabic text so it reads as written by a native speaker for its context. Do not sanitize the writer's own voice.

## 0. Intake (decide before touching text)

Determine, from the request or the text itself. Ask only if genuinely unclear:

1. **Mode**
   - `audit`: flag tells and score; do not rewrite.
   - `rewrite`: return cleaned text plus a short change log. (Default.)
2. **Register**: MSA formal | MSA light (web/marketing) | Levantine | Egyptian | Gulf | bilingual Arabic-English.
   - Social posts, chat, and DMs default to light MSA or the user's dialect, never heavy MSA.
   - Never mix dialects unless the source does on purpose.
3. **Channel**: product UI, marketing site, LinkedIn/social, formal document, chat. The channel sets how strict orthography should be (see §1).
4. **Locks**: brand names, product terms, numbers, legal wording, quotes. Never alter these.

## 1. Orthography & punctuation

| Check | Action |
|---|---|
| Partial/decorative tashkeel (random harakat or shadda on obvious words like مُهمّ، تماماً) | Remove. Keep diacritics only where they disambiguate (عَلَّمَ vs عَلِمَ), or keep full vocalization if the content type needs it (Quran, poetry, children's material). |
| Tanween on common adverbs (أيضاً، حقاً) | Match house convention; in casual registers أيضا without tanween is fine. |
| Em dash `—` | Replace with ، or a full stop, or restructure the sentence. |
| Latin punctuation `,` `?` `;` | Convert to ، ؟ ؛ |
| Quotes `"..."` | Use «...» in formal/editorial copy; keep "" only if that is the house style. |
| Mixed digits (3 / ٣) | Pick one system per document. Western digits are the default for product UI and fintech. |
| Spotless hamza/ة/ى in casual channels | In chat or social-casual registers, do not "correct" the writer's natural spelling. Never introduce errors into formal text. |

## 2. English calques (highest-weight signal)

| AI pattern | Replace with |
|---|---|
| يلعب دوراً محورياً/مهماً في | له أثر كبير في / أساسي في / يؤثر في |
| ليس فقط… بل أيضاً | لا… فحسب، بل… |
| قم بـ + masdar (قم بتحميل) | direct imperative (حمّل) |
| يتم + masdar (يتم إرسال) | past/passive verb (أُرسل) or active voice with a subject |
| من خلال (repeated) | بـ / عبر / بواسطة, or drop it |
| في نهاية اليوم | في المحصلة / في النهاية |
| عالم الـ… (عالم التصميم) | drop it; name the field directly |
| على حد سواء (overused) | معاً / كلاهما, or drop it |
| القيام بعملية + masdar | a single verb |

## 3. Filler vocabulary & formula phrases

Flag, then rewrite the underlying idea literally. Do not swap in a synonym of the same metaphor.

- **Vocabulary**: محوري، جوهري، تعزيز، رحلة، آفاق، مشهد، نسيج، حجر الزاوية، سلس، الغوص في أعماق، بوابة نحو، ثورة في، مفتاح النجاح، لا يُستهان به
- **Openers**: في عالمنا المتسارع، في ظل التطورات المتلاحقة، في عصرنا الحالي، لا شك أن، إليك…
- **Connectors**: تجدر الإشارة إلى، من الجدير بالذكر، علاوة على ذلك (stacked)، بالإضافة إلى ذلك (stacked)
- **Closers**: في الختام، ختاماً، وبهذا نكون قد…

One occurrence in a long formal text can stay. Repetition or stacking is the signal.

## 4. Structure & rhythm

- **Triads**: break automatic groups of three. Keep two items, or the one that matters.
- **Summary-closer paragraphs**: delete a final sentence that restates its own paragraph.
- **Symmetry**: remove reflexive caveats and balancing pros/cons the writer didn't intend.
- **Hedging**: replace stacked قد/ربما/من المحتمل with a direct claim, or one honest qualifier.
- **Formatting**: strip emoji bullets and bold headers from text meant to read as prose or speech.
- **Sentence length**: vary it. AI Arabic runs uniform medium sentences joined by و.
- **Thought order**: reorder into Arabic information flow (topic → claim → detail), not English SVO with clauses tacked on.

## 5. Grammar

- Prefer verbs over nominalized masdar chains.
- Check non-human plural agreement (feminine singular).
- Check العدد والمعدود for 3–10 and 11–99.
- Check dual forms in pronouns and verbs.

## 6. Scoring (audit mode, and pre/post in rewrite mode)

| Category | Weight |
|---|---|
| Calques (§2) | 3 per instance |
| Formula phrases/vocabulary (§3) | 2 per instance |
| Structure tells (§4) | 2 per pattern |
| Orthography/punctuation (§1) | 1 per pattern (not per instance) |
| Grammar (§5) | 1 per instance |

Normalize per 100 words → **Low < 3**, **Medium 3–7**, **High > 7** likelihood of reading as AI.

State plainly that this measures *perceived* machine style. It is not proof of authorship. Translated and corporate-edited copy triggers the same signals.

## 7. Output

**Audit mode**
1. Score + level
2. Table: | المقطع | النمط | الفئة | البديل المقترح |
3. Top three fixes by impact

**Rewrite mode**
1. The cleaned text
2. Change log, 5–10 lines max, grouped by category
3. Before → after score

## Guardrails

- **Preserve voice.** Keep the writer's own idioms, dialect choices, and intentional English terms. Edit only the tells; never rewrite from scratch.
- Keep meaning and locked terms exact.
- Don't over-correct into a new monotone: an obvious "de-AI'd" style is its own tell.
- If a flagged phrase is genuinely the best wording in context, keep it and say why.
- For Egyptian spoken copy or house-style bans, defer dialect specifics to `arabic-style-curator` if it is installed.