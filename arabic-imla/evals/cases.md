# Eval Cases — arabic-imla

Run each case, compare against **Expected**. Release gate: **0 introduced errors** and **0 changes inside locked spans**. Any failure blocks release.

---

## A. Clean formal text (must stay untouched)

### A1 — Machine-style but correctly spelled
- **Channel:** formal · **Mode:** fix
- **Input:** تهدف المنصة إلى تعزيز الشفافية المالية من خلال استخدام أدوات تحليل متقدمة، كما تتيح للمستخدمين متابعة إنفاقهم الشهري بسهولة.
- **Expected:** No spelling changes. Output says zero findings. Style tells (تعزيز، من خلال) are **not** touched (detox's job).

### A2 — Convention already consistent
- **Channel:** formal · **Mode:** fix · **House style:** hamza_medial = مسئول
- **Input:** المسئول عن الشئون المالية سيراجع الطلب.
- **Expected:** No change. مسئول/شئون match house style.

### A3 — LinkedIn-style post (anonymized), clean spelling, one dialect slip
- **Channel:** social · **Mode:** flag · **House style:** none (defaults)
- **Input:**
  > في مصر مثل شعبي يقول: "جاي في الهايفة ويتصدّر"،
  > وهذا ينطبق تماماً على بعض الزملاء في الاجتماعات؛ فتجده يتكلم ليملأ الوقت وليس لإضافة شيء ذي قيمة.
  > يدفعه ضعف الثقة دوماً إلى إثبات العكس، أما الواثق فعلياً من نفسه فلا يحتاج لإثبات أي شيء لأحد، ولذلك يبقى كلامه قليلاً.
  > يمكنك ملاحظة ذلك في ثلاثة تصرفات: أن يذكر أسماء معارفه ليُوحي بأنه شخصية مرموقة وذو نفوذ، وأن يزايد بأنه الأكثر تعباً، وأن يسعى جاهداً ليكون آخر المتحدثين. ويميل غالباً إلى انتقاد من حوله.
  > تذكر دائماً: الواثق من نفسه لا يحتاج لإثبات أي شيء لأي حد.
- **Expected:**
  - Counts: **0 errors · 0 convention mixes · 1 ambiguous**.
  - Ambiguous: لأي حد → keep (intentional Egyptian echo of the opening proverb) or لأي أحد / لأحد (matches the earlier لأحد). Not auto-fixed.
  - Quoted proverb (جاي في الهايفة ويتصدّر) is locked: not flagged as dialect or error.
  - Tanween on alif (8 instances: تماماً، دوماً، فعلياً، قليلاً، تعباً، جاهداً، غالباً، دائماً) is consistent → **not** flagged.
  - أما الواثق … فلا يحتاج: جواب أمّا has its فاء → **not** flagged (G5 negative check).
  - **Grammar traps, must NOT flag:** ذي قيمة (genitive by إضافة) · بأنه شخصية مرموقة وذو نفوذ (خبر أنّ is مرفوع, so ذو is correct) · ثلاثة تصرفات (singular تصرّف is masculine → feminine-form number).
  - ليُوحي tashkeel, "" quotes, and lines ending in a comma are listed only as out-of-scope hand-offs to detox, never as spelling findings.
- **Tests:** over-correction on clean text, locked quotes inside MSA, convention consistency logic, dialect-in-MSA ambiguity.

---

## B. Human text with errors

### B1 — P1 cluster
- **Channel:** formal · **Mode:** fix
- **Input:** تم إستخدام النظام الجديد في المدرسه الى جانب ارسال الاشعارات للأهالي.
- **Expected:** استخدام · المدرسة · إلى · إرسال · الإشعارات. 5 changes, all class Error.

### B2 — Fixed phrase
- **Channel:** social · **Mode:** flag
- **Input:** انشاء الله نطلق الميزة الاسبوع الجاي
- **Expected:** Flag إن شاء الله (Error) and الأسبوع (Error). **Do not** flag الجاي (dialect). No text changes.

### B3 — Alif fariqa both directions
- **Channel:** formal · **Mode:** fix
- **Input:** موظفوا البنك يرجوا من العملاء أن يحدثو بياناتهم.
- **Expected:** موظفو · يرجو · يحدّثوا (alif added; shadda not required — يحدثوا is acceptable). 3 changes.

### B4 — Systematic omission
- **Channel:** UI · **Mode:** fix
- **Input:** 12 strings where every أ/إ is written as bare ا (اضافة، الى، اكثر، ادخل…)
- **Expected:** All corrected; change table shows **one** pattern row with a count, not 12+ rows.

### B5 — LinkedIn course promo (anonymized): wasl + weak-letter deletion + grammar
- **Channel:** social · **Mode:** flag · **House style:** none (defaults)
- **Input:**
  > الشركة لا تعرف ماذا تفعل إن لم تقول أنت.
  > مديرك لن يترك عمله ويلمع صورتك.
  > أنت المسئول عن إظهار عملك.
  > ابني علاقات.
  > وإن أردت إختصار سنين من التجربة والخطأ فانضم لنا في الدفعة الثالثة من برنامج Lead Your Career.
  > نبدأ ٩ ديسمبر. فقط ٢١٩ دولار. ٧ محاضرات. أحد وأربعاء من كل أسبوع.
  > أرسل لي رسالة على لينكدإن وسأرسل لك التفاصيل كاملة
  > البقاء فيما تعرفه مريح ولكنه يضيع منك فرص.
  > تحرك فالفرص تظهر لمن يتحرك بإذن الله.
- **Expected:**
  - Counts: **5 errors (3 spelling · 2 grammar) · 0 convention mixes · 0 ambiguous**.
  - **إملاء:** إختصار → اختصار (1.1) · إن لم تقول → إن لم تقل (2.6) · ابني علاقات → ابنِ علاقات (2.6; imperative reading is unmistakable before an object, so Error not Ambiguous).
  - **نحو:** ٢١٩ دولار → ٢١٩ دولارًا (G3, last part 19 governs) · يضيع منك فرص → تضيع منك فرص (G4, non-human plural).
  - **Not flagged:** ٧ محاضرات (digits; counted plural is correct for 3–10) · المسئول (appears once, no mix).
  - Arabic-Indic digits: consistent; hand-off only (detox / house style).
  - Top fix by impact: ابني علاقات (a standalone line; most visible).

### B6 — Spacing inside quotes and parentheses (bilingual post, anonymized)
- **Channel:** social · **Mode:** flag
- **Input:**
  > عندما يسألني أحد - وخاصة العاملين في مجال ال Construction - " هل البيزنس مفيد؟"
  > يكون عادة ردي " تخيل أنك استثمرت مجهودك في آخر ١٠ سنين في بيزنس خاص بك. أين كنت ستكون اليوم؟"
  > بالطبع لا أقول اترك وظيفتك، ولكن كن مستعدا قبل أن تترك هي ( ابدأ بيزنس صغير بجانب عملك)
  > وأول خطوة هي فهمك للغة المال.
  > انضم لنا في الدفعة القادمة من برنامج Lead Your Career.
  > نبدأ ٩ ديسمبر. ٧ محاضرات. أحد وأربعاء من كل أسبوع.  فقط ٢١٩ دولار.
  > أرسل لي رسالة وسأرسل لك التفاصيل كاملة.
- **Expected:**
  - Counts: **5 errors (3 pattern rows: 2 spelling, 1 grammar) · 0 convention mixes · 0 ambiguous**.
  - Row 1, space inside quotes/parentheses ×3: `" هل` → `"هل` · `" تخيل` → `"تخيل` · `( ابدأ` → `(ابدأ`.
  - Row 2, double space ×1: `أسبوع.  فقط` → `أسبوع. فقط`.
  - Row 3 (نحو), G3: ٢١٩ دولار → ٢١٩ دولارًا.
  - **Not flagged (grammar):** آخر ١٠ سنين (10 + plural genitive, correct).
  - **Not flagged:** ال Construction (bilingual term, out of scope) · مستعدا (single tanween instance, no mix) · اترك / ابدأ (correct).
  - Dashes and "" vs «» → detox hand-off only.

### B7 — Heritage aphorisms with mixed hamza omission (high volume)
- **Channel:** formal (republication) · **Mode:** fix
- **House style:** `tanween_fath: on_alif` · `hamza_medial: مسؤول`
- **Input:** `evals/fixtures/b7-ibn-al-jawzi.txt` (17 aphorisms, Ibn al-Jawzi)
- **Why fix applies:** the whole input is the heritage text, so spelling is corrected; wording and existing tashkeel are kept (SKILL.md §0.5).
- **Why not "systematic":** hamza is present in many words (أظلمت، أصلح، أيها، أدركك) and missing in others → random errors, corrected individually, grouped by pattern.
- **Expected changes (grouped rows):**

  | Pattern | Instances |
  |---|---|
  | Missing hamzat al-qat' (1.2) | 20: إخواني، أين ×2، أسير، أوزاره، أطعته، أفادك، أتيته، أن (تجوع)، أنت ×2، أعمى، أنّ ×2 (الجلود، النار)، أمَا (علمت)، أنه، بأعماله، أفعاله، أقبلت، أدركتها |
  | Medial hamza (2.1) | 3: فتأكل، رأيت، تطفئها |
  | Wasl + weak-letter deletion (1.1 + 2.6) | 1: إبك → ابكِ |
  | Missing hamza seat letter (2.1) | 1: مسؤلاً → مسؤولاً |
  | Madda (1.6) | 1: ابن أدم → ابن آدم |
  | Space before punctuation (3.3) | one row, all ، . ؟ ! : instances |
  | Detached و (3.3) | one row, all «و X» instances |
  | يا separate (3.3) | 1: ياطفل → يا طفل |

- **Mechanical hand-off (not counted):** tatweel in تُـطاق.
- **Must NOT change:** لم يبن (correct jussive of بان; looks like a dropped letter but isn't) · واحسرة · ابن عم / محمد بن واسع (3.4) · امتلأ، استشهدت، استخبروا، اذكر (correct wasl) · tanween on alif (matches house style) · existing tashkeel (قلّ، فتعجّب، يُلقى).
- **Tests:** volume handling (grouping vs row spam), heritage-input lock rule, mixed vs systematic omission, a false-positive trap (لم يبن), supplied house style.

### B8 — Modern reflection paraphrasing a classical author: جواب أمّا
- **Channel:** social · **Mode:** flag · **Scope:** full
- **Input:**
  > كلما دخلت إلى مول، أجد نفسي أنظر إلى المواسير ودكتات التكييف.
  > وكان لي صديق مهندس معماري، ينظر إلى فروق السيراميك وسمك الزجاج.
  > يقول ابن الجوزي  لو دخل أرباب الصنائع إلى دار معمورة  رأيت البزاز ينظر إلى الفرش ويحزر قيمته، والنجار إلى السقف، والبناء إلى الحيطان وهكذا.
  > أما همة المؤمن متعلقة بالآخرة، فكل ما في الدنيا يحركه إلى ذكر الآخرة.
  > فإذا رأى ظلمة ذكر ظلمة القبر، وإن سمع صوتا فظيعا ذكر نفخة الصور  وإن رأى الناس نياما ذكر الموتى في القبور، وإن رأى لذة ذكر الجنة.
  > رزقنا الله اليقظة للآخرة والتدبر.
  > والسلام عليكم ورحمة الله وبركاته
  > بإيحاء من كتاب صيد الخاطر لابن الجوزي.
- **Why grammar is on:** the writer's own modern text paraphrasing a classical source, not heritage input.
- **Expected:**
  - Counts: **2 errors (1 spelling row · 1 grammar) · 0 convention mixes · 0 ambiguous**.
  - **إملاء:** double spaces ×3 (ابن الجوزي  لو، معمورة  رأيت، الصور  وإن) → one row.
  - **نحو:** أما همة المؤمن متعلقة → فمتعلقة (G5).
  - **Must NOT flag:** ابن الجوزي / لابن الجوزي (ابن not between two names, keeps alif) · ظلمة، فظيعا (ظ correct) · صوتا، نياما (tanween consistent, no mix) · المواسير، دكتات (loanwords) · فإذا رأى … ذكر (verbal جواب, no فاء needed).
  - Hand-off to detox: missing colon/quotes around the Ibn al-Jawzi paraphrase.
- **Tests:** G5 detection, heritage-vs-modern scope decision, conditional جواب that correctly lacks فاء.

---

## C. Fintech UI strings (consistency)

### C1 — Mixed conventions across screens
- **Channel:** UI · **Mode:** fix · **House style:** defaults
- **Input:**
  - `تم التحويل بنجاح`
  - `المسئول عن الحساب`
  - `حد السحب اليومي: ٥٠٠ شيكل`
  - `يرجى إدخال المبلغ مجدداً`
- **Expected:** مسئول → مسؤول (Convention) · مجدداً → مجددًا (Convention) · digits ٥٠٠ → 500 **only if detox is chained**; imla alone reports digits as out of scope or leaves to house-style hand-off. No other changes.

### C2 — Ambiguity must not auto-fix
- **Channel:** UI · **Mode:** fix
- **Input:** حوّل المبلغ الي علي
- **Expected:** الي → flagged Ambiguous (إلى vs إليّ). علي → flagged Ambiguous (على vs name). Text **unchanged** for both. Note: if the user confirms "to Ali", output إلى علي.

---

## D. Locked spans and dialect (zero false positives)

### D1 — Names and brands
- **Channel:** formal · **Mode:** fix · **locked_terms:** [Meyar, عبدالله العلي]
- **Input:** قدم عبدالله العلي عرضاً عن منصة Meyar الجديده.
- **Expected:** الجديده → الجديدة · عرضاً → عرضًا (Convention). **عبدالله untouched** despite abd_compound = separate.

### D2 — Quranic text
- **Channel:** formal · **Mode:** fix
- **Input:** قال تعالى: «الرَّحْمَٰنُ عَلَّمَ الْقُرْآنَ»، والرحمان هو اسم من أسماء الله.
- **Expected:** Quote untouched. Outside the quote: الرحمان → الرحمن.

### D3 — Levantine post
- **Channel:** social · **Mode:** flag · **Register:** Levantine
- **Input:** شو رأيكم بالتصميم؟ هاد اول مشروع بعملو لحالي
- **Expected:** Flag اول → أول only. شو، هاد، بعملو، لحالي are dialect: **not flagged**.

### D4 — ض/ظ minimal pair
- **Channel:** formal · **Mode:** fix
- **Input:** ضل المشروع متوقفًا بسبب ضروف التمويل.
- **Expected:** ضروف → ظروف (Error). ضل → flagged Ambiguous (ظلّ = remained is likely; ضلّ = went astray is valid). Not auto-fixed.

### D5 — Heritage text: Quran, names, editor footnotes, copy-paste artifacts
- **Channel:** formal (republication) · **Mode:** flag · **House style:** none
- **Input:** `evals/fixtures/d5-muqaddimah.txt` (Ibn Khaldun, Muqaddimah excerpt, as copied from a web edition with footnote markers)
- **Expected:**
  - **Locked, unchanged:** both Quranic verses (المائدة 22 و24), the quotes attributed to Sa'd and Umar, and names (زهرة بن حويه، الجالنوس/جالنوس، بني إسرائيل).
    - حويه is **not** "corrected" to حوية: it is a name.
  - **Corrupted lock note (one line):** ورَبٌّكَ in المائدة 24 carries tanween instead of damma (ورَبُّكَ). Not edited; user asked to verify against the mushaf.
  - **Errors flagged:**
    - و لم يكن → ولم يكن (3.3 attached و)
    - لابد → لا بدّ (1.5)
    - double spaces → one pattern row with count (في  ملكة، واثقين  بعدم، اتبع  جالنوس، إلى  مثل، ابن …[5]  وذلك، [18]  وسبب)
  - **Mechanical hand-off (reported once, not counted):** º in place of ؛ (5 instances) · tatweel in أهـل.
  - **Not flagged:** tanween on alif (سعداً، ألفاً، بأساً، مدلاً، نقلاً، عاجزاً، أحقاباً) is consistent · أظفار (valid plural) · هلاَّ and other classical tashkeel (out of scope) · footnote markers [5]…[21].
- **Tests:** locks under heavy classical prose, name-vs-ة trap, corrupted-lock behavior, artifacts kept separate from spelling.

### D6 — Trap words: إنشاء that is correct, and a non-word typo
- **Channel:** formal · **Mode:** flag
- **Input:**
  > ريد ابن خلدون هنا أن يقارن بين البدو الذين لا يحكمهم أحد (خاصة بدو العرب) وبالتالي تكون الأنفة والعزة عندهم قويةº لأن كل واحد يحمي نفسه وكل قبيلة تحمي نفسها والحضر الذين يحتمون بالجنود والشرطة، فتذهب قوة بأسهم واستعدادهم للقتال، وبالتالي استعدادهم للمطالبة بدولة وإزاحة الدولة السابقة، فحسب نظرية ابن خلدون لا يستطيع إزاحة الدول إلا شعوب فيها عزة وكرامة، تتجمع حول بعضها بحكم القرابة أو الولاء والنجدة والحمية، أما أهـل الحضر فليس فيهم هذه الأمور فلذلك لا يستطيعون إنشاء الدول، وهذه النظرية - وإن كان فيها شيء من الحق - لكن أهل الحضر إن تجمعوا حول الدين واستماتوا في سبيله، فيكون شأنهم شأن أصحاب القوة والمطالبة، وابن خلدون لا يعرض لمثل هذه الأمور وإنما يضع نظرية ويأتي بتطبيقاتها، دون أن يبحث وجهات النظر الأخرى أو الاستثناءات لقاعدته تلك.
- **Expected:**
  - Counts: **0 errors · 0 convention mixes · 1 ambiguous**.
  - Ambiguous (3.6): ريد → likely يريد. Flagged, not fixed.
  - **Must NOT flag:** إنشاء الدول. It is the correct masdar of أنشأ (establishing states) and must not trigger the إن شاء الله rule (1.5).
  - Mechanical hand-off: º ×1 · tatweel in أهـل.
  - Not flagged: "وإن كان … لكن" (style, out of scope) · فليس فيهم هذه الأمور (G4 trap: masculine verb separated from its subject is acceptable).
- **Tests:** context-sensitivity of fixed-phrase rules, non-word typo handling.

### D7 — Corrupted Quranic quote (missing word) + fixed phrase
- **Channel:** formal · **Mode:** flag
- **Input:**
  > إنّ في سماع أخبار الأخيار مقوّياً للعزائم ومعيناً على اتباع تلك الآثار، وقال بعض العارفين: الحكايات جندٌ من جنود الله تقوى بها قلوب المريد. ثم تلا قوله عزوجل لرسوله ﷺ : ﴿ كلا نقصّ عليك من أنباء الرسل ما نثبت به فؤادك وجاءك في هذه الحق وموعظة وذكرى للمؤمنين ﴾.
- **Expected:**
  - Counts: **2 errors · 0 convention mixes · 0 ambiguous** + 1 lock note.
  - عزوجل → عزّ وجلّ (1.5) · `ﷺ :` → `ﷺ:` (3.3).
  - **Lock note:** the verse (هود 120) opens with وكلًّا; the quote drops the و, and without shadda كلا reads as the negation particle. Not edited; user asked to verify against the mushaf.
  - **Not flagged:** spaces inside ﴿ ﴾ (exempt) · existing tashkeel (جندٌ، مقوّياً) · tanween consistent · قلوب المريد (wording, out of scope).
- **Tests:** corrupted-lock behavior for a missing word (D5 covers a wrong diacritic), Quranic bracket exemption.

---

## Scoring sheet

| Case | Expected changes | Actual | Introduced errors | Locked-span edits | Pass |
|---|---|---|---|---|---|
| A1 | 0 | | | | |
| A2 | 0 | | | | |
| A3 | 0 (1 flag) | | | | |
| B1 | 5 | | | | |
| B2 | 0 (2 flags) | | | | |
| B3 | 3 | | | | |
| B4 | n (1 row) | | | | |
| B5 | 0 (5 flags: 3 إملاء, 2 نحو) | | | | |
| B6 | 0 (5 flags, 3 rows) | | | | |
| B7 | 26 + 2 spacing rows (8 rows) | | | | |
| B8 | 0 (2 flags: 1 إملاء row, 1 نحو) | | | | |
| C1 | 2 | | | | |
| C2 | 0 (2 flags) | | | | |
| D1 | 2 | | | | |
| D2 | 1 | | | | |
| D3 | 0 (1 flag) | | | | |
| D4 | 1 (1 flag) | | | | |
| D5 | 0 (3 rows + 1 lock note) | | | | |
| D6 | 0 (1 flag) | | | | |
| D7 | 0 (2 flags + 1 lock note) | | | | |
