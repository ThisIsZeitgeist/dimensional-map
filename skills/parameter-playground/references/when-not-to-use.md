# When NOT to ship a playground

The playground's failure mode is **pseudo-quantitative authority**: sliders and bars look scientific, so users trust the model. If the model is invented, the playground actively teaches false intuitions.

This reference is the long version of the gate in SKILL.md. Read it before generating a playground for any domain you haven't built one for before.

## The four failure shapes

A playground misleads when any of these is true. Each is a hard-fail.

### 1. Outputs aren't observable

If practitioners don't agree on what the outputs *are*, or measure them differently, a playground that displays them as smooth bars from 0 to 100 is misleading authority.

- ✗ "Quality of writing" — interpretive, no agreed measure
- ✗ "Code maintainability" — depends on team and timescale
- ✗ "How well a meeting went" — multidimensional and contested
- ✓ "Bread crumb openness" — visible, photographable, ranked by bakers
- ✓ "Photographic depth-of-field" — quantitative (a function of aperture, distance, sensor)

### 2. Inputs aren't controllable knobs

A playground only makes sense when the inputs are things the *user actually adjusts in practice*. Variables that describe the system rather than control it should sit on a dimensional map, not a playground.

- ✗ "Database consistency model" — a categorical choice, not a knob
- ✗ "Architecture style" — choice, not slider
- ✗ "Genre of book" — describes, doesn't control
- ✓ "Aperture f-stop" — knob
- ✓ "Espresso pressure" — knob (on a paddle machine)

### 3. The model has strong interactions or feedback

The playground's math is a signed weighted sum of input contributions plus optional non-monotonic peaks. It cannot represent:

- **Multiplicative interactions**: "X only matters when Y is also high" (e.g., in cooking, salt amount * dish acidity has a multiplicative perceived saltiness effect that a sum can't capture).
- **Threshold dynamics**: "below X = nothing happens, above X = explosion" (e.g., yeast viability has hard thermal thresholds; you don't get gradient response).
- **Feedback loops**: "X drives Y which drives X back" (e.g., trading models, ecological models).
- **Discrete state changes**: phase transitions, mode switches.

If any of these dominate the domain, the playground's output will be visibly wrong even with right-direction coefficients. Build a custom simulator or skip the playground.

### 4. The user knows the domain better than the model

If the user is already an expert in the domain, a teaching-grade playground gives them nothing. They'll see the eyeballed coefficients immediately and lose trust in the rest of the artifact.

This is a softer fail — the playground isn't *wrong*, it's just *not useful*. In that case, ship a more sophisticated artifact (custom simulator, parameter-sensitivity table) or skip.

## Worked failures

### Database choice — fails gates 1, 2, 3

Inputs would have to be: scale, consistency, schema flexibility, query expressiveness, distribution, ops complexity. None of these are *knobs* — they're categorical or near-categorical *commitments*.

Outputs would have to be: throughput, latency, ops effort, cost. These are observable but depend on workload pattern, which the playground can't model.

Effects: "more consistency = less throughput" — true sometimes, complicated by workload, geography, hot keys. Strong interactions everywhere.

→ Ship a dimensional map instead. Position SQLite, Postgres, MongoDB, Redis, DynamoDB, Cassandra on axes. The map shows the categories cleanly; the playground would invent a model that doesn't exist.

### Choosing a programming language — fails gate 1, partially gate 2

Outputs people care about: development speed, runtime performance, ecosystem maturity, hireability, fun. Highly subjective and team-dependent.

Inputs aren't really tunable either — you choose Python or Rust, you don't slide between them.

→ Dimensional map only. Axes: type system strength, performance ceiling, ecosystem size, learning curve, deployment shape.

### Investment portfolio allocation — fails gates 3, 4

Inputs (% allocation per asset class) ARE knobs. Outputs (return, volatility, drawdown) ARE observable.

But: feedback loops (allocation affects market prices), regime changes (correlations break in crises), strong interactions (asset A correlates with asset B differently in different regimes). Real models are stochastic and non-stationary.

A teaching-grade playground would give terribly false confidence to a non-expert; an expert wouldn't trust it. Lose-lose.

→ If teaching, use a real simulator (Monte Carlo over historical or stochastic returns) — not a playground.

### Cooking pasta sauce — passes (carefully)

Inputs: tomato amount, cooking time, salt, heat, oil. All knobs.
Outputs: thickness, tartness, salt perception, depth-of-flavour. Observable, even if subjective.
Effects: mostly monotonic (more salt = saltier; longer time = thicker), some peaked (acidity peaks then mellows).
Interactions: salt × acidity has some interaction, but additive model is reasonable as a first approximation.

→ Pass, but flag in the notes that salt × acidity interaction is real and the model approximates.

## The honest pivot

When the gate fails, the playground isn't the right answer — but the *user's underlying request* is still legitimate. They want to feel the dynamics. Pivot:

- **Domain has structure but not dynamics** → dimensional map alone, with a written "how things move together" panel that lists the couplings explicitly. (The map's covariation panel already does this.)
- **Domain has dynamics but not in additive form** → suggest a custom simulator or visualisation appropriate to the actual model shape (Monte Carlo, agent-based, system-dynamics, etc.).
- **User is an expert** → suggest writing a parameter-sensitivity analysis with real numbers from their own work, not a stylised playground.

Failing the gate is not "we can't help you" — it's "this specific artifact would mislead, here's what would actually serve."
