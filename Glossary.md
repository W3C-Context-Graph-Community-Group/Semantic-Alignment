# Context Graph Specification Glossary

> **Status: Working Draft — v0.2**
>
> This glossary is a living document maintained by the
> [W3C Context Graph Community Group](https://www.w3.org/community/context-graph/).
> It is intended as a starting point for discussion, not a finalised specification.
> Definitions marked **\[OPEN\]** have unresolved questions that the group should
> address before stabilisation. Definitions marked **\[PLACEHOLDER\]** are
> referenced in other entries but require fuller treatment in a subsequent draft.
>
> Feedback and proposed edits should be submitted as GitHub issues or pull requests
> against the [Semantic-Alignment repository](https://github.com/W3C-Context-Graph-Community-Group/Semantic-Alignment).

---

## How to Read This Glossary

Entries are grouped into three layers, reflecting their role in the architecture:

- **Foundational concepts** — the epistemological and information-theoretic primitives on which the architecture rests.
- **Structural components** — named artefacts, data structures, and mechanisms in the Context Graph system.
- **Operational concepts** — runtime behaviours, protocols, and interfaces.

Within each section, entries are ordered alphabetically. Cross-references to other glossary entries are written in **bold**.

---

## Part 1: Foundational Concepts


### Ask (Protocol Action)
 
*Type: Operational concept*
 
Ask is one of three protocol actions (along with **Halt** and **Act**). It is triggered when uncertainty is measurable and reducible — a divergence has been identified and can be narrowed through further exchange.
 
Ask is a measurement in the formal sense: it acquires information that cannot be computed from the data the receiver already holds. This is the distinction between inference (reducing uncertainty from data you have) and measurement (acquiring data you cannot compute). The answer to an Ask is not in the received data — it is in the sender's **codebook**, on the other side of the **boundary**.
 
Every Ask produces **Context** — a resolution record that becomes part of the protocol's working memory. This new Context may reveal further incompleteness, requiring further Asks. This is the recursive property: Context is the protocol's working memory, and each resolution may reshape what remains to be resolved.
 
Ask may take several forms, operating as a natural resolution cascade:
 
- **Level 1 — String equality:** $O(1)$, catches exact matches and obvious mismatches.
- **Level 2 — URI dereferencing:** follow the link to a documentation page, compare tagged definitions.
- **Level 3 — Semantic evaluation:** human, AI, or domain-expert judgment.
 
Each level is more expensive and acquires more information. The protocol does not prescribe which level to use — it prescribes the dependency ordering and records every resolution as Context.
 
> **\[NOTE\]** The existing charter entry for Protocol Actions lists Act, Ask, and
> Halt. This entry provides the formal definition of Ask as measurement, which
> is the core operation that distinguishes the coherence protocol from static
> validation systems.
 
---

### Assertion

*Type: Foundational concept*

An assertion is a statement of belief made by a specific authority. It is the foundational unit of the Semantic Web and of RDF-based graph representations.

Assertions are epistemological rather than strictly factual: an assertion is not required to be an accurate reflection of the world, only a reflection of the truth as held by the asserting authority at the time of assertion. This is a deliberate design property. Because the provenance and authority of an assertion are as important as its content, assertions in this architecture are always accompanied by **reification** metadata that captures who made the assertion, when, and under what conditions.

A set of assertions constitutes a **graph**. Throughout this document, all references to graphs should be understood as references to **hypergraphs** unless otherwise stated.

> **\[OPEN\]** The relationship between assertions in this model and the W3C RDF 1.2 triple model should be made explicit, particularly with respect to blank nodes, named graphs, and the annotation syntax introduced in RDF 1.2.

---

### Codebook
 
*Type: Foundational concept*
 
A codebook is the complete mapping a system maintains between its symbols and their referents — not merely a lookup table, but the full set of assumptions under which the system interprets data. This includes the semantic definitions it assigns to field names, the structural conventions it applies to formats, and the contextual prerequisites it assumes but may not externalize.
 
Every framework that minimizes uncertainty — Shannon's channel coding, active inference, Bayesian decision theory — presupposes that sender and receiver share a codebook. When this assumption is not verified, the receiver may interpret data using a different codebook than the sender intended, with no signal of any kind indicating that ambiguity exists.
 
The coherence protocol decomposes a codebook into three **comparison facets** (Data, Meaning, Structure) and a **resolution layer** (Context). The **canonical claim form** is the surface onto which any system can project its codebook for comparison.

### Coherence

*Type: Foundational concept*

Coherence is the state achieved when all necessary Context has been surfaced such that comparisons on Meaning, Structure, and Data are well-defined and have been evaluated. Two systems are coherent when the Context layer contains sufficient resolutions that no remaining ambiguity in Meaning, Structure, or Data is invisible to either party.
Coherence is not a pre-existing condition discovered by inspection — it is the product of a measurement process. Two systems with identical codebooks that have not verified their alignment are not coherent in this sense. The protocol for making invisible context mutual and surfacable in an organized sequence is what produces coherence — this is why we call it a coherence protocol.
When coherence is high, systems can act on shared information with confidence. When coherence is absent, the gap between systems is consequential — and, critically, may be invisible to both parties. This condition is distinct from data quality problems and schema errors; it is a structural property of boundaries between systems that operate under different assumptions.

[NOTE] The key change: coherence is defined as an achieved state (the product of measurement), not a pre-existing degree (a score to be discovered).
This has implications for the [OPEN] question about a formal scoring model: the score is not "how coherent are we" but "how complete is the Context layer" — i.e., how many required resolutions have been recorded vs. how many remain open. The scoring model follows from the resolution layer, not from an independent metric.
---


### Comparison Facet
 
*Type: Foundational concept*
 
A comparison facet is one of the three dimensions of a **codebook** that can be directly compared across systems: **Data**, **Meaning**, and **Structure**. Each comparison facet contributes up to 1 bit of measurable boundary uncertainty (match or mismatch).
 
Comparison facets are distinguished from the **resolution layer** (Context). A comparison on Meaning or Structure is only valid when Context has been sufficiently accumulated. A comparison on Data is only interpretable when Meaning and Structure are aligned. This dependency ordering is the structural principle of the **Four Facets Model**.

---


### Canonical Claim Form
 
*Type: Structural component*
 
The canonical claim form is the irreducible five-column substrate onto which any system can project its **codebook** at the moment of contact, requiring no prior agreement on terms. A claim is a tuple:
 
| Column | Question it answers |
|--------|-------------------|
| **id** | What entity is this about? |
| **source** | Who makes this claim? |
| **timestamp** | When? |
| **key** | What property? |
| **value** | What assertion? |
 
Every assertion from every system, regardless of source format, reduces to a single row. A JSON field is one row. A CSV cell is one row. A SHACL shape decomposes into one row per assertion. The facet is encoded in the URN namespace of the key (`urn:data:`, `urn:meaning:`, `urn:structure:`, `urn:context:`). The five columns are fixed. The URN namespace is open.
 
The canonical form is not a fixed schema. Neither side must adopt the other's representation. Each system projects its own codebook onto the five-column surface at the moment of contact. The surface assembles on demand — it is *liquid*, taking the shape of the boundary rather than imposing a shape on it. This is the property that distinguishes the canonical form from a shared ontology: an ontology requires prior agreement on terms; the canonical form requires only that both sides can project onto the same surface.
 
> **\[NOTE\]** The canonical claim form is referenced in the charter (Deliverable 3:
> "The Coherence Protocol requires all boundary inputs to conform to the canonical
> claim form (id, source, timestamp, key, value) as specified by the Syntax &
> Serialization committee.") This entry provides the formal definition.

---

### Context

*Type: Foundational concept*

Context is the resolution layer for any incompleteness in any other facet. When Meaning is ambiguous, the resolution is Context. When Structure is underspecified, the resolution is Context. When neither Meaning nor Structure can be evaluated because reference frames have not been established, the prerequisite information is Context.
Context is not a fourth kind of information alongside Data, Meaning, and Structure — it is the structured record of what was missing, what was asked, what was resolved, and what remains open across all three. It is asymmetric with respect to the other facets: Data, Meaning, and Structure describe what is being communicated; Context accumulates the resolutions required to interpret them. A match on Meaning or Structure without surfaced Context is unverifiable — not because Context is a separate dimension that also needs matching, but because the comparisons on Meaning and Structure are not yet well-defined.
Context is the only facet with a recursive property. Each round of the protocol produces new Context that may reveal further incompleteness in another facet. The accumulating structure of these dependencies forms a context graph — the state space the protocol traverses on its way to coherence or halt.

[NOTE] This revision addresses the [OPEN] question in the current entry:
"Are the information-theoretic sense (boundary gap) and the structural sense (graph pattern) the same concept at different levels of abstraction, or two distinct concepts that share a name?" Under this framing, they are the same concept: the graph pattern is the accumulated record of resolutions to the boundary gap. The gap is what is missing. The pattern is what was surfaced to fill it. Context is the layer that connects them.

---

### Four Facets Model

*Type: Foundational concept*

The Context Graph architecture decomposes every codebook into three **comparison facets** and one **resolution layer**:
 
| Facet | Type | Description |
|-------|------|-------------|
| **Data** | Comparison facet | The raw informational content — what Shannon delivers. |
| **Meaning** | Comparison facet | The semantic interpretation — what the value refers to within a given vocabulary or ontology. |
| **Structure** | Comparison facet | The schema, constraints, and encoding rules — how the value is formatted and how it relates to others. |
| **Context** | Resolution layer | The accumulated resolutions of incompleteness in the other three — what was missing, what was asked, what was resolved. |
 
The facets are not orthogonal. They obey a dependency ordering:
 
**Context → Meaning → Structure → Data**
 
Data comparison is semantically void unless Meaning and Structure are aligned. Meaning and Structure comparisons are unverifiable unless Context has accumulated sufficient resolutions to make them well-defined. Context is not matched across systems for equality — it is checked for sufficiency. Each comparison facet contributes up to 1 bit of measurable boundary uncertainty. Context may require an arbitrary number of resolution steps, depending on the boundary.
 
> **\[NOTE\]** This revision reframes the four facets from parallel dimensions to
> a dependency-ordered structure with an asymmetric resolution layer. The
> \[OPEN\] question about mapping to FAIR principles, ISO 5087, etc. still
> applies and should be addressed once the group agrees on the asymmetry.

---

### Holon

*Type: Foundational concept*

A holon is an entity that is simultaneously a whole (with its own internal structure) and a part of a larger system. The term is used here in the computational sense: a holon is a system with a defined boundary that encapsulates internal state and exposes an external representation of that state to other systems.

In the Context Graph architecture, holons define the boundaries at which coherence is measured. A country can be a holon composed of states; a microservice can be a holon within a larger application; a bank branch can be a holon within a banking system. Holons may be represented as named graphs and interact with one another through **reifications** on the named graph that represents them as an entity.

The nesting of holons is recursive: a holon may contain other holons to any depth, and the **Coherence Protocol** governs how coherence measurements compose across this hierarchy.

> **\[NOTE\]** The term *holon* originates with Arthur Koestler (*The Ghost in the Machine*, 1967) and has prior use in philosophy of mind and systems theory. Its adoption here is for precision, not provocation — specifically, the dual whole/part property has no clean equivalent in standard graph vocabulary. Alternatives considered included *scope*, *system boundary*, and *named graph boundary*; none captured the recursive compositional property adequately.

---


### Ignorance of Incoherence
 
*Type: Foundational concept*
 
Ignorance of incoherence is the state in which a system acts on ambiguous information with full confidence because no instrument it possesses can distinguish the ambiguity from agreement.
 
**Semantic superposition** describes the state of the information — multiple internally consistent interpretations exist. Ignorance of incoherence describes the state of the system — it does not know it is in superposition. It silently collapses the ambiguity using its own defaults (its own timezone, its own definitions, its own reference frame) and proceeds with zero uncertainty about an interpretation that may be wrong.
 
This is the condition the coherence protocol is built to detect: not incoherence itself, but the absence of any instrument to detect incoherence. Shannon certifies the transmission as perfect. Active inference reaches minimum free energy. Prediction error is zero. The answer is wrong.
---


### Liquid Coherence
 
*Type: Foundational concept*
 
Liquid Coherence is the architectural property in which the protocol produces its own ontology at each boundary, on the same substrate it operates on. The contract between systems is not pre-signed — it assembles through the protocol and is expressed in the same form as the protocol's own state.
 
This distinguishes Liquid Coherence from rigid coherence (a pre-agreed ontology or schema that both sides must adopt before interaction). In a rigid system, the representation layer, the measurement instrument, and the translation layer are separate components. In a liquid system, the **canonical claim form** is simultaneously the storage layer, the projection surface, and the comparison basis. The projection carries all the information needed for comparison. There is nothing left for a separate instrument to do.
 
The term reflects the holonic property of the architecture: every layer is both the product and the instrument. The gauge outputs are claims. The resolution trace is claims. A trained model's weights are claims. The substrate is the same at every level.
 
---

### Resolution Layer
 
*Type: Foundational concept*
 
The resolution layer is the accumulating record of what was missing, what was asked, and what was resolved across the three **comparison facets** (Data, Meaning, Structure). In the **Four Facets Model**, **Context** functions as the resolution layer.
 
The resolution layer is not a fourth comparison axis. It is the protocol's working memory: the structured trace of every incompleteness discovered and every question answered in the course of achieving **coherence**. Every **Ask** in the protocol produces a resolution record. Each resolution may reveal further incompleteness, requiring further Asks. The protocol terminates when no further incompleteness is visible to either party. That is the coherence condition.
 
The resolution layer is recursive: each round of the protocol produces new Context that determines what the next round must address. Data, Meaning, and Structure can be updated through the protocol, but only Context tracks the state of the protocol itself.
---

### Semantic Superposition
 
*Type: Foundational concept*
 
Semantic superposition is the state in which received data admits multiple internally consistent interpretations, with no information in the received signal to distinguish them.
 
Example: the string `03/01/2026` under the label `time` is in semantic superposition. It may mean March 1st or January 3rd (structural ambiguity). It may refer to order placement time or order receipt time (meaning ambiguity). It may have been produced in Singapore, London, or New York (contextual ambiguity). Shannon's channel delivered the string perfectly. Every bit is correct. The receiver has zero uncertainty about the bits and potentially complete uncertainty about the referent.
 
Semantic superposition is collapsed — not by assumption, but by measurement — when the coherence protocol accumulates sufficient **Context** to ground comparisons on Meaning, Structure, and Data. Each **Ask** in the protocol eliminates interpretations that no amount of inference on the received data could have ruled out.
---

### Signal

*Type: Foundational concept*

**Conceptual definition:** A signal is the information-theoretic unit of communication between systems. It is measurable, potentially incomplete, and potentially incoherent with respect to the receiving system's model. The goal of the Context Graph architecture is to treat the uncertainty carried by a signal not as an error condition but as a first-class, structured artefact that can be sampled, bounded, scored, and reduced through observation.

**Operational definition:** Within the system, a signal is an encoded event produced by an external environment and submitted for ingestion by the **Context Graph Control Plane**. Before it can become part of the context graph, a signal must pass three sequential gates:

1. **Authentication** — the signal's origin is verified against the active **verifiable credential** session.
2. **Well-formedness check** — the signal conforms to the expected encoding format and schema.
3. **Validation** — the signal's content satisfies the applicable **constraints** and **shapes**.

A signal that passes all three gates generates a **context event**. A signal that fails at any gate generates a **report** and does not modify the graph state.

---

## Part 2: Structural Components

### Boundary

*Type: Structural component* **\[PLACEHOLDER\]**

A boundary is the interface between two **holons** across which **signals** are exchanged and **coherence** is measured. Boundaries are the primary sites of contextual misalignment in the architecture: terms, policies, and data structures that are well-defined within a holon may be ambiguous, absent, or contradictory when they cross a boundary into a different holon's interpretive context.

> **\[OPEN\]** The group should specify whether a boundary is itself a named graph, a set of constraints, a trust domain definition, or some combination of these. The relationship between boundaries and W3C ODRL policies, and between boundaries and verifiable credential scopes, should also be addressed.

---

### Context Event

*Type: Structural component*

A context event is the record created when a validated **signal** modifies the state of the **context graph**. It is the primary unit of the graph's append-only event log.

Each context event:

- Is assigned a system-generated unique identifier that is stable and referenceable by subsequent events, forming an auditable **event trail**.
- Carries temporal and spatial metadata about the moment and location of the event.
- Records provenance metadata: who or what initiated the event, what state change was made, and how the event is classified.
- Is published as an **assertion** with an accompanying **reification** according to the **Coherence Protocol**.

The underlying activity that triggered the event is ephemeral — the event has already occurred by the time it is recorded. The context event entity within the Context Graph is the durable record of that activity, not the activity itself.

---

### Context Graph

*Type: Structural component*

A context graph is an auditable, append-only event log representing the state of **coherence** at one or more **system boundaries**, structured as a **hypergraph**.

A context graph instance has the following properties:

- **Append-only:** Graph state accumulates through event accretion. Prior state is never overwritten, enabling full temporal replay.
- **Authenticated:** All modifications are gated through the **Context Graph Control Plane** using **verifiable credentials**. The graph is not a public or open triple store.
- **Ephemeral instances, persistent traces:** A context graph instance is created on demand at a boundary interaction, populated during that interaction, and then dissolved — but it produces a **resolution trace** that persists and is queryable after the instance terminates.
- **Structured by the Four Facets Model:** Every recorded unit of information is characterised across the **Data, Meaning, Structure, and Context** facets.
- **Layered output:** A context graph may expose a **knowledge graph** layer — a computed, more accessible view of the processed information — while keeping the raw event log under access control.

> **\[OPEN\]** The statement that context graphs are "not directly queryable except through internal processes" implies no external SPARQL endpoint. The group should make an explicit architectural decision about queryability, access control tiers, and the relationship between the raw event log and the knowledge graph view. This will be a significant point of discussion for participants from the SPARQL community.

---

### Hypergraph

*Type: Structural component*

A hypergraph, as used in this specification, is a labeled directed cyclic graph (LDCG) that supports **reification** — the capacity to treat an **assertion** as a first-class addressable resource, independent of its subject and object components. This property enables the event log architecture of the **context graph**.

> **\[NOTE\]** The term *cyclic* is used deliberately. Most graph theoretic treatments regard directed cycles as pathological; in the append-only event model, cycles arise legitimately through temporal reference chains (an event may reference a prior event that references an earlier event in the same logical chain). The architecture does not prevent cycles; it manages them through temporal ordering in the event log.

Both knowledge graphs and context graphs are hypergraphs in this sense. This specification is written primarily against **RDF 1.2** (and specifically the annotation syntax that supports reification as first-class syntax), but the structural principles apply with minor adaptation to LPG-based systems such as those supporting the OpenCypher standard, provided those systems support IRI addressability, triple manipulation, and reification. Knowledge graphs that predate 2016 and lack reification support are grandfathered under the term for backward compatibility, but may not support all functionality described in this specification.

---

### Intentional Graph

*Type: Structural component* **\[OPEN\]**

An intentional graph is a forward-facing simulation of a **context graph** constructed from **Bayesian priors** — that is, from inferences about an actor's likely future actions given observed past behaviour and current context. It represents the *anticipated* context graph that would result from a hypothetical sequence of events, rather than the recorded context graph produced by events that have already occurred.

Intentional graphs are partitioned into a separate named graph and participate in a structured conversation (a *narrative interaction*) with an external agent. The external agent provides events based on content surfaced during the simulation; once the simulation terminates, the results may be merged back as real events into the base context graph.

> **\[OPEN\]** The Bayesian framing introduces concepts from probability theory that are not yet formally defined elsewhere in this specification. The group should determine whether the intentional graph is a first-class component of the core specification or an extension, and if the former, define the formal model for prior construction, simulation termination, and merge semantics. The relationship to LLM-based inference and agentic AI workflows should be addressed here, as it is likely to be a significant area of member interest.

---

### Knowledge Graph

*Type: Structural component*

A knowledge graph is a **hypergraph** structure focused on direct assertions and indexed analytic computations. In the Context Graph architecture, the knowledge graph is typically computed from the **context graph** and represents a more accessible, queryable view of processed information. It is generally more topically oriented and more publicly accessible than the underlying context graph.

The distinction between the context graph and the knowledge graph reflects a separation of concerns: the context graph is a faithful, append-only record of events and their provenance; the knowledge graph is a derived, optimised representation for consumption by downstream systems and queries.

---

### Reification

*Type: Structural component*

**Semantic definition:** A reification is a set of assertions made about a given statement, rather than about the entities the statement describes. Where a ground assertion says "A relates to B", a reification says "the assertion that A relates to B has the following properties." This allows an assertion — a relationship or an entity–value binding — to itself be the subject of further description.

**Role in this architecture:** Reifications are the mechanism by which the context graph records the event that caused a ground assertion to be added to the graph. Every ground assertion has an associated reification that captures the **context event** responsible for its creation. This transforms the graph from a static collection of facts into a hypergraph event log that records not just what is true but *why* and *when* it came to be true, enabling post-hoc analysis of the event trail.

In RDF 1.2 terms, reification is supported natively through the annotation (`{| |}`) syntax. Equivalent mechanisms exist in LPG systems, though with varying expressivity.

See also: **Context Event**, **Hypergraph**.

---

### Report

*Type: Structural component*

A report is an artefact generated when **validation** of a **signal** or **context event** fails. Reports are published as named graphs and are themselves part of the **context graph** — they are not discarded or logged externally. This ensures that failures are first-class citizens of the event trail and are subject to the same provenance, queryability, and audit guarantees as successful events.

Reports may be generated at multiple severity levels (error, warning, informational), with severity configured per constraint. A report may itself initiate further system actions through the **Context Graph Control Plane**, for example by triggering a notification, escalating to a human decision point, or requesting additional **verifiable credentials**.

---

### Resolution Trace

*Type: Structural component* **\[PLACEHOLDER\]**

A resolution trace is the persistent artefact produced by a **context graph** instance after that instance terminates. It records the full history of how contextual misalignments at a **boundary** were identified, measured, and resolved (or left unresolved) during the lifetime of the instance. Resolution traces are the primary artefact for auditable replay and post-hoc analysis.

> **\[OPEN\]** The group should define the formal structure of a resolution trace: what fields are mandatory, what the serialisation format is, how traces compose across nested **holons**, and how they are linked in the **Coherence Protocol**. The resolution trace is referenced in multiple entries (Coherence Protocol, Intent, Decision Interface) and must be fully specified before those entries can be considered normative.

---

## Part 3: Operational Concepts

### Agency

*Type: Operational concept*

The context system is a deterministic **state machine**. It does not model external agency directly — it cannot, in any meaningful sense, infer the autonomous intent of an external actor from the events it receives. What it perceives of the outside world is limited to authenticated **signals**; everything beyond those signals is, from the state machine's perspective, unknown.

What the system *can* do is encode behavioural differentiation into **constraints** and **rules**: the behaviour of the system in response to an event may vary depending on the identity and role encoded in the **verifiable credentials** accompanying that event. The actor Jane and the actor John may trigger different rule pathways, different validation outcomes, and different downstream actions — not because the system models their agency, but because the system encodes the *structural consequences* of their identity in its rule set.

This reactive but evolvable model is a deliberate design choice. It avoids the opacity of agent-internal state while still permitting context-sensitive behaviour.

> **\[OPEN\]** The group should address the relationship between this non-agentic model and the AI/ML systems integration use cases identified in the group charter, particularly where LLM-based inference systems are expected to interact with the control plane. The **Intentional Graph** entry is the current locus for this discussion.

---

### Authentication Event

*Type: Operational concept* **\[PLACEHOLDER\]**

An authentication event is a **signal** whose content is a cryptographic credential submission. It initiates or renews a verified session within the **Context Graph Control Plane**, establishing the identity and permission scope of the submitting actor. Subsequent signals are evaluated against the active authentication context until it expires or is revoked.

> **\[OPEN\]** The group should specify the relationship between authentication events and the W3C Verifiable Credentials Data Model (VC-DATA-MODEL). If this architecture adopts VC as the credential standard, that alignment should be stated normatively; if it diverges, the reasons should be documented.

---

### Utterance Event

*Type: Operational concept* **\[PLACEHOLDER\]**

An utterance event is a **signal** whose content is a statement made by an actor as part of a conversation or narrative. A transcript can be rendered as a context graph where each utterance is a context event. 

---

### Coherence Protocol

*Type: Operational concept*

The Coherence Protocol is the specification governing how multiple **context graphs**, operating across multiple **system boundaries**, compose into a coherent multi-boundary representation. It is distinct from the **Context Graph** data model: where the context graph describes a single boundary in isolation, the Coherence Protocol describes how boundaries connect, how **coherence** gaps propagate across them, and how **resolution traces** link to form an auditable cross-system record.

The Coherence Protocol defines:

- How coherence measurements at one boundary affect coherence assessments at adjacent boundaries.
- How **holons** at different levels of the hierarchy exchange resolution status.
- The format and required fields of the resolution trace records that survive boundary crossings.
- Safe stopping conditions: what the system must do when coherence cannot be restored to a sufficient level for action.

---

### Constraint

*Type: Operational concept*

A constraint is a formal restriction on the permissible states of an entity as represented within a graph. Constraints are evaluated when a change request (an incoming validated **signal**) is applied to the graph. If the resulting state satisfies all applicable constraints, the change is logged as a **context event** and any applicable **rules** are initiated. If the resulting state violates one or more constraints, a **report** is generated and the change is not committed to the graph.

In this specification, constraints are expressed using **SHACL** shapes. The structural and semantic properties of SHACL make it well-suited for constraint expression across both RDF-native and (with adaptation) LPG-based systems.

---

### Context Graph Control Plane

*Type: Operational concept*

The control plane is the interface layer that connects the **context graph**'s **state machine** to external events and systems. It is responsible for all state-modifying operations on the graph and enforces the authentication, validation, and governance requirements of the architecture.

The control plane operates across four principal control points:

1. **Event ingestion:** Receives external **signals**, performs initial routing, and routes them to the appropriate state machine instance. This layer may distribute signals across multiple parallel context graph instances depending on content type or origin.
2. **Authentication and validation:** Evaluates incoming signals against active **verifiable credential** sessions and applicable **constraints**. Determines whether a signal may proceed to the event log.
3. **Rule execution:** When a validated event triggers applicable **rules**, the control plane executes those rules and commits the resulting new content to the graph.
4. **Report publication:** When validation fails, the control plane generates and publishes a **report** as a named graph and may initiate configurable downstream actions.

The control plane design is intended to scale horizontally: because each state machine handles a specific type of context, multiple instances can operate in parallel without shared mutable state.

---

### Decision Interface

*Type: Operational concept*

The decision interface is a neutral contract between the **context graph**'s **coherence** measurements and any external decision model (rules engine, ML classifier, human workflow, policy evaluator, or other). It is intentionally agnostic to the choice of decision framework.

The decision interface specifies:

- **Inputs to the decision model:** the uncertainty vector (a structured representation of measured coherence gaps), applicable policy parameters, and boundary metadata from the current **context graph** instance.
- **Outputs from the decision model:** a required protocol action (proceed, request clarification, escalate, halt) and optional diagnostic flags.
- **Audit requirements:** what must be recorded in the **resolution trace** to support auditable replay of the decision, independent of the decision model used.

The separation of the decision interface from the context graph data model ensures that the governance logic of a specific deployment does not contaminate the portability of the context graph representation.

---

### Intent

*Type: Operational concept*

Intent is an actor's pragmatic purpose at a **boundary** interaction — the goal they are trying to accomplish through the **signals** they are submitting. Intent is distinct from semantics: where semantics defines terms with the goal of shared consistency across authorities, intent is local and individual — it reflects a specific actor's vocabulary, understanding, and purpose at a specific moment.

Intent may be:

- **Explicit** — directly stated by the actor in the signal content.
- **Inferred** — derived by the system from observable evidence in the event trail.
- **Composed** — assembled from multiple sources including stated user intent, system designer intent encoded in the rule set, and subsystem-level behavioural constraints.

When intent is consequential to the resolution of a **coherence** gap and cannot be resolved automatically, it should be recorded in the **resolution trace** — capturing not just what was decided but what intent was attributed to the actor at the time of decision.

For the forward-looking, simulation-based treatment of intent see **Intentional Graph**.

---

### Rule

*Type: Operational concept*

A rule is a forward-chaining inference that produces new graph content when a validated state change satisfies a defined pattern within the **context graph**. Rules are initiated after **validation** succeeds; they do not fire on invalid state transitions.

Rules may:

- Create additional **assertions** (new facts derived from the event).
- Annotate existing assertions with derived properties.
- Initiate interactions with external services through a defined protocol exposed via the **Context Graph Control Plane**.

Rules are expressed in this specification using **SHACL** `sh:SPARQLRule` constructs. In SPARQL terms, a rule body is a CONSTRUCT query evaluated against the current graph state, with the focus node bound to the entity affected by the triggering event.

---

### Shapes and SHACL

*Type: Operational concept*

A shape is a structural — rather than purely semantic — representation of the constraints and rules applicable to specific nodes and their contexts within a graph. Shapes define permissible graph configurations rather than asserting ontological class membership; they are closed-world by default, making them suitable for validation.

The Shape Constraint Language (**SHACL**) is a W3C Recommendation for constructing and evaluating shapes against RDF graphs. In this specification, SHACL is the primary language for the **Context Graph Control Plane**: constraints are expressed as `sh:NodeShape` and `sh:PropertyShape` instances; rules are expressed as `sh:SPARQLRule` constructs.

SHACL's design principles are largely isomorphic to the constraint needs of LPG-based systems, and the shapes model described here can be adapted with minor modification to LPG environments, though the specific syntax will differ.

> **\[OPEN\]** The group should issue a formal position on the intended level of SHACL conformance required by the specification: SHACL Core, SHACL-SPARQL, or SHACL Advanced Features (which includes rules and non-validating constraints). The answer determines the minimum viable engine for conformant implementations.

---

### State Machine

*Type: Operational concept*

The context system is a deterministic state machine: it accepts events from external systems, evaluates them against defined **constraints**, and either commits the result (triggering applicable **rules**) or rejects it (generating a **report**). No state transition occurs that is not triggered by an authenticated, validated event.

The state machine is deliberately mechanistic. It does not model internal motivation or autonomous decision-making; those concerns belong to external systems that interact with the control plane. The state machine can, however, invoke external agentic systems as part of rule execution through the defined protocol of the **Context Graph Control Plane**. The design intent is that the context system itself remains auditable, deterministic, and reproducible — a property that would be compromised by internal agency.

---

### Validation

*Type: Operational concept*

Validation is the process of evaluating a proposed state change — an incoming authenticated **signal** — against the **constraints** and **shapes** defined for the applicable **context** within the **context graph**. Validation is binary at the transaction level: a proposed change either satisfies all applicable constraints (and proceeds to the event log) or fails (and generates a **report**).

Validation in this architecture is performed by the **Context Graph Control Plane** against SHACL shapes applied to the relevant subgraph. A validation pass does not imply semantic correctness in the open-world sense; it implies structural and relational conformance as defined by the closed-world shape model.

> **\[OPEN\]** The relationship between validation in this architecture and the W3C SHACL Recommendation's definition of *conformance* should be specified precisely. In particular, the group should address how the three-value logic of SHACL (conformant, non-conformant, indeterminate) maps onto the binary commit/report model described here.

---

### Verifiable Credentials

*Type: Operational concept*

The context graph is not an open or public triple store. All modifications flow through the **Context Graph Control Plane**, which enforces authentication and access control. This governance layer relies on **verifiable credentials** — cryptographic certificates that establish the identity, role, and permission scope of an actor submitting **signals** to the system.

Credentials are submitted as part of an **authentication event**. They remain active until they expire according to their own validity terms or are invalidated by a validation failure for a credential-sensitive constraint. For a transaction-class interaction (such as a bank payment), the credential may additionally enforce session continuity: the same credential must remain active across all events in the transaction.

> **\[OPEN\]** This specification uses the term *verifiable credentials* in a sense compatible with the W3C Verifiable Credentials Data Model (VC-DATA-MODEL 2.0), but the precise alignment — including which VC proof mechanisms are required, which optional features are in scope, and how VC expiry maps to session termination — should be specified normatively before this section is considered stable.

---

## Appendix A: Terms Under Discussion

The following terms have been raised in group discussions but do not yet have agreed definitions. They are listed here to track open items.

| Term | Status | Notes |
|------|--------|-------|
| **Data Fabric** | Under discussion | Used once in the Context Graph entry. Carries significant vendor-specific connotations (IBM, Informatica, Microsoft). The group should either define it precisely or replace it with less loaded vocabulary. |
| **Event Trail** | Needs definition | Referenced in Context Event. Requires a formal definition specifying structure, queryability, and retention. |
| **Coherence Score** | Proposed | Would formalise the quantitative dimension of the Coherence definition. Requires a measurement model. |
| **Session** | Needs definition | Implied by Verifiable Credentials but not yet a first-class term. |
| **Narrative Interaction** | Needs definition | Used in Intentional Graph. Requires clarification of whether this is a human-facing or system-facing protocol. |

---

## Appendix B: Relationship to Related Standards

The following existing standards are relevant to this specification. Alignment positions are noted where they exist; **\[OPEN\]** marks those that the group has not yet formally addressed.

| Standard | Relevance | Alignment Status |
|----------|-----------|-----------------|
| RDF 1.2 / Turtle 1.2 | Primary graph serialisation; annotation syntax for reification | **Core dependency** |
| SHACL 1.2 | Constraint and rule language for the control plane | **Core dependency** |
| W3C Verifiable Credentials Data Model 2.0 | Authentication and credential model | **\[OPEN\]** |
| W3C PROV-O | Provenance vocabulary; potentially relevant to reification and event trail | **\[OPEN\]** |
| W3C SPARQL 1.2 | Query language; relevant to knowledge graph layer and resolution trace queryability | **\[OPEN\]** |
| OpenCypher / LPG | LPG compatibility layer | Acknowledged; formal mapping not yet specified |
| W3C ODRL | Policy expression; potentially relevant to boundary and access control definitions | **\[OPEN\]** |
| ISO 5087 | Information model | **\[OPEN\]** — Four Facets Model should be compared |

---

*Last updated: 2026-03-07. Maintained by the W3C Context Graph Community Group.*
