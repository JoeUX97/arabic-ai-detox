# Arabic AI Detox

A Claude skill that finds the patterns that make Arabic text read as machine-written, scores them, and fixes them without erasing the writer's voice.

Ships with a companion skill, **[Arabic Imla](#companion-skill-arabic-imla)**, for spelling and grammar correctness.

Built by [Yousef Hamad](https://github.com/JoeUX97) · Meyar Agency · *Design to Solve*

---

## What it checks

| Category | Examples | Weight |
|---|---|---|
| **English calques** | «يلعب دوراً محورياً»، «قم بتحميل»، «يتم إرسال» | 3 per instance |
| **Filler vocabulary & formula phrases** | «في عالمنا المتسارع»، «تجدر الإشارة إلى»، «حجر الزاوية» | 2 per instance |
| **Structure & rhythm** | automatic triads, summary-closer paragraphs, stacked hedging | 2 per pattern |
| **Orthography & punctuation** | decorative tashkeel, em dashes, `,` `?` instead of ، ؟, mixed digits | 1 per pattern |
| **Masdar chains** | «القيام بعملية التحقق» instead of a verb | 1 per instance |

The score is normalized per 100 words: **Low < 3**, **Medium 3–7**, **High > 7**.

It measures *perceived* machine style, not authorship. Translated and heavily edited corporate copy trigger the same signals.

## Modes

- **Rewrite** (default): cleaned text, a short change log, and the score before and after.
- **Audit**: a table of every flagged passage with a suggested replacement, plus the top three fixes. No rewriting.

## Design decisions

- **Calques weigh the most.** Readers rarely notice a single fancy word, but they feel English sentence structure in Arabic letters right away.
- **Spelling is not an AI signal.** Machine text is usually spelled correctly, and errors are human. Spelling and grammar correctness live in Arabic Imla and carry zero weight in the AI score.
- **Shadda alone is not a tell.** Diacritics that disambiguate (عَلِمَ / عَلَّمَ) stay. Only scattered, decorative marks get removed.
- **Voice comes first.** The skill edits the tells and nothing else. It never rewrites from scratch, and it avoids producing a new "de-AI'd" monotone.

## Companion skill: Arabic Imla

[`arabic-imla/`](arabic-imla/) proofreads Arabic for spelling and for grammar errors that change the written letters. Unvoweled text hides short-vowel case endings, so the skill only checks what it can judge with confidence.

| Layer | Covers |
|---|---|
| **Spelling** | hamzat al-wasl / al-qat', medial and final hamza, madda, ة/ه, ى/ي, alif fariqa, tanween placement, ض/ظ, spacing and attached particles, fixed phrases (إن شاء الله، لا بدّ) |
| **Grammar** | الأسماء الخمسة, ون/ين, نون الأفعال الخمسة, weak-letter deletion (لم يقل، ابنِ), العدد والمعدود, agreement, فاء جواب أمّا والشرط |
| **House style** | one YAML block per project that settles accepted variants (مسؤول/مسئول، كتابًا/كتاباً، مئة/مائة) |

Every finding gets one class: **Error** (fixed or flagged), **Convention** (unified to house style, never called wrong), **Dialect** (left alone), or **Ambiguous** (flagged, never auto-fixed). Names, brands, Quran, and hadith are locked. The skill does not touch word choice or tone.

[`arabic-imla/evals/cases.md`](arabic-imla/evals/cases.md) holds 20 test cases. The release gate is zero introduced errors and zero edits inside locked text.

**Together:** Detox edits the AI tells first, then Imla runs as the final correctness gate, so it also catches anything a rewrite introduced.

## Install

Each skill lives in its own folder: [`arabic-ai-detox/`](arabic-ai-detox/) and [`arabic-imla/`](arabic-imla/). Install both to get the hand-off; each also works alone.

- **Claude Code:** copy the folders into `~/.claude/skills/` (personal) or `.claude/skills/` in a project.
- **Claude apps:** zip each folder and upload it as a custom skill.

Then ask Claude something like:

> Audit this Arabic post for AI tells.
> نظّف هذا النص من علامات الذكاء الاصطناعي مع الحفاظ على أسلوبي.
> دقق هذا النص إملائيًا ونحويًا.

---

## بالعربية

مهارة لـ Claude تبحث في النص العربي عن آثار الكتابة الآلية، وتعطيه درجة، وتصلح المواضع دون أن تمسح صوت الكاتب.

**ماذا تفحص؟** الترجمة الحرفية من الإنجليزية، والعبارات المحفوظة، والترقيم المستورد، والتشكيل المتقطع، والفصحى الثقيلة في غير مكانها.

**وضعان:** التدقيق يحدد كل موضع ويقترح بديله. وإعادة الكتابة تصلح هذه المواضع فقط وتترك الباقي كما هو.

الدرجة تقيس كيف يبدو النص للقارئ، لا من كتبه فعلاً.

**مهارة مرافقة: Arabic Imla.** تدقق الإملاء والأخطاء النحوية التي تغيّر شكل الكلمة المكتوبة، وتوحّد الأعراف الإملائية حسب أسلوب كل مشروع. لا تحتسب الأخطاء في درجة الذكاء الاصطناعي، ولا تلمس الأسماء ولا النصوص القرآنية، ولا تغيّر اختيار الكلمات.

## License

MIT. See [LICENSE](LICENSE).
