# Semantic Alignment

**W3C Context Graphs Community Group — Semantic Alignment Committee**

This repository contains the Semantic Alignment Committee's working materials for mapping semantic-web formats into the Context Graph data model's canonical atomic form. The committee is the interface between the Context Graph specification and the semantic web, ontology, and knowledge graph ecosystem. If you work with OWL, RDF, SHACL, SKOS, or related standards and want to understand how existing knowledge infrastructure connects to the Context Graph, this committee is your entry point.

The committee builds open-source adapters that decompose existing representation formats (starting with SHACL) into the canonical facet tables.  This document presents the Context Graph data model's canonical form and demonstrates it through semantic-web adapter examples. It serves as both the committee's working materials and a working draft of the core specification.

|Role|Authority|
|-|-|
|Community Group Chair|Ron Itelman|
|Semantic Alignment Committee Chair|Kurt Cagle|

---

## Context Graph Specification Vocabulary

These definitions are from the Context Graph specification maintained by the W3C Community Group. They are fixed for this committee's work.

### Context (General)
Measurable incomplete and/or incoherent information between local events and global systems. Context is not noise, not metadata, and not a linguistic concept. The goal of the group is to reduce it to a formal, information-theoretic representation of the state of missing and misaligned information in communication between systems — in the Shannon sense: sender, receiver, channel, signal, and uncertainty — structured uncertainty that can be sampled, bounded, scored, and reduced through observation.

### Context (Graph)
Within a graph, a context is a specific graph configuration that forms a testable pattern for validation and rule initiation.

### Reifications
A reification is a set of assertions that are made about a given statement or ground assertion. A reification is notable in that rather than describing a specific entity, the reification describes either the relationship between two specific entities, or between an entity and its value. Reifications are significant because they are typically used to describe the event that triggered the ground assertion to be added to the context graph, in effect creating a hypergraph event log that can then be analysed as a graph post hoc. Please see [Reifications and Context Events](#reifications-and-context-events).

### Hypergraph
A hypergraph is a labeled directed cyclic graph (LDCG) that permits reifications, meaning that an assertion can be treated as a first-class resource separate from its components. Both knowledge graphs and context graphs are hypergraphs. The assumption being made here is that all references to graphs made within this working document are to hypergraphs, and refer both to reified RDF Star (RDF 1.2) based graphs and (with certain caveats) to LPG-based graphs such as those that support the OpenCypher standard, as well as any other products or open source projects that support IRI addressability, triples manipulation and reification. Knowledge Graphs prior to 2016 without reification support are grandfathered in under the term but may not have all the functionality described as part of this effort.  

### Holon
A holon is an entity that can be represented as a system. For example, a country can be represented as a collection of states, cities, biomes and so forth. Holons represent computational boundaries between systems and, as such, typically manifest as "internal" state vs an external representation of that state. Holons may be represented as named graphs, and interact via reifications on the representation of that named graph as an entity.

### Context Event
A Context Event is a process or activity that spawns a record (an assertion) and associated reification of that assertion that is published into the context graph. The event has a system-generated unique name or identifier that can be referenced by subsequent events, creating an event trail, and to the extent possible maintains temporal and spatial information about that event along with related content (who or what initiated the event, what was changed during the event, classification of the event and so forth). The event itself is ephemeral and will typically be a change of the state of the world as modelled by the graph; however, the context event follows a specific standard (the coherence protocol) and the Context Event entity within the Context Graph is the record of the event according to that protocol.

### Context Graph
An auditable log of Context events as a local representation of local and global systems boundaries, as a hypergraph. The graph itself is an append-only data structure that represents a data fabric. Context graphs may also have a processing layer that generates validation reports and a knowledge graph-type view of the processed information for easier access. Context graphs typically provide a layer of authenticated certificates to protect the underlying structure of the graph itself, and are usually not directly queryable except through internal processes via the [Context Graph Control Plane](#context-graph-control-plane).

~~A context graph assigns identity to atomic units of information related to context events using a canonical "Four Facets Model": Data, Meaning, Structure, and Context. A Context Graph is intentionally the most simple unit of information to be general to any domain and agnostic to any technology. A Context Graph instance is ephemeral — instantiated on demand at a boundary, populated during an interaction, and producing a persistent resolution trace that survives after the instance is destroyed.~~

### Knowledge Graph
A knowledge graph is a hypergraph structure focused on direct assertions and indexed analytic computations, and is typically more topically oriented than a context graph. In the CG architecture, the knowledge graph is typically computed from the context graph and is usually more publicly accessible. 

### Shapes and SHACL
A shape is a structural, rather than semantical or Classical, representation of the constraints and rules acting upon specific nodes and their contexts within a graph. The Shape Constraint Language (SHACL) is a language for the construction of and interaction with shapes within one or more graphs. it is an abstraction language that utilises the abstractions inherent in RDF 1.2, but most of the design principles involved with SHACL can be carried over with minor modification to the LPG-based languages as well in an isomorphic fashion. This proposal is written with SHACL in mind as the graph control plane.

### Constraint
A constraint is a restriction that exists upon an entity that defines what states it can and cannot be in, as represented within a graph. When an change request occurs, the change is validated, and if it passes validation is it logged (and any rules defined on it are initiated), when it fails, a report is generated, which also may initiate additional action.

### Rule
A rule generates new content upon validation of a given change in the graph, and is initiated when a state change is made relevant to a particular context within the graph. This rule will generally create additional facts, but may also initiate external services through a defined protocol.  

### Report
A report is an artefact (as a named graph) generated when a particular validation fails, indicating the reason for the failure. Reports are treated as part of the context graph. Depending upon the (configurable) severity of the invalidation, the report may be generated even when a warning or information node is validated. Reports can be queried as part of the event system.

### State Machine
A state machine within the current context is a system that can accept events (insertions from external systems, queries, modifications, and so forth) that, if validated, produce new information via rules, or generate reports that can initiate other actions if validation fails. The state machine is deliberately deterministic and mechanistic, though it can rely on external agentic systems to initiate external actions. The context system is a state machine.

### Coherence
The degree to which required meaning, structure, data, and context are sufficiently aligned for the current intent and risk tolerance at a boundary. When coherence is high, systems can act. When coherence is low, the gap between local and global state is consequential — the same term, the same data, the same structure may produce different outcomes depending on which system interprets it.

### Coherence Protocol
The specification for composing a coherence representation that may use multiple Context Graphs across system boundaries: measuring gaps and how they propagate, how resolution traces link. The Coherence Protocol is distinct from the Context Graph data model — the Context Graph describes a single boundary; the Coherence Protocol describes how boundaries compose.

### Context Graph Control Plane
A control plane is an interface layer that connects the system's state machine to external events. This manifests at several control points: at the event ingestion layer, which takes external data and formulates them as events (this may also perform routing to other control graph state machines depending upon categoriation), at the validation stage, which incorporates verifiable certificates in order to ascertain access control levels, at the rule level when new content is created, and at the report level when an invalidation error occurs. This approach is designed to scale readily, as each state machine can handle specific types of context.

### Verifiable Credentials

The context graph is not an open or public triple store. Because it can act in an agentic sense through the control plane, it needs to be governed. Among other things, this means that validation _includes_ the ability to read the intent expressed within each event, even if the message is otherwise valid. Such intent can be determined through certificates and authentication events. For instance, a bank transaction requires that a trusted session be enabled cryptographically and that it remain in force until the session expires according to the dictates of the certificates. Certificates are submitted as part of an authentication event, and either natural expire or terminate when validation fails for specific reasons. 

### Intent (Previous)

~~The actor's pragmatic purpose at a boundary. Unlike semantics, which defines terms with the purpose of shared consistency as an authority, intent is pragmatic — it reflects an individual actor's language, understanding, and purpose. Intent may be explicit (stated by the user), inferred (derived by the system from available evidence), or composed (assembled from multiple sources, including system designer intent and subsystem composition). When consequential and unresolved, intent should be sampled across the boundary and recorded in the resolution trace. Intent is the information that determines which resolution of ambiguity is correct for this actor, at this moment.~~

### Intentional Graph

Intent is the anticipation of actions a given actor will take, given certain events (priors). It is, in effect, a simulation of a context graph based upon a hypothetical context. Such a simulation can  be called an Intentional Graph. The analysis of such Bayesian priors can be added as annotational overlays, and from these analyses, the Intention Graph, partitioned into a separate forward-facing named graph, can be constructed. The intentional graph then participates in a conversation (a narrative interaction) with an external agent that provides the relevant events based upon surfaced content within the simulation. Once the simulation has terminated, the external agent may then initiate events based upon the results of the intentional graph as a merge.

One key point to understand here - the context system does not (and arguably cannot) have a concept of external agency. It is a state machine, and beyond what the system perceives through external events (epistomological or epistolic) the state machine cannot "know" in any meaningful sense, agentic intent. What it can do, however, is append to the schema constraints and rules those specific conditions and actions that cause a change in behaviour based upon specific context - when Jane is performing the actions, this is how the rules and behaviours for these relationships behave as compared to how they do so when John as agent is performing those actions. This system is reactive but evolutionary. 


### Decision Interface
A neutral contract between the Context Graph's coherence measurements and external decision models. Defines what inputs a decision model receives (the uncertainty vector, policy parameters, boundary metadata), what outputs it must return (a protocol action and optional flags), and what must be recorded in the resolution trace for auditable replay. The interface is neutral to the choice of decision framework.

---

## Reifications and Context Events



---

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

## Links

- [W3C Context Graphs Community Group](https://www.w3.org/community/context-graph/)

## License

- **Specifications and registry content** — [W3C Software and Document License (2023)](https://www.w3.org/copyright/software-license-2023/)
- **Source code** — [MIT License](https://opensource.org/licenses/MIT)
- **Contributions** — All contributions are made under the [W3C Community Contributor License Agreement (CLA)](https://www.w3.org/community/about/agreements/cla/). You must [join the Community Group](https://www.w3.org/community/context-graph/) before contributing.

See [LICENSE.md](LICENSE.md) and [CONTRIBUTING.md](CONTRIBUTING.md) for details.
