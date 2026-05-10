---
name: parameter-playground
description: Interactive explorer of how controllable inputs shape observable outcomes — sliders driving live outcome bars. Companion to /dimensional-map for domains with a public causal model (strict gate against pseudo-science). Triggers: "how does X affect Y", "let me play with parameters", "what if I changed X", "/parameter-playground".
trigger: /parameter-playground
---

# /parameter-playground

A multislider input → outcome explorer. Companion skill to `dimensional-map` — where the map shows *where instances sit* in a design space, the playground shows *why they sit there* by making the input → outcome relationships interactive.

If `dimensional-map` is the noun (the field), `parameter-playground` is the verb (the dynamics). They're complementary on the same domain when both structure and dynamics are knowable; only the map is shipped when dynamics aren't.

## When to use

- **After a dimensional map** to give the same domain a dynamic dimension. ("We mapped coffee brewing methods. Now let me feel how grind and time interact.")
- **Standalone exploration** of a single causal system. ("How do photography exposure inputs map to outcomes?")
- **Defying bundles** — once a dimensional map shows that input X tends to come with input Y, the playground lets the user *break the bundle* and see the cup that combination would produce. This is where most of the learning happens.

## The Gate (read before invoking)

Before producing a playground, run this gate. If you fail more than 1 of these checks, **don't ship a playground** — explain to the user that the domain doesn't support quantitative input → output modelling and ship just the dimensional map (or a written analysis).

### Gate criteria — answer each "yes / no / weak"

1. **Crisp inputs.** Can you name 3-6 *controllable* variables at the domain's natural level of abstraction, each with a meaningful numeric or ordinal scale? (Concrete knobs like "water temperature 60-100°C", not abstract design choices like "consistency model".)

2. **Crisp outputs.** Can you name 4-6 outcomes that change *continuously and observably* — and are recognised by practitioners as the things you tune for?

3. **Direction of effect with confidence.** For at least 80% of (input, output) pairs, can you state the direction of effect (more/less/peaked) without inventing it? Citations or domain-standard practitioner knowledge required, not vibes.

4. **No major non-additivities.** The model behind the playground is a signed weighted sum of input contributions plus optional non-monotonic peaks. If the actual causal model has strong interaction effects (X only matters when Y is high), multiplicative behaviour, threshold dynamics, or feedback loops, the model is wrong in shape, not just magnitude. In that case, either build a custom model or skip the playground.

### Examples — pass

- ☕ Coffee extraction (water temp, grind, time, pressure → bitterness, acidity, body, caffeine, clarity)
- 📷 Photography (aperture, shutter, ISO → exposure, depth-of-field, motion blur, noise)
- 🍞 Bread baking (hydration, time, temperature, salt → crumb, crust, chew, flavour development)
- 🔊 Hi-fi room (placement, damping, EQ → bass response, stereo image, clarity)
- 🎸 Guitar tone (gain, EQ, compression → grit, presence, sustain)
- 🌱 Plant care (water, light, nitrogen → growth rate, leaf colour, flowering)

### Examples — fail (don't ship a playground)

- ❌ Choosing a database (input "scale" doesn't drive "complexity" linearly; effects are categorical and non-additive)
- ❌ Literature / philosophy / political theory (outcomes are interpretive, no agreed causal model)
- ❌ Management practices (effects depend on context, team, history — strong interaction effects)
- ❌ Most design aesthetics (taste, not function — outcomes aren't observable in the same way)
- ❌ Anything where "more X causes more Y" only sometimes, and the *sometimes* is the point

In failed-gate cases: tell the user *why* the playground would be misleading for this domain (cite the failed gate criterion) and suggest the dimensional map alone, or a written analysis.

## Workflow (if the gate passes)

### Step 1 — Pick inputs (3-6)
Each input must have:
- `name` (concrete, not abstract)
- semantic `leftLabel` and `rightLabel` (banned: "low/high", "less/more")
- `default` value (a sensible starting point in the field)
- `formatValue(v)` — turns a normalized [0,1] value into a display string with units. Use this to handle log scales, ordinal categories, etc.
- `blurb` — one sentence on what this input controls

Inputs should be the *practitioner's actual control surface*, not theoretical variables.

### Step 2 — Pick outputs (4-6)
Each output must have:
- `name` (recognised by practitioners — "bitterness" not "extraction byproducts")
- `color` (visual swatch)
- `baseline` — the value when all inputs are at zero (acknowledges that some outputs aren't zero at zero-input)
- `blurb` — one sentence on what this output represents

### Step 3 — Define effects (the model)
For every (input, output) pair where the direction of effect is known, write:
- `weight` — signed coefficient. Positive = input ↑ → output ↑. Negative = input ↑ → output ↓.
- optional `peak` — a value in [0,1] where the response inverts (rises 0→peak, falls peak→1). Use for non-monotonic relationships (acidity peaks at moderate extraction).

Cap total effects at 4× (inputs × outputs); typically only ~60% of pairs have notable effects.

**Magnitude calibration.** Coefficients should be loosely consistent — the strongest single-input contribution shouldn't exceed ~0.6, and most outputs should land somewhere in [0.2, 0.9] across realistic input combos. If the playground always shows everything at 0 or 1, the coefficients need rebalancing.

### Step 4 — Define presets (3-6)
Presets are named instances projected back into input space — usually the same instances that appeared as exemplars in the corresponding dimensional map. Each preset:
- `key` (slug)
- `name` (display)
- `values` — a partial mapping `{ inputId: normalizedValue }` covering all (or most) inputs

**Presets are where the playground earns its keep.** They let the user click "Espresso" and "Cold brew" and see the outputs flip — instantly conveying that named instances are *bundles of input choices*, just like the map's endpoint dossiers tell you they're bundles of co-occurring traits.

### Step 5 — Render
Clone `templates/playground.html.tmpl`, edit only the `PLAYGROUND = { ... }` block at the top. Save next to the source if there is one, or in `~/parameter-playgrounds/<slug>.html`.

### Step 6 — Honest reporting
Always include in the rendered output and in your text reply:
- That the model is **teaching-grade**, not lab-grade
- What kind of evidence backs the coefficients (published studies / practitioner consensus / informed guesses)
- Which input/output pairs you didn't model and why
- At least one combination the playground gets visibly wrong (e.g., low temp + high pressure for coffee — model predicts plausible-looking output but in reality the espresso machine wouldn't pull the shot at all)

## Output schema (intermediate JSON)

```json
{
  "meta": {
    "title": "...", "h1": "...", "headerSub": "...",
    "metaCallout": "...", "notesH": "How this works",
    "notes": "...", "footer": "..."
  },
  "inputs": [
    {
      "id": "temp", "name": "Water temperature",
      "leftLabel": "Cold (60°C)", "rightLabel": "Boiling (100°C)",
      "blurb": "Higher = more extraction overall, peaks past ~93°C.",
      "default": 0.8,
      "formatValue": "(v) => Math.round(60 + v * 40) + '°C'"
    }
  ],
  "outputs": [
    { "id": "bitterness", "name": "Bitterness", "color": "#f97583",
      "baseline": 0.10, "blurb": "Late-extraction compounds." }
  ],
  "effects": [
    { "input": "temp", "output": "bitterness", "weight": 0.55 },
    { "input": "temp", "output": "acidity", "weight": 0.45, "peak": 0.70 }
  ],
  "presets": [
    { "key": "espresso", "name": "Espresso",
      "values": { "temp": 0.85, "grind": 0.05, "time": 0.30, "pressure": 1.00 } }
  ]
}
```

## Anti-patterns (red flags)

| Symptom | What it means | Action |
|---|---|---|
| Coefficients invented to "look right" | Pseudo-science | Stop. Either find the real numbers or fail the gate |
| All effects monotonic, no peaks | Probably oversimplified | Real causal systems usually have at least one peaked relationship |
| Output bars never move when sliders change | Coefficients too small | Rebalance — strongest weights ~0.5-0.6 |
| All outputs always pinned at 0 or 1 | Coefficients too large | Cap individual contributions at ±0.6 |
| Inputs are abstract concepts (not knobs) | Wrong level of abstraction | Replace with practitioner control surface |
| Presets all give same output profile | Inputs aren't actually controlling outputs | Likely model failure — re-examine effect signs |
| User can't articulate what each output *means* | Fail the gate | Don't ship |
| Filler qualifiers in copy ("named presets", "various inputs", "different methods") | Trivialities that pad without informing — if the names follow immediately, the qualifier is dead weight | Strike them. Write "5 presets (espresso, ...)" not "5 named presets (...)". |

## No-filler rule

Don't add adjectives or qualifiers that don't carry information. Audit every adjective: if removing it loses no meaning, remove it. See `dimensional-map/SKILL.md` for the canonical no-filler list — same rule applies here.

## Bilingual mode (optional)

The template supports an optional second language. Add a `de: { ... }` block (or other ISO code) inside `PLAYGROUND` with the same shape as the top-level — `meta`, `inputs`, `outputs`, `presets`. The renderer merges per-field, so partial translations work (missing keys fall back to canonical). Coefficients, peaks, weights, IDs, colours, and effect arrays stay canonical (top-level only) — only translatable strings go in the override.

A language toggle (EN/DE pills, top-right) is rendered automatically when `PLAYGROUND.de` is present. Behaviour matches the dimensional-map's toggle: cookie-persistent, falls back to `navigator.languages`. When generating a playground for a German-speaking user, populate `PLAYGROUND.de` alongside the canonical block.

## Pairing with `dimensional-map`

The two skills are **complementary, not redundant**. Typical workflow:

```
User: "I'm new to coffee brewing — help me get a feel for the field"
  → /dimensional-map of coffee brewing methods
     (axes: pressure, time, grind, filtration, cost, ritual)
     (exemplars: espresso, AeroPress, V60, French press, cold brew)
     (endpoint dossiers: each pole as a bundle)

User: "OK, I see the structure. Now let me feel how it actually works."
  → /parameter-playground of coffee extraction
     (sliders: water temp, grind, time, pressure)
     (outputs: bitterness, acidity, body, caffeine, clarity)
     (presets matching the map's exemplars)
```

The map teaches the **categories**; the playground teaches the **dynamics that produced the categories**. Together: a learner moves from "five coffee methods exist" → "here's the input space they live in, here are the outcomes they trade off, here's why the field clusters where it does."

## References

- `references/when-not-to-use.md` — extended gate, with worked examples of failures
- `references/playground-design.md` — heuristics for picking inputs, outputs, calibrating coefficients
- `templates/playground.html.tmpl` — self-contained HTML renderer; clone & edit `PLAYGROUND` block
