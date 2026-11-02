# House Style — Orthography Tokens

A project-level block that resolves every **Convention [C]** item to one form. Define it once per product or client; the skill enforces it across all text.

If the user supplies no block, apply the **defaults** below and state that you did.

## Template

```yaml
house_style:
  project: "<name>"

  # Hamza seat conventions
  hamza_medial: مسؤول          # مسؤول | مسئول   (also governs شؤون/شئون، رؤوس/رءوس)
  plural_verb_hamza: يقرؤون     # يقرؤون | يقرأون
  hundred: مئة                  # مئة | مائة
  final_hamza_ya: مبادئ         # مبادئ | مبادىء

  # Tanween
  tanween_fath: on_letter       # on_letter (كتابًا) | on_alif (كتاباً)
  adverb_tanween: true          # true: أيضًا، شكرًا  | false: أيضا، شكرا

  # Names and compounds (generic text only — real people's names are locked)
  abd_compound: separate        # separate (عبد الله) | joined (عبدالله)
  ibn_in_lineage: بن            # بن | ابن

  # Hand-offs to arabic-ai-detox (listed here so both skills read one source)
  digits: western               # western (3) | arabic_indic (٣)
  quotes: «»                    # «» | ""

  # Spelling policy override (else derived from channel)
  policy: auto                  # auto | fix | flag | off

  # Project lexicon: brand and product terms written exactly as listed
  locked_terms: []
```

## Defaults (when no block is supplied)

| Token | Default | Rationale |
|---|---|---|
| hamza_medial | مسؤول | The more widespread modern form |
| plural_verb_hamza | يقرؤون | Follows the same seat rule |
| hundred | مئة | Matches pronunciation; مائة is accepted |
| final_hamza_ya | مبادئ | Modern typography |
| tanween_fath | on_letter | Current Academy-aligned practice |
| adverb_tanween | true in formal/UI, false in casual | Channel-dependent |
| abd_compound | separate | |
| ibn_in_lineage | بن | |
| digits | western | Product UI and fintech default |
| quotes | «» | Editorial default |

## Precedence

1. Explicit instruction in the current request
2. Project `house_style` block
3. Defaults above

`locked_terms` always wins over every rule, including errors: if a brand spells itself with a nonstandard form, keep it.

## Ownership

This file is the **single source of truth** for orthographic conventions. `arabic-ai-detox` should read `digits` and `quotes` from here when `arabic-imla` is installed, instead of keeping its own copy.
