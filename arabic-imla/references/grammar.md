# Grammar Layer — Rule Reference

Runs when `scope: full` (the default). Class tags as in `rules.md`: **[E]** Error · **[A]** Ambiguous-prone.

## Scoping principle

Arabic text is usually unvoweled, so short-vowel case endings (ـُ ـَ ـِ) are invisible. This layer covers **only grammar errors that change the written letters**: a letter added, dropped, or swapped (ون/ين، نون الأفعال الخمسة، ألف تنوين النصب، أبو/أبا/أبي), plus agreement and required particles. Invisible i'rab is out of scope.

**Minimum edit:** change only the letters the rule requires. Never restructure the sentence to fix grammar.

**When the parse is uncertain** (two valid analyses), classify as Ambiguous.

**Off for:** dialect registers (dialect grammar is not MSA grammar) and heritage input (classical usage differs from modern norms; see SKILL.md §0.5).

---

## G1 — Case endings visible in letters [E]

| Pattern | Errors → correct |
|---|---|
| الأسماء الخمسة (أبو/أبا/أبي، ذو/ذا/ذي، أخو/أخا/أخي) | كان ذو خبرة → ذا خبرة · إن أبو محمد → إن أبا محمد · مع ذو الخبرة → ذوي الخبرة / ذي الخبرة |
| Sound masculine plural ون/ين | إن المهندسون → إن المهندسين · لدى الموظفون → الموظفين |
| Dual ان/ين | حضر اثنين → اثنان · إن الطرفان → الطرفين |
| Dropped نون in construct (إضافة) | مهندسين الشركة → مهندسي الشركة · موظفين البنك → موظفي البنك · طرفين العقد → طرفي العقد |
| Missing alif of indefinite accusative (خبر كان، حال، مفعول به، تمييز) | كان المشروع ناجح → ناجحًا · أصبح الأمر واضح → واضحًا · ابدأ صغير → صغيرًا |

**Exceptions:** ممنوع من الصرف takes no alif (كان أفضل، وجدت مساجد). Words ending in ة/ء/ى follow `rules.md` 2.4.

**Channel note:** missing accusative alif is very common in light-MSA social posts. In `flag` mode on social channels, report it as one pattern row.

## G2 — الأفعال الخمسة [E]

- **Drop the نون after نصب and جزم:** لم يذهبون → لم يذهبوا · لن تفعلون → لن تفعلوا · يجب أن يكونون → أن يكونوا · لا تنسون → لا تنسوا
- **Keep the نون in the indicative (MSA only):** هم يذهبوا → هم يذهبون. In dialect registers يذهبوا is normal: leave it.
- Weak-letter deletion in jussive/imperative is in `rules.md` 2.6.

## G3 — العدد والمعدود [E/A]

| Number | Rule | Correct |
|---|---|---|
| 1–2 | Number follows the noun as an adjective | كتاب واحد · سنتان |
| 3–10 | Number takes the opposite gender of the **singular** counted noun; counted noun plural genitive | ثلاثة كتب · ثلاث سنوات · سبع محاضرات |
| 11–99 | Counted noun singular accusative | خمسة عشر يومًا · عشرون موظفًا |
| 100, 1000, multiples | Counted noun singular genitive | مئة دولار · ألف طالب · ٣٠٠ دولار |
| Compound (e.g. 319) | The **last-mentioned part** governs (319 = ثلاثمئة وتسعة عشر → 19 governs) | ٣١٩ دولارًا · ١٠٥ طلاب · ٢٥ يومًا |

Common errors: ٢٠ دولار → ٢٠ دولارًا · ١٥ يوم → يومًا · ٣١٩ دولار → دولارًا · ثلاث كتب → ثلاثة كتب · خمسة سنوات → خمس سنوات

**With digits:** the number's gender is invisible, so only the counted noun is checked.

**Traps [A]:** a number after the noun used as an ordinal or label (الصفحة ٢٠، الباب ١٢) has no counted-noun rule. Prices in UI/tables (`٢٠ دولار` as a label) → flag, don't fix, unless the channel is formal prose.

## G4 — Agreement [E/A]

| Pattern | Errors → correct |
|---|---|
| Non-human plural → feminine singular (verb, adjective, pronoun, relative) | يضيع منك فرص → تضيع منك فرص · الخدمات المتاحين → المتاحة · المشاريع اللذين → التي |
| Verb before an explicit human plural subject stays singular (MSA) | حضروا الطلاب → حضر الطلاب |
| Relative pronouns match the antecedent | الأشخاص الذي → الذين · الشركات الذين → التي |
| Demonstratives match | هذا الشركة → هذه الشركة · هذه المشاريع ✓ |

**Traps [A]:** a masculine verb before a feminine or non-human subject is acceptable when separated from it (ليس فيهم هذه الأمور). Flag only if unseparated. Agreement by meaning in established usage (جاء الناس كلهم) is not an error.

## G5 — Required particles [E]

| Rule | Errors → correct |
|---|---|
| جواب أمّا takes فـ | أما الثاني مهم → فمهم · أما همة المؤمن متعلقة → فمتعلقة |
| جواب الشرط takes فـ when it is a nominal clause, imperative/prohibition, or starts with ما / لن / قد / السين / سوف | إذا أردت النجاح عليك أن… → فعليك · إن كنت مستعدًا ابدأ → فابدأ · إن لم تتحرك لن تتغير → فلن تتغير |

## G6 — Common usage errors with one accepted form [E/A]

| Wrong | Correct | Class |
|---|---|---|
| سوف لن | لن | E |
| كلما … كلما | كلما (once) | E |
| لا زال (for continuation) | ما زال / لا يزال | A: لا زال is valid as a prayer (لا زال فضلك) |

---

## Out of scope

- Invisible short-vowel i'rab (unless the text is voweled and the user asks)
- Word choice, calques, register, style → `arabic-ai-detox`
- Classical grammar in heritage input
- Dialect grammar
