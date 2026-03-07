# Protocol Requirements.md

These are working notes to be used in determining a protocol schema. They should be considered draft.

## Canonical Atomic Form

Every claim in the Context Graph — regardless of source format — reduces to a single row in a five-column table:

| `id` (URI) | `source` (URI) | `timestamp` | `key` | `value` |
|---|---|---|---|---|
| What entity is this about? | Who made this claim? | When? | What property? | What value? |

This is the irreducible canonical claim form — 6NF-like in spirit. A SHACL shape decomposes into one or more rows (one per assertion). A JSON field is one row. A CSV cell is one row. A blob reference is one row. Everything decomposes to this.

### How URIs Work

All `id` and `source` values are URIs. A URI is simply a globally unique string that identifies something. The Context Graph uses URIs because they provide identity without requiring a central registry.

**Local URIs** — generated on the fly, no global dependency needed. The standard approach is a UUID formatted as a URN:

```
urn:uuid:550e8400-e29b-41d4-a716-446655440000
```

The structure is: `urn:uuid:` (the scheme, meaning "this is a UUID-based name") followed by a UUID (a 128-bit identifier that any system can generate without coordination). Any browser, any local process, any agent can mint one instantly. Two systems generating URIs independently will never collide.

For worked examples in this document, we use shortened forms for readability:

```
urn:uuid:aaa-001     (shorthand — in production, a full UUID)
urn:system:broker-a  (identifies the source system)
urn:actor:user-jane  (identifies a human actor)
urn:system:coherence-gate  (identifies the evaluation system)
```

**Global URIs** — when an entity maps to a well-known concept, the global URI is recorded as a claim, not substituted for the local `id`:

```
https://xbrl.us/us-gaap/RevenueFromContractWithCustomer
```

**The stability rule:** Graph-local `id` values remain stable throughout the trace. Alignment to global concepts is expressed as claims — using keys such as `uri`, `same_as`, or `maps_to` — rather than by replacing the `id`. This preserves join stability and trace integrity over time.

For example, if `urn:uuid:aaa-001` is later identified as a known XBRL concept, the link is a new row in the Meaning facet:

| id | source | timestamp | key | value |
|---|---|---|---|---|
| `urn:uuid:aaa-001` | `urn:system:broker-a` | 2026-03-06T09:00:00Z | `maps_to` | `https://xbrl.us/us-gaap/RevenueNet` |

The `id` never changes. The mapping is a claim like any other.

### Why ID and Source are Separate

ID and source answer different questions:

- **ID**: What entity is this claim about? (source-local — each system's entities get their own URIs)
- **Source**: Who made this claim?

Because `id` is source-local, two systems describing their own "Revenue" column will have different `id` values (e.g., `urn:uuid:aaa-001` and `urn:uuid:bbb-001`). Cross-source comparison is not an ID match — it is an evaluation event recorded in the Context facet. The coherence gate compares claims from different entities and records whether they are aligned, misaligned, or unknown.

### How Facets Link to Each Other

The `id` column is the join key across facets for a single entity. A single entity — say, System A's "Revenue" column — gets one URI. That same URI appears in the Data, Meaning, and Structure facets:

```
Data facet:       id = urn:uuid:aaa-001  →  the actual values
Meaning facet:    id = urn:uuid:aaa-001  →  what those values refer to
Structure facet:  id = urn:uuid:aaa-001  →  what shapes those values must have
```

Same `id`, three facets, three different questions about the same entity. The join is trivial — any facet can be linked to any other by `id`.

If a user uploads multiple datasets in a conversation, each column in each dataset gets its own URI. The table a column belongs to is just another claim in the Data facet:

| id | source | timestamp | key | value |
|---|---|---|---|---|
| `urn:uuid:aaa-001` | `urn:system:broker-a` | 2026-03-06T09:00:00Z | `belongs_to` | `acme-10k-2025.xlsx` |
| `urn:uuid:aaa-001` | `urn:system:broker-a` | 2026-03-06T09:00:00Z | `column_name` | `Revenue` |

No schema change. The relationship between a column and its parent table is just another row. This scales to any number of datasets without any structural modification.

### What the Five Columns Give You

| Column | What it enables |
|---|---|
| `id` | Entity identity — join claims about the same entity across facets |
| `source` | Provenance — identify who made each claim |
| `timestamp` | Temporality — track resolution over time |
| `key` | Assertion type — classify by facet and property |
| `value` | Assertion content — the actual claim |

#### Differentiability: Measuring Differences in a Universal Primitives Format

Any of the facets can be compared because of the simplicity of their structure:

- **id**: Are we talking about the same element?
- **source**: Do we agree on "according to whom"?
- **timestamp**: How much difference in time?
- **key**: Do they match?
- **value**: Do they match?

---

## Four Facets

Every data element at a boundary decomposes into four orthogonal facets. The Data, Meaning, and Structure facets share the canonical atomic form. The Context facet is compositional.

| Facet | What it captures | Form |
|---|---|---|
| **Data** | Observed values | `id \| source \| timestamp \| key \| value` |
| **Meaning** | Semantic identity (URI, definition, or constraint set) | `id \| source \| timestamp \| key \| value` |
| **Structure** | Valid value space (type, format, range) | `id \| source \| timestamp \| key \| value` |
| **Context** | Resolution history, environment, uncertainties, intent, scores | Table of tables (same atomic form, self-referential) |

Data, Meaning, and Structure are invariant — they answer fixed questions (what values exist? what do they refer to? what shapes are valid?). The atomic form captures all three completely. Because every facet table has the same structure, comparison operators (intersection, difference, distance scoring) are universal across facets.

### Context Facet: Table of Tables

Context is the meta-facet. It records the *evaluation* of the other three facets — plus everything else needed to audit, replay, and learn from boundary events. It is a container of named sub-tables, each using the same five-column atomic form:

```
Context Graph Instance (at a boundary, at a moment)
├── Data facet       → id | source | timestamp | key | value
├── Meaning facet    → id | source | timestamp | key | value
├── Structure facet  → id | source | timestamp | key | value
└── Context facet    → table of tables (same atomic form)
    ├── environment     (timezone, locale, session metadata)
    ├── uncertainties   (ambiguities detected across facets)
    ├── intent          (questions asked/answered, sampling across the boundary)
    ├── query log       (conversation history)
    ├── tasks           (operations performed)
    └── score           (coherence measurements)
```

The "table of tables" property comes from the fact that Context claims *reference* other facet claims through the `key` column (e.g., `score.meaning.definition` points to the Meaning facet's `definition` key). It's tables all the way down, in the same format. The `source` column distinguishes who made the claim — the coherence gate, the user, an auditor — so disagreements about evaluations are handled the same way as disagreements about data.

A conversation may involve multiple datasets — multiple tables uploaded by a user, multiple sources in a global system. Each dataset gets its own Data, Meaning, and Structure tables, each identified by URI. The Context facet ties them together: it records which datasets existed at each point in the conversation, what their facet states were, what ambiguities were detected *across* them, and how they relate.

The Context facet can reference previous Context Graph states — this is the self-referential property that makes it a table of tables, not just a table. Over time, the resolution traces it produces become the persistent audit log of how understanding evolved at this boundary. (The Context Graph instance itself is ephemeral; the trace it produces is durable.)

For v1: Data, Meaning, and Structure are locked as `id | source | timestamp | key | value`. The Context facet uses the same atomic form, with sub-table names encoded in the `key` column (e.g., `score.meaning.definition`, `intent.question`, `environment.timezone`). Implementation experience from the deliverable will inform conventions for these key naming patterns.

---

## Worked Example: Two Systems, One Word — Full Trace

Two systems both have a column called **"Revenue"**. A user asks: *"Compare Company A's revenue to Company B's revenue."*

The system populates the Data, Meaning, and Structure facets for both systems, then evaluates coherence across them and records the result in the Context facet.

### Data Facet

| id | source | timestamp | key | value |
|---|---|---|---|---|
| `urn:uuid:aaa-001` | `urn:system:broker-a` | 2026-03-06T09:00:00Z | `belongs_to` | `acme-10k-2025.xlsx` |
| `urn:uuid:aaa-001` | `urn:system:broker-a` | 2026-03-06T09:00:00Z | `column_name` | `Revenue` |
| `urn:uuid:aaa-001` | `urn:system:broker-a` | 2026-03-06T09:00:00Z | `value` | `4823000` |
| `urn:uuid:bbb-001` | `urn:system:broker-b` | 2026-03-06T09:00:00Z | `belongs_to` | `globex-annual-2025.xlsx` |
| `urn:uuid:bbb-001` | `urn:system:broker-b` | 2026-03-06T09:00:00Z | `column_name` | `Revenue` |
| `urn:uuid:bbb-001` | `urn:system:broker-b` | 2026-03-06T09:00:00Z | `value` | `5200` |

### Meaning Facet

| id | source | timestamp | key | value |
|---|---|---|---|---|
| `urn:uuid:aaa-001` | `urn:system:broker-a` | 2026-03-06T09:00:00Z | `definition` | `Net revenue after returns and allowances` |
| `urn:uuid:aaa-001` | `urn:system:broker-a` | 2026-03-06T09:00:00Z | `standard` | `US-GAAP` |
| `urn:uuid:aaa-001` | `urn:system:broker-a` | 2026-03-06T09:00:00Z | `excludes` | `deferred revenue` |
| `urn:uuid:aaa-001` | `urn:system:broker-a` | 2026-03-06T09:00:00Z | `maps_to` | `https://xbrl.us/us-gaap/RevenueNet` |
| `urn:uuid:bbb-001` | `urn:system:broker-b` | 2026-03-06T09:00:00Z | `definition` | `Total revenue including deferred` |
| `urn:uuid:bbb-001` | `urn:system:broker-b` | 2026-03-06T09:00:00Z | `standard` | `IFRS` |
| `urn:uuid:bbb-001` | `urn:system:broker-b` | 2026-03-06T09:00:00Z | `excludes` | *(none)* |
| `urn:uuid:bbb-001` | `urn:system:broker-b` | 2026-03-06T09:00:00Z | `maps_to` | `https://xbrl.ifrs.org/Revenue` |

### Structure Facet

| id | source | timestamp | key | value |
|---|---|---|---|---|
| `urn:uuid:aaa-001` | `urn:system:broker-a` | 2026-03-06T09:00:00Z | `datatype` | `decimal` |
| `urn:uuid:aaa-001` | `urn:system:broker-a` | 2026-03-06T09:00:00Z | `currency` | `USD` |
| `urn:uuid:aaa-001` | `urn:system:broker-a` | 2026-03-06T09:00:00Z | `scale` | `units` |
| `urn:uuid:bbb-001` | `urn:system:broker-b` | 2026-03-06T09:00:00Z | `datatype` | `decimal` |
| `urn:uuid:bbb-001` | `urn:system:broker-b` | 2026-03-06T09:00:00Z | `currency` | `EUR` |
| `urn:uuid:bbb-001` | `urn:system:broker-b` | 2026-03-06T09:00:00Z | `scale` | `thousands` |

### Context Facet

The system now evaluates coherence across the three facets and records the result in the Context facet. Every entry below uses the same five-column atomic form. The `source` is the coherence gate, and the `key` column references which facet property was evaluated.

#### Evaluation linkage

First, the evaluation explicitly identifies which entities are being compared:

| id | source | timestamp | key | value |
|---|---|---|---|---|
| `urn:uuid:eval-001` | `urn:system:coherence-gate` | 2026-03-06T09:00:01Z | `evaluates.left` | `urn:uuid:aaa-001` |
| `urn:uuid:eval-001` | `urn:system:coherence-gate` | 2026-03-06T09:00:01Z | `evaluates.right` | `urn:uuid:bbb-001` |
| `urn:uuid:eval-001` | `urn:system:coherence-gate` | 2026-03-06T09:00:01Z | `evaluates.trigger` | `user query: Compare Company A's revenue to Company B's revenue` |

#### Score (coherence measurement)

| id | source | timestamp | key | value |
|---|---|---|---|---|
| `urn:uuid:eval-001` | `urn:system:coherence-gate` | 2026-03-06T09:00:01Z | `score.meaning.definition` | `mismatch` |
| `urn:uuid:eval-001` | `urn:system:coherence-gate` | 2026-03-06T09:00:01Z | `score.meaning.standard` | `mismatch` |
| `urn:uuid:eval-001` | `urn:system:coherence-gate` | 2026-03-06T09:00:01Z | `score.meaning.excludes` | `mismatch` |
| `urn:uuid:eval-001` | `urn:system:coherence-gate` | 2026-03-06T09:00:01Z | `score.meaning.maps_to` | `mismatch` |
| `urn:uuid:eval-001` | `urn:system:coherence-gate` | 2026-03-06T09:00:01Z | `score.structure.datatype` | `match` |
| `urn:uuid:eval-001` | `urn:system:coherence-gate` | 2026-03-06T09:00:01Z | `score.structure.currency` | `mismatch` |
| `urn:uuid:eval-001` | `urn:system:coherence-gate` | 2026-03-06T09:00:01Z | `score.structure.scale` | `mismatch` |
| `urn:uuid:eval-001` | `urn:system:coherence-gate` | 2026-03-06T09:00:01Z | `score.meaning.coherence` | `0/4` |
| `urn:uuid:eval-001` | `urn:system:coherence-gate` | 2026-03-06T09:00:01Z | `score.structure.coherence` | `1/3` |
| `urn:uuid:eval-001` | `urn:system:coherence-gate` | 2026-03-06T09:00:01Z | `verdict` | `ask` |

**Coherence: Meaning 0/4, Structure 1/3. Verdict: Ask** — the mismatch is consequential but resolvable through structured inquiry.

To see *what* differed (not just that it differed), join back to the Meaning and Structure facets by the entity `id`s in `evaluates.left` and `evaluates.right`. The score records the evaluation; the facets record the evidence.

#### Intent (sampling across the boundary)

An Intent Map for this domain specifies that when revenue definitions diverge, the system should ask the user to clarify which definition they need. The intent explicitly links back to the evaluation:

| id | source | timestamp | key | value |
|---|---|---|---|---|
| `urn:uuid:intent-001` | `urn:system:coherence-gate` | 2026-03-06T09:00:05Z | `intent.evaluation` | `urn:uuid:eval-001` |
| `urn:uuid:intent-001` | `urn:system:coherence-gate` | 2026-03-06T09:00:05Z | `intent.question` | `System A reports net revenue (GAAP, USD, excludes deferred). System B reports total revenue (IFRS, EUR, includes deferred). Which definition do you need for this comparison?` |
| `urn:uuid:intent-001` | `urn:system:coherence-gate` | 2026-03-06T09:00:05Z | `intent.option.1` | `Net revenue (GAAP)` |
| `urn:uuid:intent-001` | `urn:system:coherence-gate` | 2026-03-06T09:00:05Z | `intent.option.2` | `Total revenue (IFRS)` |
| `urn:uuid:intent-001` | `urn:system:coherence-gate` | 2026-03-06T09:00:05Z | `intent.option.3` | `Both — labeled as non-comparable` |
| `urn:uuid:intent-001` | `urn:system:coherence-gate` | 2026-03-06T09:00:05Z | `intent.resolved` | `false` |

The user responds:

| id | source | timestamp | key | value |
|---|---|---|---|---|
| `urn:uuid:intent-001` | `urn:actor:user-jane` | 2026-03-06T09:01:12Z | `intent.response` | `Both — labeled as non-comparable` |
| `urn:uuid:intent-001` | `urn:system:coherence-gate` | 2026-03-06T09:01:12Z | `intent.resolved` | `true` |

Notice: the user's response has a different `source` (`urn:actor:user-jane`) than the system's question (`urn:system:coherence-gate`). The same `id` (`urn:uuid:intent-001`) links them — they're about the same intent event. The `timestamp` shows when the resolution occurred. This is the same pattern as every other claim in the system.

#### Re-evaluation after resolution

The user chose "Both — labeled as non-comparable." The system re-evaluates:

| id | source | timestamp | key | value |
|---|---|---|---|---|
| `urn:uuid:eval-001` | `urn:system:coherence-gate` | 2026-03-06T09:01:15Z | `verdict` | `act` |
| `urn:uuid:eval-001` | `urn:system:coherence-gate` | 2026-03-06T09:01:15Z | `verdict.basis` | `User selected labeled non-comparable presentation. No semantic transformation required. Definition mismatch, currency mismatch, and scale mismatch are surfaced in output labels.` |

The verdict moves from **Ask** to **Act** — not because the definitions were reconciled, but because the user made an informed choice about how to handle the incoherence. The resolution trace records the full chain: evaluation → score → intent question → user response → re-evaluation → final verdict with basis. This trace is the persistent, auditable artifact that survives after the Context Graph instance is destroyed.

Next time *any* user or system encounters these two Revenue columns, the trace is already there — the ambiguity doesn't need to be re-discovered. It has been measured, surfaced, sampled across the boundary, and resolved.

---

## Boundary Primitives

Four primitives compose every coherence event:

| Primitive | Definition |
|---|---|
| **Claim** | An atomic assertion with provenance — one row in the canonical table |
| **Uncertainty** | A measurable gap — what is not known but could be |
| **Resolution** | How an uncertainty collapsed — which claims arrived, from where, on what basis |
| **Verdict** | Gate output: **Act** (coherence sufficient), **Ask** (uncertainty resolvable through structured inquiry), or **Halt** (incoherence is unresolvable within current scope) |

---

## SHACL's Role

SHACL is an adapter, not the framework. A SHACL shape is a bag of claims. Each SHACL shape decomposes into one or more rows of the canonical atomic table (one row per assertion). This committee defines how those rows route into the correct facet table.

---

## Deliverable

One canonical worked example, end to end, with SHACL:

1. **One financial concept** — "Revenue" from a single SEC 10-K filing (XBRL source)
2. **SHACL shape** — Kurt writes the shape for this concept
3. **Four facet tables** — The shape is decomposed into `id | source | timestamp | key | value` rows across Data, Meaning, Structure, and Context
4. **One clarifying question** — An Intent Map specifies a question that resolves the most consequential ambiguity (e.g., "net or gross?")
5. **One resolution trace** — The user answers. The system re-evaluates. The trace is complete.
6. **Auto-generated JSON Schema** — From the facet tables, one API endpoint

That's it. One concept, one document, one question, one trace. The minimal proof that SHACL decomposes into the four facets and that coherence can be measured, surfaced, and resolved at a boundary.

Once this canonical example works, we scale to additional concepts and document the adapter pattern.

### Routing taxonomy

A canonical mapping of SHACL property types to facets:

| SHACL property | Facet |
|---|---|
| `sh:datatype`, `sh:minCount`, `sh:maxCount`, `sh:pattern`, `sh:minInclusive` | **Structure** |
| `ex:calculationMethod`, `ex:reportingStandard`, `ex:excludes` | **Meaning** |
| Actor/situational prerequisites | **Context** |
| Presence/accessibility checks | **Data** (not from SHACL) |

### Adapter pattern

Once the SHACL adapter works, document the general pattern so future adapters (JSON Schema, SQL DDL, OpenAPI) can populate the same five-column tables.

---

## Principle

The spec follows the implementation. We ship working examples and let the math prove the specification. Consensus emerges from working code.

---