# Engineering Decisions

This document records non-trivial design decisions made during the development of the assignment.

---

## Decision 1: Use retrieval before scoring instead of evaluating every facet

### Problem

The facet catalogue contains hundreds of heterogeneous facets, and the assignment requires an architecture that can conceptually scale to thousands of facets.

Sending every facet to a language model for every conversation would:

- increase latency,
- increase inference cost,
- increase the probability of unsupported scores,
- and scale poorly as the catalogue grows.

### Options considered

1. Score every facet independently.
2. Use only keyword/rule matching to select facets.
3. Use a retrieval/routing layer to select a small relevant subset before scoring.

### Decision

Use a retrieval-first architecture:

Conversation → Retrieval/Routing → Candidate Facets → Compact Scoring → Structured Output

Only a relevant subset of facets will be sent to the scoring stage.

### Trade-off

This introduces an additional retrieval component and creates a possible retrieval failure mode. However, it substantially improves scalability and reduces unnecessary model calls.

---

## Decision 2: Explicitly model non-observable facets

### Problem

The catalogue contains facets that cannot reasonably be inferred from ordinary conversation, including medical measurements, diagnoses, laboratory values, and some external or biographical facts.

A naive scorer could hallucinate values for these facets.

### Options considered

1. Allow the model to estimate every facet.
2. Remove non-observable facets from the dataset entirely.
3. Preserve all facets but explicitly mark unsuitable facets as not observable and exclude them from retrieval/scoring.

### Decision

Preserve the original facets and enrich them with:

- `facet_type`
- `conversation_observable`
- `sensitivity`
- `retrieval_enabled`
- `scoring_definition`
- `abstention_reason`

Facets requiring medical, diagnostic, laboratory, genetic, or external evidence are excluded from ordinary conversational scoring.

### Trade-off

The system scores fewer facets from a given conversation, but avoids producing unsupported or fabricated inferences.

---

## Decision 3: Use reproducible rule-based preprocessing rather than manual labelling

### Problem

The assignment dataset is heterogeneous and contains headers, malformed entries, observable traits, medical facets, psychological constructs, and external facts.

Manually labelling every row would be difficult to reproduce and would not scale.

### Options considered

1. Manually label every facet.
2. Use an LLM to label every facet.
3. Build deterministic preprocessing rules and audit the resulting categories.

### Decision

Use reproducible preprocessing rules based on normalization, keyword/phrase matching, header detection, and explicit observability/sensitivity rules.

The resulting dataset is audited using category counts and targeted inspections of sensitive and excluded facets.

### Trade-off

Rule-based classification can produce edge cases and false positives, requiring debugging and iterative refinement. However, the pipeline is reproducible and can be rerun on an updated catalogue.