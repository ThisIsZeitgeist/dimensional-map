# Quality gates

Every candidate axis from step 2 must survive these gates before it ships in the map. The gates are deliberately strict — most generated axes will fail one of them, and that's the point.

## Gate 1 — Discriminating

The named instances on this axis must spread across **at least 0.5 of the range**. If everything clusters in a 0.1 band, the axis is not informative — it's just an assertion that "this thing matters."

### How to check
After step 2, compute `max(pos) − min(pos)` across your named instances. If < 0.5, the axis fails this gate.

### Worked failure
- **Axis: "Use of attention"** (modern LLMs)
- All instances at 1.0 (everyone uses attention)
- Spread = 0.0 → fails. Drop.

### Worked rescue
- "Use of attention" → reframe as "Attention pattern": dense full attention (1.0) ↔ sparse / sliding-window / linear (0.0). Now Mistral-7B with sliding window sits at 0.4, GPT-3 at 1.0, RWKV at 0.0. Spread = 1.0 → passes.

## Gate 2 — Semantic endpoints

Both poles must have **concrete, semantic labels**. No placeholder vocabulary.

### Banned words at endpoints
"low", "high", "weak", "strong", "less", "more", "worse", "better", "simple", "complex".

These words don't tell the reader what the pole *contains*. The pole has to be nameable as a *kind of thing*, not as a magnitude.

### Worked failure
- **Axis: "Complexity: low → high"** → meaningless. What's complex about it? What kind of complexity?

### Worked rescue
- Replace with: "Reasoning architecture: monolithic forward pass → explicit Sys1+Sys2 split"
- Now the poles are concrete kinds-of-thing, and the axis explains *what* gets more complex.

### Test
Read the pole label in isolation. Can you point to a real instance and say "yes, that's it"? If not, the label is too abstract.

## Gate 3 — Orthogonal-ish

Two axes must not be near-duplicates. If two axes correlate above ~r=0.85 across your exemplars, they're measuring almost the same thing and you should:
- **merge** them into one (broader name covering both), or
- **drop** one and move the relationship to co-variations.

### How to check
Compute Pearson correlation between the position vectors of any two axes. If > 0.85, treat as duplicate.

(Heuristic substitute when you don't want to compute: ask "if I told you a model's position on axis A, could I predict its position on axis B with high confidence?" If yes, they're duplicates.)

### Worked failure
- "Parameter scale" + "Training compute" — almost perfectly correlated for any specific era. Merge into "Scale" with both flagged in the bundle, and put the coupling in co-variations.

### Worked acceptable
- "Parameter scale" + "Modality breadth" — correlated (~0.7) but not duplicate. The non-overlap (Llama-3-70B is large but text-only) carries information. Keep both.

## Gate 4 (optional) — Surprise

Does knowing this axis change how you'd think about an instance?

If knowing the axis is "just one more checkbox", the axis is feature-list-grade, not dimensional-map-grade. The best axes *shift mental models* — after seeing them, the reader sees the field differently.

This gate is optional because not every axis needs to be a revelation, but if you find your map is full of "yeah, OK" axes, force this gate on at least 3 of them.

### Test
"After learning this axis, what about the field do I now see that I didn't see before?" If you can't answer in one sentence, the axis is dull. Either reframe it sharper, or drop.

## After the gates

You should have 8-10 surviving axes. Less than 6? Don't pad — ship what survives, and report the dropped ones honestly. More than 12? Cut the weakest by Gate 4 (surprise) until you're at ≤10.

The dropped axes are useful too — list them in the analysis output with their gate-failure reason. Readers learn from what *isn't* an axis as much as from what is.
