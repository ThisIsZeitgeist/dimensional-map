---
name: dimensional-map
description: Maps an unfamiliar domain — informative axes, where instances sit on each, and the bundle of co-occurring traits at each pole. Use for fast orientation, instance comparison, or locating where something sits. Self-contained HTML output. Triggers: "help me understand X", "where does X fit", "how does X compare to Y", "/dimensional-map".
trigger: /dimensional-map
---

# /dimensional-map

A learning aid for getting expert-fast in a new domain. Models how experts think: not as feature lists but as a phase space of axes, distributions, and bundles.

## When to use

- **New-domain orientation** — "I'm new to X, what are the key variables?"
- **Locate an instance** — "where does Postgres sit in the database landscape?"
- **Pairwise compare** — "espresso vs. AeroPress — what's actually different?"
- **Sanity-check claims** — "is this paper's contribution as novel as it sounds?"

## Three core ideas

This skill is built on three observations. If you skip any of them, the output reverts to a generic feature-list with no learning value.

### 1. Experts see axes, not items
A novice sees ten LLMs as ten things. An expert sees ten points in a 10-dimensional space. **The space is what carries information**, not the items.

### 2. Endpoints are bundles, not values
"Espresso" isn't just "9-bar pressure". It bundles a very fine grind, a ~30-second extraction window, an expensive precision grinder, multi-step tamping ritual, and a concentrated 1:2 yield. **The endpoint label is a category defined by joint position on many variables.** Always elaborate what's bundled at each endpoint — that's where the real teaching happens. (See `references/endpoint-elaboration.md`. This is the single highest-value step in the skill, and the most-skipped.)

### 3. Variables co-vary
Picking a value on one axis narrows the others. Surfacing the couplings — *with mechanism, not just correlation* — turns the map from a chart into a model of the domain.

## Workflow (rigid — follow exactly)

### Step 1 — Confirm scope
Three valid scopes. Pick one explicitly:
- **Locate an instance** ("Postgres in the database landscape")
- **Pairwise compare** ("espresso vs. AeroPress")
- **Field map** ("modern web frameworks")

### Step 2 — Brainstorm 12-15 candidate axes
For each candidate write:
- variable name + short name (≤14 chars, for the radar label)
- left pole short label + 2 named instances at this pole
- right pole short label + 2 named instances at this pole

If you cannot name 2 instances at each pole, **drop the candidate**. See `references/finding-axes.md` for heuristics.

### Step 3 — Apply quality gates → keep 8-10
See `references/quality-gates.md`. The three gates:
- **Discriminating**: instances spread across ≥0.5 of range (else the axis is uninformative)
- **Semantic endpoints**: never "low/high" or "weak/strong" — concrete anchors only
- **Orthogonal-ish**: not a near-duplicate of another axis (move duplicates to co-variations)

### Step 4 — Elaborate each endpoint (the bundle)
**Most-skipped, highest-value step.** For each pole of each axis, write a four-part dossier:
1. **label** — the short label (also goes on the chart)
2. **bundle** — 3-5 traits that almost always co-occur at this pole, *beyond* the variable being graphed
3. **canonical exemplar** — the instance most associated with this pole
4. **why-bundled** — one-sentence mechanism for why these traits travel together (economic, engineering forcing function, design coupling, regulatory, path-dependent)

See `references/endpoint-elaboration.md` for patterns and a worked example.

### Step 5 — Place 5-7 exemplars on each axis
Use `[0,1]` floats. Mark confidence per position:
- `*` measured / publicly documented
- `~` estimated from indirect evidence
- `?` guess (cap total guesses at 20% of all positions)

Always include the target instance plus 4-6 alternatives that span the field's range — including at least one at each pole.

### Step 6 — Surface 5-8 co-variations
Each entry: `{ a, b, mechanism, example, strength }`. Mechanism is the load-bearing field — without it you have correlation theatre. See `references/covariation-prompts.md`.

### Step 7 — Render
Two artifacts:
1. **Structured analysis** (markdown) — axes table, exemplars, endpoint dossiers, co-variations, confidence summary, list of axes considered-and-dropped (so the user sees what you cut)
2. **HTML visualization** (on user request, or when they explicitly want to see the map) — clone `templates/map.html.tmpl` to a new path, then edit only the `DATA = { ... }` block at the top with the mapped data. The template's JS does the rest. Save next to the source file if there is one (e.g. next to a paper, project doc), otherwise in `~/dimensional-maps/<slug>.html`.

### Step 8 — Honest reporting
- Total axes considered vs. kept (e.g., "13 considered, 9 kept, 4 dropped: <reasons>")
- % of positions guessed (per axis, plus overall)
- Endpoints where the bundle is uncertain — mark them
- If <6 axes survive the gates, ship 6 — don't pad. A small honest map beats a padded one.

## Anti-patterns (red flags)

| Symptom | What it means | Action |
|---|---|---|
| All instances within ±0.1 on an axis | Axis isn't informative | Drop |
| Endpoints labelled "low/high", "weak/strong", "less/more" | Lazy labelling | Replace with semantic anchors |
| 0 co-variations surfaced | You haven't understood the domain | Restart from step 2 |
| 12+ axes shipped | Signal-to-noise crashing | Cut to ≤10 |
| Endpoint elaboration = "more X" | Missed the point of the skill | Re-do step 4 |
| Target instance at every extreme | Either map is biased OR target really is unusual | Add a sanity-check axis where target should be unremarkable |
| Co-variations without mechanism | Correlation theatre | Demand the *why* for each one |
| Bundle has 6+ traits | Padding | Cap at 5 — pick the load-bearing ones |
| No canonical exemplar at a pole | You don't know that pole well enough | Research more before mapping |
| Filler qualifiers in copy ("named methods", "various aspects", "specific examples", "different types") | Trivialities that pad without informing — if the names/items appear right after, the qualifier is dead weight | Strike them. Write "5 methods (espresso, AeroPress, ...)" not "5 named methods (...)". The reader sees the names; the qualifier doesn't earn its place. |

## No-filler rule

Don't add adjectives, qualifiers, or scene-setting phrases that don't carry information. **Audit every adjective**: if removing it loses no meaning, remove it. Common offenders:

- "named" / "specific" / "various" / "different" — when the items follow immediately
- "important" / "critical" / "key" — when the reader is already reading because it's important
- "the world of X" / "the realm of X" / "in the field of X" — replace with just X
- "various aspects of" / "different facets of" — replace with the actual aspects/facets
- "carefully" / "thoughtfully" / "deliberately" — adverbs that signal effort rather than describing it

The bar: every word should change the meaning if removed. Filler fails this test.

## Output format (intermediate JSON)

The HTML template consumes this exact shape. Produce it as the deliverable from steps 1-6, then splice into the template at step 7.

```json
{
  "meta": {
    "title": "Coffee brewing methods — a dimensional map",
    "pageTitle": "Coffee brewing methods — a dimensional map",
    "headerSub": "A dimensional map of...",
    "metaCallout": "How to read this...",
    "legendLabel": "Compare against:",
    "footer": "Caveats..."
  },
  "models": [
    { "key": "espresso", "name": "Espresso", "full": "Espresso (9-bar machine)",
      "color": "#f97583", "isTarget": true }
  ],
  "axes": [
    {
      "id": "pressure",
      "name": "Brewing pressure",
      "short": "Pressure",
      "kind": "Mechanism",
      "blurb": "What drives water through the grounds — gravity, immersion, or pump pressure.",
      "leftPole": {
        "label": "Atmospheric (gravity / immersion)",
        "bundle": ["gravity or immersion drives extraction", "coarse-to-medium grind",
                   "forgiving to user technique", "simple equipment", "long contact tolerated"],
        "exemplar": "French press",
        "why": "Without pressure only diffusion drives extraction, so time substitutes for force; coarse grind avoids over-extraction at long times; cheap mechanism."
      },
      "rightPole": {
        "label": "9 bar (espresso pressure)",
        "bundle": ["very fine grind required", "~30s extraction window",
                   "expensive pump-driven machine", "precise dosing critical",
                   "concentrated 1:2 yield"],
        "exemplar": "Espresso",
        "why": "Design coupling — high pressure forces fine grind to slow flow; fine grind demands precise dose and temperature; the whole stack is needed to brew at all."
      },
      "densityPeaks": [{"at":0.10,"w":0.18,"h":0.85}, {"at":0.95,"w":0.10,"h":0.55}],
      "pos":        { "espresso": 1.0, "frenchpress": 0.0,  "v60": 0.0 },
      "confidence": { "espresso": "*", "frenchpress": "*", "v60": "*" },
      "foot": "Espresso defines the right pole — 9 bar is what makes it espresso, and what justifies the rest of the bundle."
    }
  ],
  "covariations": [
    {
      "a": "Pressure",
      "b": "Grind size",
      "mechanism": "Hydraulic balance — pressure-brewed methods need fine grind to slow flow; gravity methods need coarse grind to allow flow at all.",
      "example": "Espresso (9 bar, talcum-fine) vs. French press (atmospheric, sea-salt-coarse).",
      "strength": "tight"
    }
  ]
}
```

## Bilingual mode (optional)

The template supports an optional second language. Add a `de: { ... }` block (or other ISO code) inside DATA with the same shape as the top-level — `meta`, `models`, `axes`, `covariations`. The renderer merges per-field, so partial translations work (missing keys fall back to canonical). Position values, density peaks, confidence marks, model keys/colours stay canonical (top-level only) — only translatable strings go in the override.

A language toggle (EN/DE pills, top-right) is rendered automatically when `DATA.de` is present. It writes a `dt_lang_pref` cookie and re-renders without reload. Initial language is picked from the cookie, then `navigator.languages` (German wins if `de*` is preferred), else the canonical default.

When generating a map for a German-speaking user, default to populating `DATA.de` alongside the canonical block — same shape, fully translated, no IP/proper-noun changes (model keys, colours, IDs identical).

## References
- `references/finding-axes.md` — heuristics for spotting informative axes
- `references/quality-gates.md` — the three gates with examples of failed axes
- `references/endpoint-elaboration.md` — how to write endpoint dossiers (the bundle, exemplar, why)
- `references/covariation-prompts.md` — patterns for surfacing couplings with mechanism
- `templates/map.html.tmpl` — self-contained HTML renderer (just edit the `DATA = {…}` block)

## Companion skill: parameter-playground

`/dimensional-map` shows **structure** — where instances sit in the design space. For domains where the input → outcome dynamics are *also* knowable (and pass the gate), `/parameter-playground` is the natural follow-up: a multislider explorer of how controllable variables shape observable outcomes, with presets that reproduce the map's exemplars.

Typical pairing:
1. User asks to understand a new domain → `/dimensional-map`
2. User wants to feel the dynamics → `/parameter-playground` on the same domain
3. The playground's presets reproduce the map's exemplars; clicking through them shows that named instances are *bundles of input choices*, complementing the map's endpoint dossiers (which show they're bundles of co-occurring traits).

`/parameter-playground` has a strict gate — only ship it when the domain has crisp inputs, crisp outputs, and a publicly known causal model. See that skill's `references/when-not-to-use.md` for the full gate.
