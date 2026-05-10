# Designing a parameter playground

This reference assumes the gate (in `when-not-to-use.md`) has passed. It covers practical heuristics for choosing inputs, outputs, and effect coefficients that produce a useful teaching artifact.

## Picking inputs (3-6)

The inputs are the **practitioner's actual control surface** — the knobs they touch.

### Heuristics for finding inputs

1. **Walk through a session.** What does the practitioner adjust during one cycle of the activity? In coffee, they pick beans, set grind, weigh dose, set temperature, push start. The continuously-adjustable ones are inputs.

2. **Read the reference manual or recipe.** Anything called out as "tunable", "set", "adjust", "calibrate" is likely an input. Anything stated as a fixed property is not.

3. **Look at what experts disagree about.** "What grind for V60?" produces a real discussion; "what shape is a V60 dripper?" doesn't. Disagreement = a real knob.

### Common mistakes when picking inputs

- **Confusing dimensional-map axes with playground inputs.** Map axes are *design choices* (e.g., "routing topology"). Inputs are *operational knobs* (e.g., "water temperature for this brew"). If the variable is "what kind of thing is this", it's an axis. If it's "what setting did you use this time", it's an input.
- **Picking too many inputs.** Beyond 6, the playground becomes overwhelming and the user can't isolate effects. Cap at 6.
- **Picking abstract inputs.** "Effort level" or "skill" — too vague. Replace with the specific thing they translate to (technique steps, time invested).

### Input formatting

Each input renders as a slider with a normalized [0,1] internal value. The `formatValue(v)` function maps [0,1] to a human display string. Common patterns:

- **Linear unit**: `(v) => Math.round(60 + v * 40) + "°C"` (60-100°C)
- **Log scale**: `(v) => 15 * Math.pow(24*3600/15, v)` then format seconds → minutes/hours
- **Ordinal categories**: `(v) => labels[Math.floor(v * labels.length)]` (e.g., "Talcum / Fine / Medium / Coarse")
- **Logarithmic dollar amount**: `(v) => "$" + Math.round(10 * Math.pow(10000/10, v))` (e.g., $10-$10000)

The endpoints labels (`leftLabel`, `rightLabel`) should be **anchored to real values**, not abstract ("60°C" not "Cold").

## Picking outputs (4-6)

Outputs should be **the things practitioners care about** — the qualities they assess after the activity.

### Heuristics

1. **Read tasting notes / review vocabulary.** What words do practitioners use to describe results? In coffee: bitter, bright, body, sweet, clean. Those are your outputs.

2. **Look at quality dimensions named in evaluations.** Cupping forms, photo critique rubrics, food review categories, audio measurements.

3. **Avoid "overall good".** A single "quality" output collapses the trade-offs the playground is meant to show. The whole point is that *better one output* often means *worse another*.

### Output design

- **Color**: pick from the palette in the template (`#f97583` warn-red, `#ffb86b` accent-orange, `#b392f0` purple, `#85e89d` green, `#79b8ff` blue) — these match the dimensional-map.
- **Baseline**: most outputs aren't zero at zero-input. Bitterness might be 0.10 (no extraction → small baseline from any extraction at all). Clarity might be 0.55 (high default, *pulled down* by extraction). Set baselines so the bars aren't all flat at zero when sliders are at minimum.
- **Blurb**: one sentence the user can read to understand what the output represents. Practitioner vocabulary, not jargon.

## Calibrating effect coefficients

This is the most error-prone step. The model is a signed weighted sum:

```
output_value = baseline + Σ(over inputs) effect.weight * f(input_value, effect.peak)
```

where `f` is identity for monotonic effects, or a tent function (rises 0→peak, falls peak→1) for non-monotonic.

### Setting weights

- **Sign first, magnitude second.** Lock down the *direction* of every effect from domain knowledge before fiddling with magnitudes.
- **Largest weight ~0.5-0.6.** A single input shouldn't be able to drive an output from baseline to saturation alone; that means too much. Two or three inputs working together should be needed to saturate.
- **Smallest meaningful weight ~0.1.** Below that, the user can't see the effect — drop the entry from the model and document the omission in notes.
- **Sum bound check.** For any output, sum |weights| over all inputs. Should be roughly 1.0 to 2.0. If it's much larger, the output saturates instantly; if smaller, sliders barely move it.

### Setting peaks

A peak makes the response non-monotonic — output rises as input goes from 0 to `peak`, then falls from `peak` to 1.

- **Use peaks for outputs that have a "sweet spot"**: acidity in coffee (peaks at moderate extraction), sweetness in baking (peaks at right caramelization), depth-of-field in photography (peaks at moderate aperture).
- **Position peak at the value where practitioner experience says the maximum is.** For coffee acidity, that's roughly 60-70% of full extraction → peak at 0.6-0.7 for time/temp inputs.
- **Don't over-use peaks.** If most effects are peaked, you've probably overcomplicated. Real systems have ~2-4 peaked relationships among 15-25 effects total.

### Sanity-check the calibration

After writing all effects, simulate by hand:

1. **All inputs at 0**: Outputs should land near baselines. If anything saturates, weights are wrong-signed.
2. **All inputs at 1**: Outputs that monotonically rise (bitterness, body) should be near 1. Outputs with peaked components should be lower (acidity, sweetness, clarity).
3. **Each preset**: Should produce a *recognizable* profile. If "espresso" doesn't show high body and bitterness, the model is wrong.
4. **A breaking combination**: A combination that doesn't show up in the field (e.g., high pressure + coarse grind for coffee) should still produce a *visibly off* profile. If it produces a normal-looking cup, the model is too forgiving.

## Designing presets

Presets are the **pedagogical payoff** of the playground. Each preset:

- Should match a named instance from the corresponding dimensional map (if there is one).
- Sets all inputs at once — clicking it should produce a recognizable output profile in 0.2 seconds.
- Is a teaching moment: clicking "Espresso" then "Cold brew" lets the user *see* how shifting four sliders shifts five bars.

### Number of presets

3-6. Less than 3 doesn't show contrast; more than 6 dilutes. The presets should span the input space — pick examples at the corners of the design space, not all from one neighbourhood.

### Presets that defy the bundle

Optional but powerful: include 1 preset that combines inputs that *don't* normally co-occur, to show what the playground predicts. Label it as "Counterfactual". E.g., for coffee: "9-bar pressure + coarse grind + long time" — what would that cup taste like? The playground answers, and the answer (typically: low extraction, watery, no acidity, low caffeine) explains why this combination doesn't exist in the field.

This is the cleanest demonstration of why **bundles exist** — combinations off the bundle simply don't make good cups.

## Writing the "How this works" notes

Every playground must include a `notes` section explaining:

1. **The model shape** — signed weighted sum + optional peaks. Be explicit about it.
2. **Calibration grade** — "teaching-grade, not lab-grade". Don't pretend it's predictive.
3. **The pedagogical point** — what the user is supposed to *learn* from playing with it (usually: bundles, trade-offs, why the field clusters).
4. **At least one combination it gets visibly wrong** — model honesty. Coffee playground: "low temp + high pressure" produces plausible-looking output but in reality the espresso machine wouldn't pull a shot at all.

This honesty is what separates a useful teaching tool from pseudo-science.

## Common calibration mistakes

| Symptom | Cause | Fix |
|---|---|---|
| Bars never move much | Weights too small | Bump strongest to ~0.5 |
| Bars always pinned at 0 or 100 | Weights too large | Cap individual weights at ±0.6 |
| Presets all produce similar output profiles | Effects not actually input-specific | Re-examine sign/magnitude per (input, output) |
| Acidity rises forever instead of peaking | Missing peak parameter | Add `peak: 0.6-0.7` to acidity effects |
| One output never reaches > 0.4 | Baseline too low or weights too negative | Adjust baseline or rethink whether this output is genuinely dampened |
| Specific known combination feels obviously wrong | Coefficients miscalibrated for that quadrant | Add a new effect or peak to capture it |
