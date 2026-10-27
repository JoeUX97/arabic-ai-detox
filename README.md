# Arabic AI Detox

A Claude skill that finds the patterns that make Arabic text read as machine-written, scores them, and fixes them without erasing the writer's voice.

Built by [Yousef Hamad](https://github.com/JoeUX97) · Meyar Agency · *Design to Solve*

---

## What it checks

| Category | Examples | Weight |
|---|---|---|
| **English calques** | «يلعب دوراً محورياً»، «قم بتحميل»، «يتم إرسال» | 3 per instance |
| **Filler vocabulary & formula phrases** | «في عالمنا المتسارع»، «تجدر الإشارة إلى»، «حجر الزاوية» | 2 per instance |
| **Structure & rhythm** | automatic triads, summary-closer paragraphs, stacked hedging | 2 per pattern |
| **Orthography & punctuation** | decorative tashkeel, em dashes, `,` `?` instead of ، ؟, mixed digits | 1 per pattern |
| **Grammar** | masdar chains, non-human plural agreement, العدد والمعدود | 1 per instance |

The score is normalized per 100 words: **Low < 3**, **Medium 3–7**, **High > 7**.

It measures *perceived* machine style, not authorship. Translated and heavily edited corporate copy trigger the same signals.

## Modes

- **Rewrite** (default): cleaned text, a short change log, and the score before and after.
- **Audit**: a table of every flagged passage with a suggested replacement, plus the top three fixes. No rewriting.

## Design decisions

- **Calques weigh the most.** Readers rarely notice a single fancy word, but they feel English sentence structure in Arabic letters right away.
- **Orthography depends on the channel.** In chat and casual social posts, spotless spelling is itself a signal, so the skill leaves a writer's natural spelling alone.
- **Shadda alone is not a tell.** Diacritics that disambiguate (عَلِمَ / عَلَّمَ) stay. Only scattered, decorative marks get removed.
- **Voice comes first.** The skill edits the tells and nothing else. It never rewrites from scratch, and it avoids producing a new "de-AI'd" monotone.

## Install

The skill lives in the [`arabic-ai-detox/`](arabic-ai-detox/) folder.

- **Claude Code:** copy the folder into `~/.claude/skills/` (personal) or `.claude/skills/` in a project.
- **Claude apps:** zip the `arabic-ai-detox` folder and upload it as a custom skill.

Then ask Claude something like:

> Audit this Arabic post for AI tells.
> نظّف هذا النص من علامات الذكاء الاصطناعي مع الحفاظ على أسلوبي.

---

## بالعربية

مهارة لـ Claude تبحث في النص العربي عن آثار الكتابة الآلية، وتعطيه درجة، وتصلح المواضع دون أن تمسح صوت الكاتب.

**ماذا تفحص؟** الترجمة الحرفية من الإنجليزية، والعبارات المحفوظة، والترقيم المستورد، والتشكيل المتقطع، والفصحى الثقيلة في غير مكانها.

**وضعان:** التدقيق يحدد كل موضع ويقترح بديله. وإعادة الكتابة تصلح هذه المواضع فقط وتترك الباقي كما هو.

الدرجة تقيس كيف يبدو النص للقارئ، لا من كتبه فعلاً.

## License

MIT. See [LICENSE](LICENSE).
