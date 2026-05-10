# Dimensional thinking

Two complementary [Claude Code](https://claude.com/claude-code) skills for building **multivariate intuition** in any new domain — by treating the domain as a phase space of axes, distributions, and bundles, then making both **structure** and **dynamics** visible.

**Live demo:** [dimensional-thinking.pages.dev](https://dimensional-thinking.pages.dev/) — coffee brewing as a worked example, bilingual (EN/DE).

---

## The premise

A novice sees ten coffee methods as ten things. An expert sees ten points in a multi-dimensional space — and immediately knows which axes carry information, which combinations cluster as bundles, and which inputs drive which outputs. That compressed mental model is most of what an expert carries that a novice doesn't.

These skills are a direct tool for building that model deliberately, on demand, for any new domain.

## Three core ideas

1. **Experts see axes, not items.** The space carries information, not the items. Find the variables that discriminate.
2. **Endpoints are bundles.** "Espresso" isn't just "high pressure" — it's pressure + fine grind + short time + expensive machine + ritual. The label is a category defined by joint position on many variables.
3. **Variables co-vary.** Picking a value on one axis narrows the others. Surfacing the couplings — with mechanism, not just correlation — turns the chart into a model.

## The two skills

| Skill | Mode | What it does |
|---|---|---|
| [`/dimensional-map`](skills/dimensional-map) | Structure · comparative | Maps an unfamiliar domain by finding 8–10 informative axes, applying quality gates, elaborating each pole as a bundle, and surfacing covariations with mechanism. Renders a self-contained HTML map. |
| [`/parameter-playground`](skills/parameter-playground) | Dynamics · interactive | Multislider input → outcome explorer for domains with a known causal model. Strict gate against pseudo-science. Typically called after `/dimensional-map` on the same domain. |

The map shows **where** instances sit; the playground shows **why** they sit there. Read the map first, then play with the playground.

## Install

Claude Code reads skills from `~/.claude/skills/`. To install:

```bash
git clone https://github.com/ThisIsZeitgeist/dimensional-map.git
cp -r dimensional-map/skills/* ~/.claude/skills/
```

Or symlink (keeps you on the latest pull):

```bash
git clone https://github.com/ThisIsZeitgeist/dimensional-map.git
ln -s "$(pwd)/dimensional-map/skills/dimensional-map"      ~/.claude/skills/dimensional-map
ln -s "$(pwd)/dimensional-map/skills/parameter-playground" ~/.claude/skills/parameter-playground
```

Restart Claude Code (or start a new session) and the skills should appear in the available-skills list.

## Use

In any Claude Code session, type one of:

```
/dimensional-map of <topic>
/dimensional-map of LLM training paradigms
/dimensional-map of modern web frameworks

/parameter-playground for <topic>
/parameter-playground for coffee extraction
/parameter-playground for photography exposure
```

Or trigger via natural phrasing:

- "help me understand X" → `/dimensional-map`
- "where does X fit?" → `/dimensional-map`
- "how does X compare to Y?" → `/dimensional-map`
- "how does X affect Y?" → `/parameter-playground`
- "let me play with the parameters" → `/parameter-playground`
- "what if I changed X?" → `/parameter-playground`

Each skill produces a structured analysis and (on request) a self-contained HTML artifact.

## Adapt to a new domain

The HTML templates are self-contained — no build step, no dependencies. To make a new map or playground:

1. Clone the template (`skills/dimensional-map/templates/map.html.tmpl` or `skills/parameter-playground/templates/playground.html.tmpl`)
2. Edit only the `DATA = { … }` block at the top (the worked example for coffee gives you the schema by example)
3. Open in a browser

Optional: add a `de: { ... }` block (or any other ISO code) inside the DATA for bilingual mode — the toggle and language detection are automatic when the override block is present.

## Repository layout

```
.
├── README.md                          ← this file
├── LICENSE                            ← MIT
└── skills/
    ├── dimensional-map/               ← drop into ~/.claude/skills/
    │   ├── SKILL.md                   ← spec + workflow + anti-patterns
    │   ├── references/                ← heuristics, gates, design patterns
    │   │   ├── finding-axes.md
    │   │   ├── quality-gates.md
    │   │   ├── endpoint-elaboration.md
    │   │   └── covariation-prompts.md
    │   └── templates/
    │       └── map.html.tmpl          ← self-contained HTML renderer
    └── parameter-playground/          ← drop into ~/.claude/skills/
        ├── SKILL.md
        ├── references/
        │   ├── when-not-to-use.md     ← the gate against pseudo-science
        │   └── playground-design.md
        └── templates/
            └── playground.html.tmpl
```

The live demo (coffee brewing, bilingual EN/DE) is hosted separately at [dimensional-thinking.pages.dev](https://dimensional-thinking.pages.dev/). The templates under `skills/*/templates/` produce that demo — clone a template, edit the `DATA` block at the top, open in a browser.

## Where the playground stops

`/parameter-playground` has a **strict gate**. It only ships when the domain has crisp inputs, crisp outputs, and a publicly known causal model. Coffee passes. Photography, baking, hi-fi audio, plant care, guitar tone all pass. Choosing a database, picking a programming language, evaluating literature — those *fail* the gate. For those domains, the right artifact is the dimensional map alone, because pretending to model dynamics with a weighted sum would actively teach false intuitions.

See [`skills/parameter-playground/references/when-not-to-use.md`](skills/parameter-playground/references/when-not-to-use.md) for the full gate.

## Contributing

Issues and pull requests welcome. If you build a map or playground for a new domain that's interesting, consider opening a PR with the `DATA` block as a new example in a `demo/examples/` folder.

## License

[MIT](LICENSE). The skills themselves are domain-general; the coffee example is one of many.
