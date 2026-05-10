# Surfacing co-variations

After axes are stable and endpoints elaborated, ask these questions to find couplings between axes. Each answer becomes one card in the co-variation panel.

The job here is **not to list correlations**. It's to find the **mechanism** that *forces* the correlation. Without mechanism you have a chart, not a model. With mechanism, the user can predict where new instances will land on multiple axes from one observation.

## Question patterns to run

### 1. Range narrowing
> "If I move axis A to its right pole, what does that do to the plausible range of axis B?"

If the range of B narrows by ≥30%, you have a co-variation worth recording.

### 2. Shared constraint
> "Which axes share a single underlying constraint?"

Common shared constraints in technical fields:
- **Capex** → scale, modality breadth, oversight infrastructure, deployment shape
- **Single-node memory** → parameter scale, parallelism complexity, batch size
- **Lab structure (open vs. closed)** → openness, alignment regime, deployment, weights publication

When two axes both descend from the same constraint, the coupling is *tight* — you can't independently choose them.

### 3. Forcing function
> "Which axes have a *X requires Y* relationship?"

- Live distillation **requires** a teacher source running in parallel
- Frontier scale **precludes** single-node training (engineering, not preference)
- Multimodal **requires** vision/audio data curation

These are the strongest couplings — reality won't let you decouple them.

### 4. Conventional couplings (loose)
> "Which axes co-vary by *convention* rather than by force?"

These are the **most interesting** ones — they describe what the field *could* break apart but currently doesn't. They're the openings for unconventional designs and where novelty tends to hide.

Examples:
- Research scale ↔ text-only — by convention (multimodal data is expensive), not by physics. A small lab *could* do narrow multimodal.
- Strong inductive bias ↔ small scale — historically, but not necessarily.

If your map has 0 loose couplings, you missed the breakable conventions. Field maps need at least one.

## Card schema

```json
{
  "a": "Param scale",
  "b": "Modality breadth",
  "mechanism": "Vision/audio data curation is expensive; only labs running 100B+ training runs amortize the data work.",
  "example": "GPT-4o is omnimodal at frontier scale; Mistral-7B is text-only at research scale.",
  "strength": "tight"
}
```

### Field-by-field

- **a, b**: axis names (use `name`, not `id` — these will be displayed)
- **mechanism**: one sentence. The most important field. Must answer *why*. If you can only describe correlation, find the cause first.
- **example**: one named instance pair that illustrates the coupling. Concrete > abstract.
- **strength**: `"tight"` (forced by economics/engineering/physics — can't decouple without paying real cost) or `"loose"` (conventional — exception is plausible).

## Number of co-variations

Aim for **5-8** total. Hard cap at 10 — beyond that the user's brain can't hold the relationships. Better to ship the strongest 5 than dilute with 12.

Distribution: at least **1 loose** (conventional) and at least **3 tight** (forced) for a healthy map. All-tight is a domain map (good); all-loose is a vibes map (bad — go back to find the forced ones).

## Anti-patterns

| Symptom | What's wrong | Fix |
|---|---|---|
| Mechanism = "they correlate" | Not a mechanism | Find economic/engineering/design/regulatory cause |
| Card count >10 | Cognitive overload | Cut to ≤8 strongest |
| All cards "tight" | Missed the breakable conventions | Force-find at least 1 "loose" |
| All cards "loose" | Domain isn't actually constrained, or you missed the constraints | Look for capex / engineering forcing functions |
| Mechanism uses "tends to" / "often" / "usually" | Hand-waving | Replace with the *reason* it tends-to |
| Same axis appears in 4+ cards | Either that axis is the master variable (legitimate) or you're padding | If padding, consolidate |
