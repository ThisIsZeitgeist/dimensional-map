# Endpoint elaboration — the bundle, the exemplar, the why

The most common failure mode of a dimensional map is treating **endpoints as scalar values** when they are, in fact, **categories defined by joint position on many variables**. Saying "1T+ frontier MoE" without elaboration is like saying "fast car" — a label compressing many specifics that experts unpack automatically and novices don't.

This is the load-bearing reference. Read it before doing step 4.

## What an endpoint actually is

When you label a pole "1T+ frontier MoE", you've named a *cluster*. Real instances near that pole share many properties beyond parameter count:
- closed lab (open frontier MoE doesn't really exist)
- multimodal (only frontier scale amortizes the data work)
- post-hoc RLHF + Constitutional alignment (only labs at this scale fund alignment teams)
- API-only deployment (weights too big to ship)
- $100M+ training run (the price of admission)

These traits aren't *implied* by parameter count alone — physics doesn't say a 1T model has to be closed. They cluster because of **economics, engineering forcing functions, design coupling, regulation, and history**. The bundle is the actual thing your reader is encountering when they meet a "frontier MoE" in the wild. Without the bundle, the label is a shorthand they can't unpack.

## The four-part dossier

For each pole of each axis, write:

### 1. Label
The short label that goes on the chart. ≤6 words, concrete. Same as before.

### 2. Bundle (3-5 traits)
Things that almost always co-occur at this pole, **beyond the variable being graphed**. The bundle is what makes the endpoint a *kind of thing*.

Rules:
- **3-5 items**, no more. >5 means you're padding; <3 means you don't know the pole well.
- Each trait should be **another variable** that this pole's instances tend to land at a specific value on. Not the same variable said differently.
- Traits should be **observable**: "expensive" is OK, "well-engineered" is not.

### 3. Canonical exemplar
The single instance most strongly associated with this pole. The "if I had to pick one" answer. Often a tiebreaker — when in doubt about the bundle, ask "what does X look like?" and read off its other properties.

### 4. Why-bundled (1 sentence)
The mechanism that makes these traits travel together. Pick one (or combine):
- **Economic** — only X-level capex affords Y, Z, W
- **Engineering forcing function** — X requires Y; Y requires Z
- **Design coupling** — X presupposes Y (live distillation presupposes a teacher source)
- **Regulatory / social** — closed lab → no public weights → API-only
- **Path-dependent history** — X became convention after Y was built; alternatives exist in principle but aren't tried

The mechanism is what the reader actually *learns*. Without it, the bundle is mysterious.

## Worked example — Parameter scale axis

### Left pole — "1B (small research)"
- **Bundle**: academic-affordable training run · single-node feasible · open weights typical · text-only · DP-only training
- **Canonical exemplar**: Mistral-7B
- **Why-bundled**: cost ceilings on individuals and small labs force the whole bundle to co-occur. You can't run a multimodal RLHF pipeline on $50K of compute.

### Right pole — "1T+ (frontier MoE)"
- **Bundle**: closed lab · multimodal · post-hoc RLHF + Constitutional · API-only deployment · $100M+ training run
- **Canonical exemplar**: GPT-4 / Claude Opus
- **Why-bundled**: capex pays for the whole stack. Only labs with this budget amortize multimodal data, run alignment programs, and operate inference infrastructure. Weights are too big to ship even if the lab wanted to open-source.

Notice:
- The bundles don't say "many parameters" — that's already the axis. They say what *else* clusters at this pole.
- The bundles don't overlap. Each item belongs to one pole.
- The why-bundled is *causal*, not just descriptive.

## Why this matters

### For the learner
Once a learner sees that "frontier MoE" comes with `closed-lab + multimodal + post-hoc-RLHF + API-only` as a *package*, they understand the endpoint as a *kind of thing*, not a *value*. The next time they meet a new frontier model, it slots into the bundle without re-learning. That compression is what expertise *is*.

### For the analyst
The bundle explains why **moving along an axis is hard**. You're not flipping one knob, you're trying to break a cluster apart. Consider the AeroPress: it pressurises lightly without demanding fine grind or expensive equipment, defying the espresso bundle that ties pressure to grind to cost. The bundle predicts: an AeroPress designer has to invent technique workarounds — paper microfilters, plunge timing, inversion methods — for affordances that espresso machines get from their full stack. Anomalies pay tax for what the bundle gives away.

### For the field
Once you map the bundles, you can predict where **anomalies will appear** — instances that defy the bundle, e.g. a small open model with frontier-style alignment, or a frontier model running on consumer hardware. Anomalies are usually the most interesting research directions.

## Anti-patterns

| Symptom | What's wrong | Fix |
|---|---|---|
| Bundle = "more of the variable" | Missed the point | The bundle should contain things *not* on the axis |
| Bundle has 6+ items | Padding | Cap at 5, pick load-bearing |
| No canonical exemplar | You don't know this pole | Research more before mapping |
| Why-bundled = "they just do" | No mechanism | Find economic, engineering, design, regulatory, or historical cause |
| Both poles have the same bundle items | Bundle isn't pole-specific | Re-do; bundle items must distinguish |
| Bundle items all subjective ("better") | Not observable | Replace with observable properties |
| Pole labelled with magnitude only ("very large") | Not a category | Re-label as a kind-of-thing |

## When to skip endpoint elaboration

Almost never. Even for "obvious" axes, the bundle teaches. The only exception is when the axis is genuinely scalar with no clustering — e.g., "release year". In that case, the pole is just a value, and you can write `"bundle": []` with `"why": "Pure scalar; no bundled traits."` But this should be rare. If you find yourself writing this for >2 axes, the axes themselves are the problem.
