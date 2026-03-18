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

### Assertion

*Type: Foundational concept*

An assertion is a statement of belief made by a specific authority. It is the foundational unit of the Semantic Web and of RDF-based graph representations.

Assertions are epistemological rather than strictly factual: an assertion is not required to be an accurate reflection of the world, only a reflection of the truth as held by the asserting authority at the time of assertion. This is a deliberate design property. Because the provenance and authority of an assertion are as important as its content, assertions in this architecture are always accompanied by **reification** metadata that captures who made the assertion, when, and under what conditions.

A set of assertions constitutes a **graph**. Throughout this document, all references to graphs should be understood as references to **hypergraphs** unless otherwise stated.

> **\[OPEN\]** The relationship between assertions in this model and the W3C RDF 1.2 triple model should be made explicit, particularly with respect to blank nodes, named graphs, and the annotation syntax introduced in RDF 1.2.

---

### Coherence

*Type: Foundational concept*

Coherence is a measurable property of a system boundary: the degree to which the meaning, structure, data, and context required for a specific intent are sufficiently aligned for the current risk tolerance at that boundary.

When coherence is high, systems can act on shared information with confidence. When coherence is low, the gap between local and global state is consequential — the same term, the same data, or the same structure may produce different outcomes depending on which system interprets it. This divergence is not a data quality problem and not a schema error; it is a structural property of boundaries between systems that operate under different assumptions.

Coherence is the primary quantity that the **Context Graph** is designed to measure and, through the **Coherence Protocol**, to reduce.

> **\[OPEN\]** The group should define a formal scoring model for coherence: what are the observable inputs, what scale is used, and how does coherence compose across nested **holons**?

---

### Context

*Type: Foundational concept*

Context is structured uncertainty at a system boundary: the set of conditions, assumptions, and prerequisites whose absence or misalignment prevents valid interpretation of a **signal** by a receiving system. Context is not metadata, not a linguistic property of terms, and not noise. It is the information-theoretic gap between what a sender encodes and what a receiver requires in order to act correctly on that encoding — formalised in the Shannon sense of sender, receiver, channel, signal, and uncertainty.

Within the graph architecture, a context is additionally represented as a specific graph configuration forming a testable pattern against which **validation** and **rule** initiation can be triggered. These two senses — the boundary-level information gap and the graph-level structural pattern — are related: the pattern is the local representation of the gap.

> **\[OPEN\]** The relationship between the information-theoretic sense (boundary gap) and the structural sense (graph pattern) should be formalised. Are these the same concept at different levels of abstraction, or two distinct concepts that share a name?

---

### Four Facets Model

*Type: Foundational concept*

The Context Graph architecture assigns identity and structure to all atomic units of contextual information using four orthogonal facets:

| Facet | Description |
|-------|-------------|
| **Data** | The raw informational content of the unit. |
| **Meaning** | The semantic interpretation of that content within a given vocabulary or ontology. |
| **Structure** | The constraints and relationships governing how the unit relates to others. |
| **Context** | The boundary conditions and prerequisites required for valid interpretation. |

Every **context event**, **assertion**, and **resolution trace** can be characterised in terms of all four facets. The model is intended to be general enough to apply across domains and agnostic to any specific technology stack.

> **\[OPEN\]** The mapping between the Four Facets Model and existing foundational models (e.g. the FAIR principles, the ISO 5087 information model, the W3C Data Catalog vocabulary) should be documented to situate the model in the wider standards landscape.

---

### Holon

*Type: Foundational concept*

A holon is an entity that is simultaneously a whole (with its own internal structure) and a part of a larger system. The term is used here in the computational sense: a holon is a system with a defined boundary that encapsulates internal state and exposes an external representation of that state to other systems.

In the Context Graph architecture, holons define the boundaries at which coherence is measured. A country can be a holon composed of states; a microservice can be a holon within a larger application; a bank branch can be a holon within a banking system. Holons may be represented as named graphs and interact with one another through **reifications** on the named graph that represents them as an entity.

The nesting of holons is recursive: a holon may contain other holons to any depth, and the **Coherence Protocol** governs how coherence measurements compose across this hierarchy.

> **\[NOTE\]** The term *holon* originates with Arthur Koestler (*The Ghost in the Machine*, 1967) and has prior use in philosophy of mind and systems theory. Its adoption here is for precision, not provocation — specifically, the dual whole/part property has no clean equivalent in standard graph vocabulary. Alternatives considered included *scope*, *system boundary*, and *named graph boundary*; none captured the recursive compositional property adequately.

---
### Ontology, Protocol, or Both?

*Type: Foundational concept*

TCP/IP counts bits and packets to answer: did the message arrive intact? The Coherence Protocol counts bits to answer: does the message mean the same thing on both sides? TCP/IP verifies transmission. The protocol verifies coherence. Both measure in bits. Different bits. Different layer. Same architectural pattern.
The topology is where it gets important. TCP/IP has a sender, a receiver, and a channel. The packet either arrived or it didn't. That's a two-party check on a single channel.

The Context Graph is fundamentally different. There is no master graph. Each system has its own local context graph — its own representation of what it knows, what it assumes, and what it has resolved. Coherence is not a property of any single graph. It's a property of the boundary where two local graphs meet. No graph is authoritative over another. Each is a local reflection.
What lives on each local graph — the claims about meaning, structure, data, and context at every boundary — is an ontology. It is a structured representation of what a system knows and assumes. The protocol is what runs on that ontology: measuring, comparing, resolving. The graph is the knowledge. The protocol is the instrument. They are not competing concepts. One is the surface. The other is what you do with it. See: [Holon](#holon)

But the relationship goes deeper than surface and instrument. The graph doesn't exist before the protocol runs. The protocol has nothing to run on before the graph exists. They create each other.
When the protocol runs at a boundary — comparing codebooks, detecting misalignment, issuing an Ask — the result is a ContextEvent that gets written to the graph. That event is the graph. The graph is not a pre-existing database the protocol queries. It is the accumulated trace of the protocol having run. And the next time the protocol runs at that boundary — or any boundary involving the same systems — it reads the graph that prior runs produced. The resolutions already recorded shape what the protocol needs to ask next. The graph tells the protocol what's already been resolved. The protocol tells the graph what's still missing.

**The protocol is the graph observing itself and deciding what to do.**

Your context graph reflects your system's state. My context graph reflects mine. Neither of us has the "real" graph. What we have is a comparison surface — the canonical claim form — where both local graphs can project onto the same five-column substrate and the protocol can measure the divergence in bits.

This is similar to the mercury pool reflecting the stars metaphor: the local boundary (mercury surface) can only reflect its outer boundary (stars), but it doesn't directly touch or have any ownership of it.
The pathways between local graphs — every Ask, every resolution trace, every Context entry that flows across a boundary — are not data transfers. They are pathways to reconciliation of mutual understanding. Each one narrows the gap between what your (sender) graph reflects (a local ContextEvent can include boundaries of many systems) and what my (receiver) reflects. The bit-level accuracy is what makes that narrowing measurable. You can say: this boundary had 3 bits of divergence, we resolved 2 through Asks, 1 remains open, the protocol action is Ask or Halt depending on your threshold.

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
## Part 4: Semantic Commitments


### 4.1 Overview

The W3C Context Graph Community Group operates under five semantic commitments. Together they specify what the protocol is trying to achieve, why local interpretation is necessary, what honest self-reporting looks like, why no prior agreement is required, and why the community maintaining this standard must apply the same discipline to itself. 

This is the operational practice of holonic thinking: a context graph continually finds and resolves the knowledge gaps any system has — through inference, deterministic methods, or human judgment. The same principles that govern honest measurement in digital systems apply in physical and organizational ones.

The five commitments in this section are the conditions under which trust becomes verifiable rather than assumed — grounded in honest measurement, local sovereignty, and decisions whose rationale is fully expressible on the canonical claim surface.

---

## 4.2 Semantic Honesty

*Term introduced by Kurt Cagle, Context Graph Architecture §1.3.*

The architecture makes its epistemic commitments visible. Divergence between a representation and the world it describes is named, detectable, and auditable — not an implementation error. The architecture does not pretend to more certainty than it has.

Semantic honesty is the goal. The coherence protocol is the instrument that makes it verifiable. A system that asserts its commitments without measuring whether the receiving system shares them is not being honest — it is being confident.

---

## 4.3 Semantic Sovereignty
*Semantic Commitments*

**Definition.** A system retains the right to define and interpret its own terms locally. No external ontology can resolve meaning at a boundary without the local system's participation.

Semantic sovereignty is not an argument against shared ontologies — they work well within communities that have agreed to use them. It asserts that this scope has a boundary, and that boundary is exactly where the coherence protocol operates.

Contextual misalignment is not a failure of standardization. It is the normal condition at any boundary between independently designed systems. It requires a measurement instrument, not a coordination mandate.

---

## 4.4 Bounded Reflexivity
*Semantic Commitments*

**Definition.** A system honestly reports its current coherence state within the constraints of what has been measured, claiming nothing beyond what the protocol can verify.

The protocol provides the *foundation* on which reflexive capabilities can be built. The five-column canonical claim form, the append-only context log, and the Halt/Ask/Act decision record give any system the substrate it needs to reason about its own coherence state.

What a system does with that substrate — how it reasons about misalignment patterns, improves its Ask sequences, predicts future incoherence — is above the protocol layer. The protocol does not constrain those capabilities. It makes them possible by ensuring their inputs are honest.

---

## 4.5 Unilateral Activation
*Semantic Commitments*

Every prior interoperability standard requires both parties to have adopted it before it can function. The coherence protocol requires neither.

One side can run the protocol unilaterally. The Ask acquires information from the other side regardless of whether the other side knows the protocol exists. The other side needs only to be able to answer a question.

This is what makes the protocol a *measurement instrument* rather than a *coordination protocol*. A ruler measures a wall whether or not the wall knows it is being measured.

**On full adoption.** If all systems adopted the protocol, they would share a common substrate — which would itself constitute a shared codebook. This is not a contradiction. It is the expected behavior of any successful standard. What distinguishes this protocol is that full adoption is never required for it to work. Partial adoption makes it faster. Full adoption makes it richer. Neither is a precondition.

*Hypothesis: The more systems that adopt the Context Graph Architecture with the Coherence Protocol, the more capable these systems can be in reducing uncertainty of shared understanding.*

---

## 4.6 Semantic Openness
*Semantic Commitments*



**Definition.** This community treats its own vocabulary as subject to the same measurement discipline it applies to all boundaries. No term in this specification is exempt from the coherence protocol. The standard does not lock meaning — it provides the instrument to detect and record when meaning has changed.

Standards create stability. But they also lock meaning — and a specification written for one community may carry different meaning when adopted by another. This is not a failure of standards. It is a structural property of open systems, and it applies equally to this protocol.

Three terms that are not the same thing:

**Open World Assumption** — a logical stance. Absence of a statement does not mean it is false, only unknown. Governs reasoning about incomplete knowledge within a model.

**Open systems** — an architectural property. A system that interacts across boundaries it did not design, with parties it did not anticipate.

**Semantic openness** — a governance commitment. The community maintaining this standard applies the coherence protocol to its own vocabulary rather than exempting itself from measurement.

These are orthogonal. Conflating them produces communities that reason carefully about incompleteness and boundary interactions — and then treat their own vocabulary as immune to both.

> Standards create the stability that makes coordination possible. Semantic openness ensures that stability does not become the new source of incoherence.

The protocol implements all five simultaneously.

---

## 4.7 The Decision Dependency Chain
*Operational Layer*

Every decision made in response to measured uncertainty — a judgment, a policy, a rationale, an automated action, a human override — is itself a claim. It has a source, a timestamp, a key, and a value. It belongs on the canonical claim surface. It is subject to all five commitments.

This is not optional. A decision process that operates above the protocol without leaving a claim record is not semantically honest. A policy that cannot be expressed as a claim cannot be audited. A rationale that lives only in a human's head cannot be sovereign, reflexive, or open. The canonical claim form is not just the substrate for measuring uncertainty — it is the substrate for everything the protocol produces in response to uncertainty.

The decision dependency chain specifies the URN vocabulary available for this purpose. These are the cognitive primitives: the minimal operators through which any system — computational or human — can act on the context graph, record its reasoning, and participate in the coherence protocol.



### Computational primitives — `urn:self:`

Operations a system performs on its own context graph.

| URN | Action |
|---|---|
| `urn:self:search:<query>` | Query the context graph for matching claims |
| `urn:self:get:<claim-id>` | Retrieve a specific claim by identifier |
| `urn:self:update:<claim-id>` | Write a resolution record to the context graph |
| `urn:self:compare:<claim-id-a>:<claim-id-b>` | Run the four-facet gauge on two claims |
| `urn:self:halt` | Record a Halt decision and its reason |
| `urn:self:ask:<facet>` | Record an Ask and the facet being queried |
| `urn:self:act` | Record an Act decision and its conditions |
| `urn:self:math:<function>:input:<value>` | Apply a mathematical function to a claim value |

The `urn:self:math:` namespace is open. Any function that operates on claim values — aggregation, normalization, entropy calculation, statistical comparison — can be expressed here without changing the substrate.

---

### Psychometric primitives — `urn:human-in-loop:`

Signal contributed by a human participant. Structurally identical to any other claim — same five columns, same URN-namespaced key, same canonical form. The substrate does not distinguish between a sensor reading and a human judgment. Both are measurements.

| URN | Signal |
|---|---|
| `urn:human-in-loop:agree` | Human confirms a resolution |
| `urn:human-in-loop:disagree` | Human rejects a resolution — 1 bit, surfaced |
| `urn:human-in-loop:rate:<value>` | Scalar rating of a claim or resolution |
| `urn:human-in-loop:flag` | Surface a claim for human review |
| `urn:human-in-loop:dislikes` | Negative preference signal |
| `urn:human-in-loop:query-rate:<value>` | Frequency of queries — cognitive load signal |
| `urn:human-in-loop:bits-per-query-avg:<value>` | Average information acquired per Ask — efficiency signal |

The psychometric primitives are first-class measurements. A human disagreeing with a resolution is a 1-bit claim — `urn:human-in-loop:disagree` — with source, timestamp, and context. It enters the append-only log alongside every other claim. It can trigger a new Ask. It can change a protocol state from Act back to Ask. The human is in the loop structurally, not just philosophically.

---

### The chain

These primitives operate under all five commitments simultaneously:

```
Semantic Honesty        — every decision record is visible and auditable
        ↓
Semantic Sovereignty    — every system records decisions in its own terms
        ↓
Bounded Reflexivity     — every record claims only what was measured
        ↓
Unilateral Activation   — any system can begin recording without agreement
        ↓
Semantic Openness       — the primitive vocabulary is extensible by the community
```

Any decision process — automated policy, human judgment, AI recommendation, regulatory rationale — that cannot be expressed through these primitives on the canonical claim surface is operating outside the protocol. The protocol does not prohibit that. It makes the gap visible.

> What cannot be recorded cannot be audited. What cannot be audited cannot be honest.




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
