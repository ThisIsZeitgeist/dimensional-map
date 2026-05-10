# Finding informative axes

The hardest step in building a dimensional map is finding the *right* axes. A bad axis is one that everybody agrees is "important" but doesn't actually discriminate between instances. A good axis is one that experts in the field implicitly use to *categorize* what they encounter.

## Heuristics — where good axes hide

### 1. The practitioner's hands
What knobs do practitioners actually turn when configuring a thing? Those knobs are axes. If "you decide X" appears in tutorials, design docs, or config files, X is an axis.
- Web frameworks: rendering strategy (SSR / CSR / SSG / streaming), routing model (file-based vs. config-based), state management coupling.
- LLMs: parameter count, context length, tokenizer choice, RLHF intensity.

### 2. The trade-off literature
Where do papers, blog posts, or reviews explicitly discuss trade-offs? Each named trade-off is an axis.
- "We chose dense over MoE because…" → routing topology axis.
- "We sacrificed throughput for memory" → engineering trade-off axis.

### 3. Divergence points between alternatives
Where do two well-known alternatives differ most? That's an axis. Where they agree isn't.
- DeepSeek-R1 vs. Mistral-7B differ most on inference-time compute scaling and reasoning architecture, not on tokenizer or attention type.

### 4. The hardcoded ↔ learned spectrum
Almost every engineering domain has axes of *how much structure is built in vs. discovered*.
- ML: hand-engineered features vs. learned representations
- Compilers: handwritten passes vs. ML-driven optimization
- Robotics: classical control vs. learned policies
- Coffee brewing: pressure-and-grind dialed by hand vs. set-and-forget

### 5. Cost shape
Fixed-cost vs. variable-cost is almost always informative. So is one-shot-cost vs. recurring-cost.
- Inference: fixed-cost forward pass vs. test-time-scaled deliberation
- Storage: read-mostly vs. write-mostly

### 6. Time and place coupling
*When* something happens (training-time / inference-time / post-hoc) and *where* (single node / distributed / federated) are usually informative axes.
- Alignment: post-hoc RLHF vs. live oversight (when)
- Training: single-node vs. 3D-parallel mesh (where)

### 7. Regulatory / constraint pressure
Closed-vs-open, licensed-vs-permissive, compliant-vs-unregulated. These often *cause* clustering on other axes (closed → no public weights → API-only deployment).

### 8. Social structure of the field
Solo-vs-team, academic-vs-industrial, hobbyist-vs-professional. These usually get smuggled in via other axes (parameter scale collapses to "industrial only" above 100B), but sometimes deserve their own axis when the social structure is the *point*.

## Bad axes (drop these)

| Bad axis | Why | What to do instead |
|---|---|---|
| "Quality" | Compresses 10 things into one judgment | Decompose into the actual sub-judgments |
| "Complexity" | Vague unless you specify *of what* | "Topology complexity" / "API surface complexity" / "Operational complexity" |
| "Difficulty" | Wrong level — difficulty is a property of *use*, not of the thing | Use what makes it difficult instead |
| "Performance" | Depends entirely on benchmark | If the benchmark is the comparison, name it; otherwise drop |
| "Modernity" | Time, not a real axis (and recency ≠ better) | Look at *what changed* recently — that's the axis |
| "Popularity" | Outcome, not a design choice | Drop unless mapping field dynamics specifically |

## Test your candidate axes

Before adding an axis to the keep pile, ask:

1. **Can I name 2 instances at each pole?** If no, drop.
2. **Do those instances differ on this axis specifically, not on a downstream consequence?** ("GPT-4 is better than GPT-3" isn't an axis; "GPT-4 is multimodal, GPT-3 is text-only" is.)
3. **Would removing this axis change my mental model of the domain?** If no, it's just a feature, not an axis.
4. **Can I imagine an instance at each pole that doesn't yet exist?** This tests whether the axis is *generative* — good axes describe a space of possibilities, not just an enumeration of known points.
