# Colloquiality check

A short, practical reference for the colloquiality check called out in the main skill. The audit before you write the first sentence in any non-English variant.

## Why this matters

Literal translations of English technical terms read as obvious machine output to native experts, even when the grammar is correct. The reader's first impression of a dimensional map is its language — if a single load-bearing term lands as a calque, the map's authority collapses before the dimensions get a chance to do their work.

The user who triggered this reference saw "Verfügbarkeit von **Bodenwahrheit**" (literal calque of "ground-truth availability") and pushed back: "Mag wörtlich korrekt sein, versteht aber kein Mensch." That's the failure mode to avoid.

## The audit

Before you write the first non-English sentence:

1. **List the load-bearing technical terms** in the EN source. Anything that appears as an axis `name`, `short`, `tagline`, or as a recurring concept in the prose. For a typical map, ~15-30 terms.
2. **For each, decide one of three classifications**:
   - **Native term established** → use it (e.g., DE "Halbwertszeit" for half-life).
   - **Loanword established** → keep the English (e.g., DE "Ground Truth", "Deployment").
   - **No good translation; calque tempting** → paraphrase around it. Do **not** coin a new compound just to translate.
3. **Test by reading aloud**. If a constructed compound feels like a translation-exam answer, it probably is one. Rewrite.
4. **Sanity-check loanword grammar**. Loanwords inherit a gender in the target language — get the article right. DE: "die Map" not "der Map"; "das Deployment" not "die Deployment".
5. **Pick one form per concept and stick with it**. Mixing "Ground Truth" and "Bodenwahrheit" within one document is worse than committing to either.

## Starter glossary — German AI/ML/tech

Not exhaustive, but covers the most common dimensional-map vocabulary. Use the **Use** column directly; the **Avoid** column shows the calque traps.

| Concept (EN)         | Use (DE)                          | Avoid                              | Why                                                                 |
|----------------------|-----------------------------------|------------------------------------|---------------------------------------------------------------------|
| Ground truth         | **Ground Truth**                  | ~~Bodenwahrheit~~                  | Loanword universal in German AI/ML.                                 |
| Blast radius         | **Blast Radius**                  | ~~Schadenradius~~                  | Schadenradius lives in physical-safety German, not DevOps.          |
| Half-life            | **Halbwertszeit**                 | ~~Half-life~~                      | Native scientific term, fully established.                          |
| Deployment           | **Deployment**                    | ~~Einsatz-Verfahren~~              | Loanword universal in DevOps; "Einsatz" works in policy/legal prose. |
| Pipeline             | **Pipeline**                      | ~~Datenröhre~~ ~~Verarbeitungskette~~ | Loanword universal.                                                  |
| Prompt injection     | **Prompt Injection**              | ~~Eingabeaufforderungs-Einschleusung~~ | Loanword universal in DE security.                                  |
| Stack (tech)         | **Stack**                         | ~~Stapel~~ (tech context)          | "Stapel" is fine for paper/cards, not for tech stacks.              |
| Token                | **Token**                         | ~~Wertmarke~~ ~~Zeichen~~          | Loanword universal in ML.                                            |
| Embedding            | **Embedding**                     | ~~Einbettung~~ (tech context)      | Native "Einbettung" exists but loanword dominates ML usage.          |
| Fine-tune            | **Fine-Tune** / **Fine-Tuning**   | ~~Feinabstimmung~~ (tech context)  | Loanword dominates; "Feinabstimmung" is fine in adjacent domains.    |
| Modality fit         | **Modalitätspassung**             | (no good loanword)                 | Native compound is acceptable; reads as German.                      |
| Adoption maturity    | **Adoptionsreife**                | ~~Annahme-Reife~~                  | "Adoption" is well-loaned into German tech speech.                   |
| Adversarial pressure | **adversarialer Druck**           | ~~feindlicher Druck~~              | Calque-style adjective established; "feindlich" wrong register.      |
| Map (the artifact)   | **Karte** *or* **Map** (feminine) | "im Map", "der Map" → wrong gender | If using loanword: "die Map", "auf der Map". Native "Karte" cleaner. |
| Gate (verb)          | paraphrase: **blockieren** / **begrenzen** / **kontrollieren** | "gatet", "gegatet" repeatedly | Some DevOps DE uses it, but overuse reads as cargo loan.             |
| Skill substitution   | **Substitutionsrichtung**         | ~~Fertigkeits-Ersetzung~~          | Latin-derived compound is natural in DE labor-economics.             |

When in doubt, search GitHub issues, Stack Overflow, or fediverse threads in the target language for the term. If five out of five native posts use the loanword, that's your answer. If they use a paraphrase, take it.

## Worked example: the Bodenwahrheit case

**The mistake:**
```
axes: [
  {
    name: "Verfügbarkeit von Bodenwahrheit",
    short: "Oracle",
    ...
  }
]
```

`Bodenwahrheit` is a literal calque: `ground` → `Boden`, `truth` → `Wahrheit`. Grammatically a valid German compound noun. Yields ~0 hits in native AI/ML German writing. Native writers use "Ground Truth" (loanword), "Referenz" (paraphrase), or "Goldstandard" (paraphrase). Picking the literal compound was the failure.

**The fix:**
```
axes: [
  {
    name: "Verfügbarkeit von Ground Truth",
    short: "Oracle",
    ...
  }
]
```

Note the rest of the row was already correct: `short: "Oracle"` (loanword) and `kind: "Verifizierbarkeit"` (native term). The bug was isolated to one calque in the long form.

**The systemic fix:** before writing the DE variant, audit the EN axis names + short labels + recurring nouns against the table above. Most of the calque traps cluster in three or four terms per map; spending five minutes on the audit avoids the embarrassment of shipping "Bodenwahrheit" in production.

## Generalizing to other languages

The same pattern holds in French (`vérité-terrain` is acceptable but `Ground Truth` is also common in ML; literal compound calques of English ML terms read poorly), Italian, Spanish, Japanese (where loanword vs. kanji rendering choice carries similar weight). For any target language: find native ML/tech writing, see what the community says, copy that pattern. Don't invent.
