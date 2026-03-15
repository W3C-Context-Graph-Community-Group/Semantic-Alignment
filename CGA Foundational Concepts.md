# Context Graph Architecture: Foundational Specification {#title}

**W3C Context Graph Community Group — Draft Community Group Report**

---

> **Status of This Document**
>
> This document is a Draft Community Group Report produced by the
> [W3C Context Graph Community Group](https://www.w3.org/community/context-graph/).
> It does not represent a W3C Standard and is subject to change without notice.
> All participants in this group have signed the
> [W3C Community Contributor License Agreement](https://www.w3.org/community/about/agreements/cla/).
>
> Publication as a Community Group Report does not imply W3C endorsement.
> This document is a foundational position paper intended to achieve working
> consensus among Community Group participants on the core concepts, semantic
> commitments, and architectural principles that underlie the Context Graph
> Architecture (CGA) family of specifications. It is explicitly informative:
> normative conformance requirements are delegated to the specification
> documents that build upon this foundation.
>
> Feedback is invited via the group's
> [GitHub repository](https://github.com/w3c/context-graph) issue tracker
> and mailing list. This document is expected to undergo significant revision
> as the normative specifications mature.

---

| Field | Value |
|---|---|
| **Title** | Context Graph Architecture: Foundational Specification |
| **Shortname** | cga-foundation |
| **Version** | 0.1 (Draft) |
| **Date** | 2026-03-15 |
| **Status** | Draft Community Group Report |
| **This version** | https://w3c.github.io/context-graph/foundation/ |
| **Repository** | https://github.com/w3c/context-graph |
| **Editors** | TBD — Context Graph CG Chairs |
| **Contributors** | Kurt Cagle, Ron Itelman, Holger Knublauch, Jason Koh, Connor Cantrell, Michael Caskey |
| **License** | [W3C Software and Document License](https://www.w3.org/Consortium/Legal/2015/copyright-software-and-document) |

---

## Abstract {#abstract}

This document establishes the foundational concepts, semantic commitments,
and architectural principles underlying the Context Graph Architecture (CGA).
It defines what it means for a graph to represent a bounded, interactable
context; how the truthfulness of statements within that context is understood
and qualified; how trust is assigned to different sources of information; and
how state change, event history, and uncertainty are formally represented.

The document does not define implementation conformance requirements — those
are delegated to the normative specifications that build upon this foundation.
Its purpose is to make the commitments of those specifications explicit and
accessible, so that implementers, ontology designers, domain experts, and
standards authors share a common basis for decisions.

The intended audience is technically literate practitioners: senior architects,
technical leads, and informed executives who need to understand what the
architecture commits to and why, without requiring deep expertise in formal
logic or knowledge representation theory.

---

## Table of Contents {#toc}

- [1. Introduction](#introduction)
  - [1.1 Motivation and Scope](#motivation-and-scope)
  - [1.2 Relationship to Existing W3C Specifications](#relationship-to-w3c)
  - [1.3 Design Philosophy](#design-philosophy)
  - [1.4 Document Conventions](#document-conventions)
- [2. Terminology and Definitions](#terminology)
  - [2.1 Core RDF Terminology](#core-rdf-terminology)
  - [2.2 Architecture-Specific Terms](#architecture-specific-terms)
  - [2.3 Notation Conventions](#notation-conventions)
- [3. Semantic Commitments](#semantic-commitments)
  - [3.1 The Epistemic Stance of the Architecture](#epistemic-stance)
  - [3.2 What It Means for a Statement to Be in a Graph](#statement-semantics)
  - [3.3 The Assertion-Belief-Observation Distinction](#assertion-belief-observation)
  - [3.4 Graph State and World State](#graph-state-world-state)
  - [3.5 Reconciling Open and Closed World Assumptions](#owa-cwa)
- [4. The Architectural Layering Model](#layering-model)
  - [4.1 Overview](#layering-overview)
  - [4.2 The T-Box: Class and Property Definitions](#t-box)
  - [4.3 The A-Box: Instance Assertions](#a-box)
  - [4.4 The S-Box: Shapes and Constraints](#s-box)
  - [4.5 The X-Box: Controlled Vocabularies](#x-box)
  - [4.6 Inter-Layer Relationships](#inter-layer)
  - [4.7 Layer Governance Principles](#layer-governance)
- [5. The Holon Model](#holon-model)
  - [5.1 Holons as the Fundamental Unit](#holons-fundamental)
  - [5.2 The Interior/Exterior Distinction](#interior-exterior)
  - [5.3 Boundary Membranes](#boundary-membranes)
  - [5.4 Holarchies: Whole/Part Relationships](#holarchies)
  - [5.5 The Scene Graph as Interior Model](#scene-graph)
  - [5.6 Self-Referential Identity](#self-referential-identity)
- [6. The Context Graph Model](#context-graph-model)
  - [6.1 What Is a Context Graph](#what-is-context-graph)
  - [6.2 Context Graphs and Holons](#context-graphs-and-holons)
  - [6.3 Contextual Misalignment and Resolution](#contextual-misalignment)
  - [6.4 Context Propagation Across Boundaries](#context-propagation)
- [7. The Trust Model](#trust-model)
  - [7.1 Sources of Assertion](#sources-of-assertion)
  - [7.2 Trust Levels](#trust-levels)
  - [7.3 The Trust Boundary as Architectural Primitive](#trust-boundary)
  - [7.4 Verified Identity: DIDs and the Agent Model](#did-agent-model)
  - [7.5 Credential-Based Access: Verifiable Credentials](#verifiable-credentials)
  - [7.6 Device Signals and the IoT Trust Chain](#iot-trust-chain)
  - [7.7 Derived Assertions and Rule-Generated Triples](#derived-assertions)
- [8. State, Event, and Transition](#state-event-transition)
  - [8.1 The Event Sourcing Commitment](#event-sourcing)
  - [8.2 State as Derived from History](#state-derived)
  - [8.3 The Causal Order Model](#causal-order)
  - [8.4 Temporal Semantics: Two Kinds of Time](#temporal-semantics)
  - [8.5 Partial Ordering Under Asynchrony](#partial-ordering)
  - [8.6 State Machine Semantics](#state-machine-semantics)
- [9. Belief, Uncertainty, and Inference](#belief-uncertainty)
  - [9.1 Belief States and Confidence Annotation](#belief-states)
  - [9.2 Precision Weighting and Signal Reliability](#precision-weighting)
  - [9.3 The Generative Model Correspondence](#generative-model)
  - [9.4 The Discrete/Continuous Interface](#discrete-continuous)
  - [9.5 Hierarchical Structure and Inference](#hierarchical-inference)
- [10. The Presentation and Perception Model](#presentation-model)
  - [10.1 The Projection Graph as Derived View](#projection-graph)
  - [10.2 Appearance as Declarative Style](#appearance-graph)
  - [10.3 Perception as Agent-Relative Projection](#agent-perception)
  - [10.4 The Delta Subscription Model](#delta-subscription)
  - [10.5 Multi-Agent Consistency](#multi-agent-consistency)
- [11. Lifecycle and Governance](#lifecycle-governance)
  - [11.1 Entity and Holon Lifecycle](#entity-lifecycle)
  - [11.2 Schema Evolution and Versioning](#schema-evolution)
  - [11.3 Vocabulary Governance](#vocabulary-governance)
  - [11.4 Conflict Resolution](#conflict-resolution)
  - [11.5 Graph Compaction and Archival](#compaction-archival)
- [12. Relationship to Adjacent Standards](#adjacent-standards)
- [13. The CGA Specification Family](#specification-family)
- [Appendix A: Design Decision Log](#design-decisions)
- [Appendix B: Comparison With Related Architectures](#comparisons)
- [Appendix C: Worked Example](#worked-example)
- [Appendix D: Acknowledgements](#acknowledgements)
- [Appendix E: References](#references)

---

## 1. Introduction {#introduction}

### 1.1 Motivation and Scope {#motivation-and-scope}

The World Wide Web Consortium has, over three decades, developed a powerful
and interoperable family of specifications for representing, querying, and
constraining data as graphs. RDF provides the data model. SPARQL provides
the query language. SHACL provides constraint validation and rule-based
inference. OWL provides formal ontological reasoning. Together, these
specifications can represent virtually any domain of knowledge with
precision and interoperability.

What this specification family does not address — and what increasingly
matters as knowledge graphs are deployed in operational, multi-agent, and
cyber-physical environments — is what it means for a graph to represent a
**place**: a bounded, inhabited, interactable context within which agents
operate, entities have state that changes over time, and the graph's
representation must be maintained in coherent correspondence with the
world it describes.

A database table, a web page, a room in a building, a city district, a
chapter in a book, an organisation's policy domain, and a digital twin of
a manufacturing cell are all, in this sense, bounded contexts. Each has
an interior — the entities, relationships, and state that exist within it.
Each has an exterior — the representation it presents to the wider world.
Each has a boundary — the rules governing what may enter, what may exit,
and what is visible from outside. Each changes state over time in response
to events. Each may contain other bounded contexts in a nested hierarchy.

No existing W3C specification provides a unified framework for representing
these structures and their behaviour. The Context Graph Architecture (CGA)
is a proposed framework that fills this gap, built compositionally from
existing W3C standards.

The scope of this document is the **foundational concepts** that the CGA
requires. It does not specify the full details of implementation — those
belong to the normative specification documents referenced in Section 13.
Its purpose is to establish common ground: to define what we mean when we
say "context graph," "holon," "boundary," "event," "belief," and "trust,"
so that the normative specifications that use these concepts can be written
and read with a shared understanding.

### 1.2 Relationship to Existing W3C Specifications {#relationship-to-w3c}

The Context Graph Architecture is not a replacement for, or extension of,
any existing W3C specification. It is a **compositional pattern** — a
specification of how existing standards are assembled, combined, and
interpreted to achieve architectural goals that none of them achieves alone.

The primary W3C specifications upon which the CGA is built are:

- **RDF 1.2** — the data model, including named graphs and the `{| |}`
  reification notation that allows statements to be annotated with metadata
- **SHACL 1.2** — the constraint and rule layer, providing validation of
  graph structure and rule-based generation of new triples
- **SPARQL 1.2** — the query and graph manipulation language underlying
  SHACL's constraint and rule mechanisms
- **OWL 2** — the ontological layer for domain modelling and class reasoning
- **SKOS** — the controlled vocabulary layer for classification schemes and
  taxonomies
- **W3C DID Core 1.0** — the decentralised identifier specification providing
  cryptographically verifiable, controller-independent identifiers
- **W3C Verifiable Credentials Data Model 2.0** — the credential specification
  providing privacy-respecting, cryptographically verifiable claims
- **GeoSPARQL 1.1** — spatial representation and query
- **OWL Time** — temporal representation

The novel contribution of this specification is not any new data model,
query language, or constraint mechanism. It is the explicit specification of
how these existing components are composed, what semantic commitments govern
that composition, and what vocabulary names the components and relationships
that the composition requires.

### 1.3 Design Philosophy {#design-philosophy}

Four principles govern the design of the Context Graph Architecture. These
are not arbitrary preferences — they are the result of the design choices
described in Appendix A, and they have consequences throughout the normative
specifications that build on this foundation.

**Semantic honesty.** The architecture makes its epistemic commitments
visible. Where a statement represents a belief qualified by confidence, it
is represented and queried as such. Where a graph representation may diverge
from the physical world it describes, that divergence is a named,
detectable, and auditable condition — not an implementation error. The
architecture does not pretend to more certainty than it has.

**Compositionality.** The architecture is built from existing W3C standards.
New vocabulary is introduced only where no existing term adequately names
a required concept. This maximises interoperability with the existing
semantic web ecosystem and minimises the learning curve for practitioners
already familiar with the RDF stack.

**Operational closure with epistemic openness.** Within a bounded context,
the architecture adopts the closed world assumption for operational purposes:
rules fire on the graph as presented; constraints are evaluated against the
graph as it exists. At the domain modelling level, the architecture adopts
the open world assumption: a holon may contain entities and relationships
not yet described by any current shapes graph. This is not a contradiction
but a principled stratification, described fully in Section 3.5.

**Auditability by design.** All state changes are the product of events
that are recorded, retained, and ordered. The current state of any entity
is derivable from its event history. Nothing is silently mutated. This
principle underlies the event sourcing commitment described in Section 8.

### 1.4 Document Conventions {#document-conventions}

**Informative vs. normative content.** This document is entirely informative.
No statement in this document establishes a conformance requirement. Where
a statement appears that will eventually become normative in a downstream
specification, it is prefaced with the phrase "the architecture requires"
or "the specification will require."

**Prefix declarations.** The following prefixes are used throughout this
document. All are well-established W3C namespaces except `cga:`, which is
the provisional namespace for the Context Graph Architecture vocabulary.

```turtle
@prefix rdf:  <http://www.w3.org/1999/02/22-rdf-syntax-ns#> .
@prefix rdfs: <http://www.w3.org/2000/01/rdf-schema#> .
@prefix owl:  <http://www.w3.org/2002/07/owl#> .
@prefix xsd:  <http://www.w3.org/2001/XMLSchema#> .
@prefix sh:   <http://www.w3.org/ns/shacl#> .
@prefix skos: <http://www.w3.org/2004/02/skos/core#> .
@prefix geo:  <http://www.opengis.net/ont/geosparql#> .
@prefix time: <http://www.w3.org/2006/time#> .
@prefix cga:  <https://w3c.github.io/context-graph/ns#> .
```

> **Note:** The `cga:` namespace is provisional and subject to change by
> CG decision. It is used throughout this document to distinguish
> CGA-specific vocabulary from general RDF vocabulary.

**Code examples.** Turtle and SPARQL code blocks appear throughout this
document as informative illustrations. They are not normative and may be
simplified for clarity.

**Definitions.** Defined terms appear in **bold** at their point of
definition. Cross-references to defined terms are italicised on first use
within each major section.

---

## 2. Terminology and Definitions {#terminology}

### 2.1 Core RDF Terminology {#core-rdf-terminology}

This document assumes familiarity with the following RDF concepts as defined
by their respective W3C specifications. Brief characterisations are provided
for context; the authoritative definitions are in the cited specifications.

A **triple** is the atomic unit of RDF data: a subject-predicate-object
statement asserting a relationship between two resources or between a
resource and a literal value. (RDF 1.2 Concepts and Abstract Syntax)

A **named graph** is a set of triples associated with a name — an IRI that
identifies the graph and can itself be the subject of other triples.
Collections of named graphs form a **dataset** or, in the multi-graph
context used here, a **hypergraph**. (RDF 1.2 Concepts)

A **reifier** is a resource that annotates a triple with metadata, using
the RDF 1.2 `{| |}` notation. The reifier carries additional statements
about the triple without altering the triple's assertional status.
(RDF 1.2 Concepts)

A **shape** in SHACL is a set of constraints that a node or property in
a graph must satisfy. A **shapes graph** is a named graph containing shape
declarations. (SHACL 1.2)

A **SHACL rule** is a SPARQL CONSTRUCT expression attached to a shape that
generates new triples when focus nodes satisfy the shape's conditions.
(SHACL 1.2 Advanced Features)

### 2.2 Architecture-Specific Terms {#architecture-specific-terms}

The following terms are specific to the Context Graph Architecture. Where
a term has a pre-existing usage in another field (systems theory, computer
science, philosophy), the CGA usage is noted as a specialisation or
extension of that usage.

**Holon.** An entity that is simultaneously a self-contained whole and a
component of a larger structure. In the CGA, a holon is represented as a
named graph whose IRI serves both as the persistent identity of the entity
and as the address of its interior content. The term is drawn from Arthur
Koestler's systems theory (1967) and is used here in a graph-theoretic
rather than biological sense.

**Holarchy.** A hierarchical structure composed of holons, where each
holon is both a whole relative to the holons it contains and a part
relative to the holon that contains it. In CGA terms, a holarchy is
represented as a directed acyclic graph of `cga:parentHolon` relationships
between holon IRIs.

**Context Graph.** A named graph that carries the interpretation context
active at a point of decision or interaction. A context graph resolves
ambiguity in how terms, concepts, and policy references should be
understood by a specific agent in a specific situation. Context graphs
are epistemic — they are about how to interpret what is asserted — whereas
holon interior graphs are ontological — they are about what exists and
what state it is in.

**Interior Graph.** The named graph addressed by a holon's IRI. The
interior graph contains the scene graph of entities, state assertions,
structural relationships, and event history references that constitute the
holon's internal world.

**Exterior Graph.** A named graph derived from a holon's interior graph
by controlled projection, representing the holon as it appears to other
entities in the parent holon's scene graph. The exterior graph contains
aggregate, access-controlled summaries of interior state; it does not
expose raw interior content.

**Boundary Membrane.** The shapes graph associated with a holon, which
serves three functions: it defines what entities may exist within the
interior (structural validation); it governs what transitions are valid
(operational constraint); and it specifies the conditions under which
agents may enter or exit (portal access control). The membrane metaphor
is deliberate — a boundary membrane is selective, identity-preserving,
and the primary mechanism by which a holon maintains its coherence.

**Portal.** A reified triple in a parent holon's interior graph that
represents a traversable connection to a child holon's interior. The
portal triple asserts a directional relationship between a location entity
in the parent scene graph and the child holon's IRI; the reifier annotation
carries liminal state (locked, open, traversing) and access conditions.

**Focus Agent.** A principal — identified by a Decentralised Identifier —
that inhabits a holon interior, carries a context graph of verified claims
and current state, and generates interaction events. The term "focus" is
drawn from SHACL's concept of a focus node, adapted to name the active,
mobile, identity-bearing participant in a CGA system.

**Scene Graph.** The directed acyclic graph of entities within a holon
interior, together with their positions in the holon's coordinate system
and their relationships to one another. The scene graph is the spatial
and relational model of the holon's content. It is a data structure, not
a visualisation construct.

**Assertion.** A triple asserted into a named graph by a source whose
identity has been verified and whose authority to make this assertion has
been validated. Assertions from verified agents and validated device signals
are the inputs to the event sourcing pipeline.

**Observation.** A raw signal received from an external source — typically
an IoT device or sensor — whose trustworthiness has not yet been established.
Observations reside in the ingestion graph and carry no assertional status
in the interior graph until promoted through the trust boundary.

**Belief.** An assertion annotated with a confidence value representing the
system's current degree of certainty that the asserted state corresponds to
the world. All state triples in the interior graph are beliefs; they differ
in whether their confidence is explicit (annotated in the reifier) or
implicit (assumed maximum for directly asserted verified facts).

**Twin Sync State.** The coherence relationship between a digital twin
entity's graph representation and its physical instantiation. Twin sync
state is a first-class property of any entity that corresponds to a
physical object or device, and transitions through the values Synchronised,
Lagged, Degraded, and Diverged.

**Projection Graph.** A named graph derived from the interior graph, the
appearance graph, and a specific agent's current position and perceptual
scope, representing what that agent currently perceives within the holon.
Projection graphs are agent-specific, dynamically maintained, and the
primary source of information for client presentation layers.

**Appearance Graph.** A named graph containing declarative presentation
rules that map entity IRIs to visual or sensory properties, conditioned
on entity state. The appearance graph is analogous to a CSS stylesheet:
it is declarative, separable from the data it styles, and its rules
cascade through the holon hierarchy.

**ContextEvent.** A formally recorded state change or interaction — an
immutable entry in the event history graph. Every change to interior graph
state is the product of one or more ContextEvents. The event history is
append-only; nothing is ever deleted from it.

**Trust Boundary.** The architectural interface at which unverified
external signals become trusted internal assertions. The trust boundary
is a named, explicit component of every CGA deployment. All transitions
across it are recorded in the event history.

**Free Energy (CGA sense).** The divergence between the expected state of
a named graph as specified by its shapes graph and the observed state as
recorded in event and sensor data. Used informally in this document to
describe the condition that drives state machine transitions and
active inference. This is not a formal mathematical quantity in this
specification; the connection to Friston's variational free energy principle
is an informative analogy.

### 2.3 Notation Conventions {#notation-conventions}

Turtle examples use RDF 1.2 syntax including the `{| |}` reifier notation.
A reified triple with annotations takes the following form:

```turtle
<subject> <predicate> <object> {|
    <annotation-property> <annotation-value> ;
    <annotation-property> <annotation-value>
|} .
```

This notation asserts the base triple `<subject> <predicate> <object>` and
additionally asserts that the anonymous reifier node has the stated properties.
The reifier node is distinct from the subject, predicate, or object; it
annotates the triple as a whole.

Named graph blocks use the SPARQL/Turtle `GRAPH` keyword:

```turtle
GRAPH <graph-iri> {
    # triples in this named graph
}
```

SPARQL examples are shown as complete queries or as query fragments with
ellipsis notation (`# ...`) indicating omitted clauses.

---

## 3. Semantic Commitments {#semantic-commitments}

This section is the most important in the document. It states what the
architecture means by its fundamental operations. Every normative
specification in the CGA family presupposes the commitments made here.

### 3.1 The Epistemic Stance of the Architecture {#epistemic-stance}

A knowledge graph is, at its core, a claim about the world. Every triple
in a graph asserts that something is true: that this entity has that
property, that these two entities are related in this way, that this event
occurred at that time. The question the CGA must answer — and that most
graph architectures leave implicit — is: **true in what sense, and with
what degree of confidence?**

The CGA adopts a **stratified epistemic model**. Different named graphs
within the hypergraph carry different epistemic statuses, and the
interpretation of a triple depends on which graph it inhabits.

The *event history graph* occupies the highest epistemic status — it
records what was asserted, by whom, and when, without interpretation or
aggregation. It is the closest the architecture comes to ground truth.

The *interior graph* occupies the operational epistemic status — it
represents the system's current best belief about the holon's state,
derived and maintained by processing the event history. It is true for
operational purposes: rules fire against it, agents query it, constraints
are validated against it. But it is always a derived representation, not
the primary record.

The *projection graph* occupies the perceptual epistemic status — it
represents what a specific agent currently perceives, derived from the
interior graph under spatial and access constraints. It is correct relative
to the agent's position and scope, but not necessarily a complete picture
of the interior.

The *appearance graph* occupies a policy epistemic status — it is not
truth-apt at all. It specifies how entities should be presented, not what
is true about them.

This stratification is not merely theoretical. It has direct practical
consequences: a query against the interior graph returns the system's best
current belief; a query against the event history returns the authoritative
record; a query against the projection graph returns what a specific agent
can currently see. These are different questions with different answers,
and conflating them is a source of significant architectural error.

### 3.2 What It Means for a Statement to Be in a Graph {#statement-semantics}

In classical RDF semantics, a triple in a graph is an existential assertion:
the graph asserts that the described relationship holds. The CGA extends
this interpretation with graph-layer semantics.

A triple `<s> <p> <o>` in the interior named graph of a holon means:
*the system's current best belief is that entity s has property p with
value o, within the operational scope of this holon.*

This formulation differs from classical RDF assertion in two significant ways.

First, it is scoped to the holon. A triple asserting that an entity has a
particular status is valid within this holon's operational context and does
not necessarily generalise beyond it. Two holons may hold different beliefs
about the same entity, reflecting different observation histories or different
access to information.

Second, it is qualified as a belief. The architecture acknowledges that the
interior graph may not perfectly reflect the world — signals may be delayed,
sensors may be imprecise, concurrent updates may create transient
inconsistencies. A triple without a reifier annotation carries an implicit
confidence of 1.0, meaning it was asserted by a verified source with no
qualification. A triple with a reifier annotation carrying
`cga:beliefConfidence "0.87"^^xsd:decimal` expresses that the system
currently believes this triple with 87% confidence, which should inform
how rules use it and how clients present it.

The reifier annotation is therefore not metadata in the dismissive sense
— something appended for bookkeeping. It is a first-class qualifier that
changes the meaning of the triple for the purposes of reasoning and
presentation.

### 3.3 The Assertion-Belief-Observation Distinction {#assertion-belief-observation}

The architecture formally distinguishes three categories of incoming
information, because they carry different epistemic statuses and must
be handled by different pipeline components.

An **observation** is a raw signal from a source whose trustworthiness
has not been established in the current interaction. Observations arrive
from IoT sensors, external APIs, unverified agents, and other sources
that have not yet passed through the trust boundary. They are written to
the ingestion graph. They carry no assertional status in the interior
graph until they have been validated and promoted.

An **assertion** is a statement whose source has been verified —
cryptographically in the case of human agents and high-assurance devices,
by registry lookup and signal validation in the case of lower-assurance
devices. An assertion enters the event history graph as an immutable record
and subsequently drives interior graph state through the SHACL rule
machinery.

A **belief** is an assertion annotated with a confidence value derived
from the precision of its observational basis and the weight of prior
system beliefs. All state triples in the interior graph are beliefs. For
many operational deployments, this distinction is transparent — high-quality
sensors and verified agents produce near-maximum confidence beliefs that
behave identically to classical RDF assertions for all practical purposes.
The distinction becomes important in sensor-rich, high-noise, or adversarial
environments where acting on a low-confidence belief without qualification
would produce incorrect behaviour.

The following example illustrates the distinction using the Turtle `{| |}`
notation:

```turtle
# An observation — in the ingestion graph, not yet trusted
GRAPH cga:SignalIngestion {
    cga:RawSignal_001 a cga:IoTSignal {|
        cga:fromDevice "SN-TEMP-0011"^^xsd:string ;
        cga:verified   "false"^^xsd:boolean ;
        cga:receivedAt "2026-03-15T10:00:00Z"^^xsd:dateTime
    |} .
}

# An assertion — verified, in the event history
GRAPH cga:LabHolon_events {
    cga:Event_042 a cga:DeviceStateEvent {|
        cga:sourceDevice cga:TempSensor_0011 ;
        cga:eventType    cga:ReadingEmitted ;
        cga:deviceTime   "2026-03-15T09:59:58Z"^^xsd:dateTime ;
        cga:causalOrder  42
    |} .
}

# A belief — in the interior graph, confidence-annotated
GRAPH cga:LabHolon {
    cga:LabTemperature cga:currentValue "21.4"^^xsd:decimal {|
        cga:unit              cga:Celsius ;
        cga:beliefConfidence  "0.96"^^xsd:decimal ;
        cga:informedBy        cga:Event_042 ;
        cga:updatedAt         "2026-03-15T10:00:01Z"^^xsd:dateTime
    |} .
}
```

### 3.4 Graph State and World State {#graph-state-world-state}

The CGA does not assume the graph is the world. In the context of digital
twins, IoT integration, and any system where the graph represents physical
entities, the graph is a **representation** of the world — a model
maintained in as close a correspondence as available signals, trust
mechanisms, and update latencies permit.

Three coherence states exist between any digital twin entity's graph
representation and its physical instantiation:

**Synchronised.** Graph state and world state agree within the precision
and latency bounds declared for this entity. This is the normal operational
condition.

**Lagged.** Graph state is accurate but stale: the world has changed and
the corresponding signal has not yet arrived. This is a transient condition
that self-corrects under normal operation. The expected lag is declarable
as a property of the device or sensor type.

**Diverged.** Graph state and world state are inconsistent beyond the
declared tolerance. This is an exceptional condition requiring explicit
resolution. The architecture provides two resolution modes: perceptual
inference (updating the graph to conform to the observed world) and active
inference (acting on the world to bring it into conformance with the graph's
expected state). The choice between them is governed by the precision
weighting model described in Section 9.2.

The twin sync state of an entity is always a first-class property of that
entity in the interior graph — not a monitoring annotation added by a
separate system, but a property as fundamental as its position or status.

### 3.5 Reconciling Open and Closed World Assumptions {#owa-cwa}

One of the defining tensions in semantic web technology is the conflict
between the Open World Assumption (OWA) that governs OWL and the Closed
World Assumption (CWA) that governs databases, most software systems, and
human operational expectations.

The OWA holds that the absence of a statement from a knowledge base does
not entail that the statement is false — it entails only that the statement
is unknown. This is epistemically correct for open, distributed, federated
knowledge environments: the fact that a dataset does not record a
researcher's co-authors does not mean the researcher has no co-authors.

The CWA holds that the knowledge base is complete for the purposes of the
question being asked: if the system has no record of a VP of Engineering,
there is no VP of Engineering in this organisation, for this system's
purposes. This is operationally necessary for constraint enforcement,
business rules, and any system that must make definitive decisions.

The CGA resolves this tension not by choosing one assumption but by
**assigning each assumption to the layer where it is appropriate.**

The OWA governs **domain modelling** at the T-Box layer. The T-Box defines
what kinds of things may exist and how they may be related. An OWL class
`cga:Engineer` does not assert that every engineer in the world has been
described. The domain is genuinely open: there are engineers not yet
represented in any graph, and the existence of an unrepresented engineer
is not a contradiction.

The CWA governs **operational constraint** at the S-Box layer. A SHACL
shape declaring that a conformant spectrometer must have exactly one status
value is evaluated against the graph as presented. For the purposes of
this constraint, if no status is present, the entity is non-conformant —
the system does not entertain the philosophical possibility that a status
might exist somewhere beyond the current graph. Operational correctness
requires this closure.

The **belief model** operates at the interface between these two stances.
A belief state with a confidence below 1.0 is the architecture's explicit
acknowledgement that the closed-world operational layer is working with
a representation that the open-world domain perspective knows to be
incomplete or uncertain. The reifier annotation does not undermine the
CWA at the operational level — rules still fire deterministically against
the interior graph. But it makes the epistemic qualification visible, so
that systems consuming the results of those rules can account for
uncertainty in their downstream decisions.

This principled stratification is the foundation of the CGA's claim to
be both operationally tractable and epistemically honest.

---

## 4. The Architectural Layering Model {#layering-model}

### 4.1 Overview {#layering-overview}

The CGA organises its constituent knowledge into four distinct layers,
each with a characteristic type of content, a governing assumption, and a
primary specification technology. These layers are commonly referred to
as the T-Box, A-Box, S-Box, and X-Box — a nomenclature that extends the
classical Description Logic distinction between terminological and
assertional components.

| Layer | Name | Content | Assumption | Primary Technology |
|---|---|---|---|---|
| T-Box | Terminology | Class and property definitions | OWA | OWL 2, RDFS |
| A-Box | Assertions | Instance data, state, events | CWA (per graph) | RDF 1.2 |
| S-Box | Shapes | Constraints and rules | CWA | SHACL 1.2 |
| X-Box | Vocabulary | Concept schemes, taxonomies | OWA | SKOS |

Each layer plays a distinct role. No layer should contain content properly
belonging to another — this is a governance principle, not a technical
enforcement, but violating it produces architectures that are difficult
to maintain, evolve, or reason about.

### 4.2 The T-Box: Class and Property Definitions {#t-box}

The T-Box defines the vocabulary of the domain: what kinds of things exist,
what properties they may have, and how they are related. In formal terms,
a T-Box comprises class definitions, property declarations, subclass
relationships, domain and range constraints, and other terminological
axioms expressed in OWL 2 or RDFS.

The T-Box operates under the Open World Assumption. It makes no claim
about what instances exist — only about what kinds of instances are
conceivable and what properties they may have. A T-Box declaration that
`cga:Holon` is a subclass of `cga:NamedContext` does not assert that any
particular holon exists; it defines what it would mean for something to
be a holon.

In the CGA, the CGA Vocabulary (Appendix E) provides the foundational
T-Box. Deployers extend this vocabulary with domain-specific class and
property definitions for their particular application domains.

### 4.3 The A-Box: Instance Assertions {#a-box}

The A-Box comprises instance data: the specific entities, relationships,
states, events, credentials, and agent contexts that exist within a
running CGA deployment. Every named graph in a CGA hypergraph — every
holon interior, event history, agent context, ingestion buffer, and
projection graph — is A-Box content.

The A-Box operates under the Closed World Assumption within each named
graph's scope. This means that SHACL constraints and SPARQL queries
against a specific named graph treat that graph as complete for the
purposes of the query. Absence of a triple within a named graph is
operationally significant.

A-Box content in the CGA differs from classical RDF assertion in one
important respect: graph membership is **intentional and governed**. An
entity exists in a named graph because an authorised rule or verified
agent placed it there. The graph membership of an entity is not incidental
— it carries semantic significance that is defined in Section 3.2.

### 4.4 The S-Box: Shapes and Constraints {#s-box}

The S-Box comprises SHACL shapes, constraints, and rules that define the
expected structure of the A-Box content and the valid transitions between
states. The S-Box is, in Active Inference terms, the **generative model**:
it specifies what the system expects the world to look like, and
discrepancies between the S-Box's expectations and the A-Box's current
state are the prediction errors that drive rule firing and state
transitions.

The S-Box operates under the Closed World Assumption. When a SHACL rule
fires, it queries the current A-Box as presented, generates new triples,
and writes them to the appropriate named graphs. This is a deterministic,
one-pass, closed-world operation: the rule does not speculate about states
that might exist beyond the current graph.

The boundary membrane of a holon — the shapes graph that governs what
may exist within the interior and what transitions are valid — is S-Box
content. This is the primary location where operational correctness
is enforced.

### 4.5 The X-Box: Controlled Vocabularies {#x-box}

The X-Box comprises SKOS concept schemes — taxonomies, classification
systems, and controlled vocabularies that provide the shared terminology
referenced by the T-Box, A-Box, and S-Box. Examples include: the
enumeration of valid entity states (`cga:Idle`, `cga:Running`,
`cga:Offline`); the hierarchy of access levels (`cga:Public`,
`cga:Restricted`, `cga:Confidential`, `cga:TopSecret`); the taxonomy of
capability types; the classification of event types; and the spatial index
schemes used for indexed coordinate representation.

The X-Box is significant because it gives these enumerations **structure**
beyond a flat list. A SKOS concept scheme can express that `cga:Running`
is a narrower concept than `cga:BusyState`, enabling SPARQL queries to
ask "find all entities in any busy state" using a property path
(`skos:broader+`) rather than an exhaustive enumeration. This is
particularly valuable for access control hierarchies, where a permission
check that requires level `cga:Restricted` or higher should automatically
encompass `cga:Confidential` and `cga:TopSecret` without the check
enumerating each.

The X-Box is maintained by vocabulary governance processes external to
any individual holon. It is a shared resource across the holarchy.

### 4.6 Inter-Layer Relationships {#inter-layer}

The layers form a dependency hierarchy with a defined direction:

- The **X-Box** provides concept IRIs that are referenced by all other
  layers. It has no dependencies on other layers.
- The **T-Box** defines classes that instantiate X-Box concepts and
  declares properties used in A-Box assertions and S-Box shapes.
- The **S-Box** constrains and transforms A-Box content, referencing
  T-Box classes in `sh:targetClass` declarations and X-Box concepts in
  `sh:in` enumerations and `skos:broader` path queries.
- The **A-Box** instantiates T-Box classes, references X-Box concepts
  as property values, and is governed by S-Box shapes.

This dependency direction is also the governance direction: changes to
the X-Box and T-Box can require updates to the S-Box and A-Box; changes
to the A-Box do not affect the other layers. Schema evolution — addressed
in Section 11.2 — must respect this direction.

### 4.7 Layer Governance Principles {#layer-governance}

Each layer has a characteristic governing authority:

- **T-Box and X-Box** content is maintained by domain authorities:
  standards bodies, vocabulary consortia, or agreed community governance
  processes. Individual deployments consume and extend T-Box and X-Box
  content but do not unilaterally modify shared definitions.
- **S-Box** content for a given holon is maintained by the holon's
  controlling authority, identified by the DID controller property in
  the holon's signature triple.
- **A-Box** content is generated by authorised rules (S-Box rules
  acting on verified events) and verified agents within the trust
  model described in Section 7.

---

## 5. The Holon Model {#holon-model}

### 5.1 Holons as the Fundamental Unit {#holons-fundamental}

The concept of the holon, introduced by Arthur Koestler in *The Ghost in
the Machine* (1967), addresses a problem that is genuinely difficult to
articulate in conventional terms: what do we call something that is
simultaneously a complete, self-governing whole and a dependent part of
a larger system?

A cell is a complete living system with its own metabolism and regulatory
mechanisms. It is also a component of a tissue, which is a component of
an organ, which is a component of an organism. None of these levels
cancels the others. The cell does not stop being a whole because it is
part of something larger. This dual nature — Koestler called it the
Janus nature, after the two-faced Roman god — is the defining property
of a holon.

The same structure appears everywhere in organised complexity: in
language (words within sentences within documents), in organisations
(departments within divisions within corporations), in physical space
(rooms within buildings within cities within regions), and in
information systems (records within tables within databases within
data ecosystems).

The CGA adopts the holon as its fundamental architectural unit because
it is the right abstraction for systems that are simultaneously
self-governing (with their own internal state, rules, and identity) and
relationally embedded (within containing systems that have their own
state and rules).

In CGA terms, a holon is defined by four properties:

1. A **persistent IRI** that serves both as the entity's identity and
   as the address of its interior named graph.
2. A **shapes graph** that governs the interior — the boundary membrane.
3. An **exterior graph** — the representation of the holon in the parent
   scene graph, generated by controlled projection.
4. A **controller DID** identifying the authority responsible for the
   holon's governance.

A named graph that satisfies these four conditions is a conformant holon
interior in CGA terms.

### 5.2 The Interior/Exterior Distinction {#interior-exterior}

The most fundamental distinction in the holon model is between interior
and exterior. The interior is the holon's world as it knows itself. The
exterior is the holon's representation as it appears to others.

The interior contains full operational state: the complete scene graph,
the state annotations of all entities, the event history references,
the agent context graphs of current occupants. It is governed by the
CWA and is the graph against which S-Box rules fire.

The exterior contains only what the holon chooses to project: summary
labels, aggregate counts, access level declarations, spatial footprints,
and portal representations. It is generated by a SHACL rule that
constructs the exterior graph from the interior. The exterior is never
directly writable — it is always derived.

This distinction has two important consequences.

First, the interior can be **access-controlled independently of the
exterior**. The exterior of a vault holon in the parent building's scene
graph — a node labelled "The Vault" with an access level annotation and
a spatial footprint — is visible to all agents in the building. The
interior of the vault, which contains the actual asset descriptions, is
only accessible to agents who have satisfied the portal access conditions.

Second, the relationship is **one-directional**. The exterior is derived
from the interior; information does not flow from the exterior into the
interior. An agent observing the exterior of a holon knows only what that
holon has chosen to project. This is the technical realisation of
Koestler's self-assertive tendency: the holon controls its own
representation.

### 5.3 Boundary Membranes {#boundary-membranes}

The boundary membrane is the shapes graph of a holon. It performs three
distinct functions, all using SHACL 1.2 mechanisms.

**Structural validation.** The membrane declares, via `sh:targetClass`
and `sh:rootClass`, what classes of entity may exist within the interior.
An entity that does not conform to the interior's shapes is rejected —
it cannot be asserted into the interior named graph without triggering
a validation violation.

**Operational constraint.** The membrane's property shapes constrain
the valid states and relationships of entities within the interior.
A shape that declares `sh:minCount 1` on a status property for all
entities of a given type ensures that no entity of that type can exist
in the interior without a status value. The CWA guarantees that this
constraint is meaningful: absence of a value is non-conformance, not
unknown conformance.

**Portal access control.** The membrane includes shapes governing the
conditions under which agents may traverse portals into and out of the
interior. These shapes use `sh:if`/`sh:then`/`sh:else` to express
conditional access logic — if the portal is locked, then a valid
credential of the required type must be present; otherwise, the
portal's state must be open or unlocking.

The boundary membrane is declared in the holon's signature triple and
is addressable via the holon's DID document service endpoint, making
it discoverable by any agent that has resolved the holon's DID.

### 5.4 Holarchies: Whole/Part Relationships {#holarchies}

Holons are organised into holarchies through the `cga:parentHolon`
relationship, declared in the reifier annotation of each holon's
self-referential signature triple. A city holon contains district holons,
which contain building holons, which contain room holons. A document holon
contains chapter holons, which contain section holons. An organisation
holon contains department holons.

This hierarchical structure carries consequences at every level:

- **Shapes cascade.** A parent holon's shapes graph may define defaults
  that child holon shapes inherit or override, analogous to CSS cascade.
  This is currently underspecified and is a priority area for the
  normative CGA-Holon specification.
- **Events propagate.** A state change event in a child holon that is
  significant at the parent level generates a derived event in the parent
  holon's event history. The mechanism for this propagation is defined
  in CGA-Event.
- **Identities compose.** An agent's position within the holarchy is
  described by a path of holon IRIs: city → district → building → room.
  This path is the agent's full current address, not just the innermost
  holon.

The holarchy also provides the structural basis for the hierarchical
generative model described in Section 9.5: each level of the holarchy
corresponds to a level of the predictive hierarchy, with higher levels
handling slower, more abstract predictions and lower levels handling
faster, more concrete ones.

### 5.5 The Scene Graph as Interior Model {#scene-graph}

The interior of a holon is a **scene graph** in the graph-theoretic sense:
a collection of entities with positions in a coordinate space and
relationships to one another. The term is borrowed from computer graphics
and game engine design, where a scene graph is the primary data structure
for representing the spatial and relational organisation of a virtual
environment.

In the CGA, the scene graph is not a visualisation construct. It is an
operational data model. Its nodes are addressable IRIs; its edges are
typed RDF properties; its spatial annotations are typed literals using
GeoSPARQL's `geo:wktLiteral` for continuous spatial representation, and
SKOS concept IRIs in indexed spatial classification schemes (H3, S2,
postal codes) for indexed representation.

The CGA explicitly supports **multiple simultaneous coordinate systems**
within a single holon interior. An entity may have a continuous WGS84
position (for geographic accuracy), an H3 cell classification (for
indexed spatial queries), and an administrative region membership (for
policy application). These are all simultaneously valid and non-redundant
descriptions of the entity's spatial position. SPARQL queries can use
any of them, and the X-Box spatial index scheme provides the hierarchy
that relates them.

Importantly, the scene graph model extends beyond spatial holons. A
document's interior scene graph has chapter entities with ordinal
positions. An organisation's interior scene graph has role entities with
reporting relationships. The "coordinate system" for a document interior
is ordinal position within a table of contents; for an organisation, it
might be a combination of reporting hierarchy and functional classification.
The scene graph abstraction unifies all of these.

### 5.6 Self-Referential Identity {#self-referential-identity}

Every conformant holon interior contains a triple asserting the holon's
own identity within itself, annotated with structural metadata via the
`{| |}` reifier. This is the holon's signature — its declaration that
the named graph knows what it is and what it belongs to.

```turtle
GRAPH cga:LabHolon {

    cga:LabHolon a cga:Holon {|
        cga:schemaVersion  "1.0" ;
        cga:validatedBy    cga:LabHolon_shapes ;
        cga:createdAt      "2026-03-15T09:00:00Z"^^xsd:dateTime ;
        cga:parentHolon    cga:BuildingHolon ;
        cga:controller     <did:web:lab.example.org>
    |} ;
        rdfs:label          "The Lab"@en ;
        cga:coordinateSystem cga:LocalCartesian ;
        geo:hasGeometry     [
            a geo:Geometry ;
            geo:asWKT "POLYGON((0 0, 0 8, 10 8, 10 0, 0 0))"^^geo:wktLiteral
        ] .
}
```

The self-referential triple is not circular in a problematic sense. The
graph name as subject is an IRI; making statements about that IRI within
the graph it names is a well-defined operation in RDF 1.2. The reifier
annotation carries metadata about the holon as a whole — its schema
version, its parent, its controller — without those facts polluting the
scene graph content.

The `cga:controller` annotation links the holon to a DID — the
cryptographically verifiable identifier of the authority responsible
for the holon's shapes graph and governance. Key rotation, authority
transfer, and decommissioning are all managed through DID infrastructure
without requiring changes to the interior named graph.

---

## 6. The Context Graph Model {#context-graph-model}

### 6.1 What Is a Context Graph {#what-is-context-graph}

The term "context graph" requires precise definition, because "context"
is one of the most overloaded terms in computing. In the CGA, a context
graph is specifically a named graph that carries **interpretation context**
— the framing information needed to resolve the meaning of terms, policies,
and state values at a specific decision or interaction point.

The motivating problem is contextual misalignment: two parties using the
same term to mean different things, or the same concept encoded differently
in different knowledge bases. The term `cga:Senior` might mean "ten or
more years of experience" in one organisation's knowledge graph and
"fifteen or more" in another's. A policy asserting that senior staff
have access to certain resources cannot be correctly applied across
organisational boundaries without a context graph that records how
`cga:Senior` is to be interpreted in each context.

A context graph is therefore not a content graph (which describes what
exists) but a **framing graph** (which describes how content should be
interpreted). It carries:

- **Term mappings** — how terms in one vocabulary correspond to terms in
  another
- **Policy framings** — under what conditions a policy applies and what
  its terms mean in this context
- **Temporal framings** — what valid time period this context applies to
- **Agent framings** — what role, capability, or identity context an
  agent is operating under

### 6.2 Context Graphs and Holons {#context-graphs-and-holons}

Context graphs and holons are distinct but related concepts, and the
distinction matters architecturally.

A holon is **ontological** — it is about what entities exist, where they
are, and what state they are in. A context graph is **epistemic** — it
is about how to interpret what is being said about those entities.

The overlap occurs at holon boundaries: when an agent crosses a portal
from one holon to another, the context of interpretation may shift. Terms
that meant one thing in the source holon may mean something slightly
different in the target holon. The portal traversal event is not just a
spatial transition — it is potentially a contextual transition, requiring
the agent's context graph to be updated to reflect the new interpretive
frame.

An agent's context graph is therefore a persistent companion to the agent's
position in the holarchy: it records not just where the agent is, but what
interpretive frame the agent is currently operating under at each level of
the holarchy. A city-level context graph might define how urban policy terms
are interpreted; a district-level context graph might refine those terms
for the specific regulatory environment of this district; a building-level
context graph might further qualify them for the building's operational
domain.

The CGA does not require that every holon boundary corresponds to a context
graph boundary. For holons within a single organisational context with
consistent vocabulary, a single context graph may serve the entire holarchy.
Context graph boundaries are introduced where contextual misalignment is
actually present or anticipated.

### 6.3 Contextual Misalignment and Resolution {#contextual-misalignment}

Contextual misalignment is the condition in which the same symbolic
expression carries different meanings in different operational contexts.
It is the root cause of many integration failures: systems that exchange
data without exchanging the interpretive context in which that data was
produced.

The CGA provides a formal detection and resolution mechanism for
contextual misalignment, using the X-Box SKOS vocabulary layer.

**Detection** occurs when a cross-context query returns results that
differ depending on which context graph is active — indicating that the
same query resolves differently in different contexts.

**Resolution** proceeds through SKOS alignment properties. The strongest
form of alignment is `skos:exactMatch`, asserting that two concepts in
different schemes are interchangeable. Weaker alignments — `skos:closeMatch`,
`skos:broadMatch`, `skos:narrowMatch` — express partial overlap and
require explicit handling: a query that resolves via a `skos:closeMatch`
alignment should flag this in its result annotations, because the match
is approximate, not exact.

The full specification of contextual misalignment detection and resolution
algorithms is delegated to the normative CGA-Context specification.

### 6.4 Context Propagation Across Boundaries {#context-propagation}

When a focus agent traverses a portal from one holon to another, the
agent's context graph must be examined and potentially updated to reflect
the new interpretive environment. This context propagation is a first-class
event in the event history — not a silent background operation.

Context propagation has two phases. The **outbound phase** records the
agent's departure from the current context, preserving the current context
graph as a snapshot. The **inbound phase** evaluates the target holon's
context requirements, applies any necessary term mappings, and updates the
agent's context graph to reflect the new interpretive frame.

If context propagation fails — because the agent's current context is
incompatible with the target holon's required context in ways that cannot
be resolved by SKOS alignment — the portal traversal fails with a context
incompatibility error, which is a distinct failure mode from a credential
rejection. The agent lacks not the right to enter, but the interpretive
frame to operate correctly within.

---

## 7. The Trust Model {#trust-model}

### 7.1 Sources of Assertion {#sources-of-assertion}

The CGA receives information from four categories of source. Each category
has a distinct trust chain, a distinct verification mechanism, and distinct
failure modes.

**Human agents** are principals who interact with the system through
interfaces — applications, portals, command systems. Their identity is
established via Decentralised Identifiers; their authority to perform
specific actions is established via Verifiable Credentials. Human agent
assertions are generally high-trust but require the full two-phase
verification pipeline before their claims enter the interior graph.

**Autonomous agents** are software principals that operate without direct
human oversight, typically executing predefined policies or learned
behaviours. They are identified by DIDs and carry VCs asserting their
capabilities and authorisations. The trust level of an autonomous agent
is not necessarily lower than that of a human agent — it depends on the
quality of the credentials it carries and the authority that issued them.

**IoT devices and sensors** are physical or virtual instruments that emit
state signals at defined frequencies. They are identified by device IRIs
in a device registry, not DIDs (though high-assurance devices may use
DIDs). Their signals are trusted to the degree that the device registry
certifies them, weighted by the device's historical signal quality. Device
signals enter the ingestion pipeline as observations and are promoted to
assertions only after validation.

**SHACL rules** generate derived assertions as a product of rule firing
against the current graph state. A rule-derived triple carries implicit
trust from the validated rule set that produced it: if the rules are
correct and the input assertions are trusted, the derived assertions are
trusted at the same level. The provenance of a rule-derived triple is
recorded in its reifier annotation, identifying the rule that produced it.

### 7.2 Trust Levels {#trust-levels}

The architecture uses a hierarchical trust level vocabulary, expressed as
a SKOS concept scheme, to classify assertions by the strength of their
provenance. The levels, from lowest to highest:

**Unverified** (`cga:Unverified`). A raw signal in the ingestion graph.
No assertional status.

**Device-Attested** (`cga:DeviceAttested`). A signal from a registered
device that has passed sequence and plausibility validation but has not
been cryptographically verified. Suitable for non-critical operational
telemetry.

**Credential-Trusted** (`cga:CredentialTrusted`). An assertion from a
principal whose identity has been verified via DID resolution and whose
claims are supported by Verifiable Credentials from trusted issuers.
The standard trust level for human and autonomous agent assertions.

**Cryptographically Verified** (`cga:CryptoVerified`). An assertion
whose content has been verified against a cryptographic signature using
a published verification method. Includes VC Data Integrity Proofs
(EdDSA-rdfc-2022, BBS+) and high-assurance device attestations.

**Rule-Derived** (`cga:RuleDerived`). An assertion generated by a
validated SHACL rule set. Trust is inherited from the input assertions
and the validation status of the rule set itself.

The trust level of an assertion is carried in its reifier annotation
and is a precondition check for many S-Box rules: a rule that modifies
critical state may require that the triggering event carry at least
`cga:CryptoVerified` trust.

### 7.3 The Trust Boundary as Architectural Primitive {#trust-boundary}

The trust boundary is the explicit interface at which unverified
observations become trusted assertions. It is not a single system
component but an architectural concept that must be implemented as an
explicit, auditable process.

The trust boundary has two sides. On the left: raw signals, unverified
claims, and preliminary observations — all residing in the ingestion
graph. On the right: verified assertions in the event history and derived
state in the interior graph. Nothing moves from left to right without
passing through a documented verification process. Every crossing is
recorded in the event history.

The trust boundary is implemented as a two-step pipeline: a validation
step (cryptographic verification of signatures, sequence number checking,
plausibility filtering) followed by a promotion step (a SPARQL UPDATE
that writes the verified assertion into the event history graph). The
promotion step is the moment at which an observation becomes an assertion.

The trust boundary principle implies that no system component may write
directly to the interior graph. All writes to the interior graph are
mediated by SHACL rules that read from the event history. The interior
graph is always the product of rules operating on trusted events, never
the direct product of external signals.

### 7.4 Verified Identity: DIDs and the Agent Model {#did-agent-model}

The CGA adopts the W3C Decentralised Identifiers (DID) Core 1.0
specification as the identity layer for both holons and agents. A DID
is a globally unique, cryptographically verifiable, controller-independent
identifier that can be resolved to a DID document containing public keys,
verification methods, and service endpoints.

For holons, the DID serves as the holon's canonical IRI. When resolved,
the DID document provides the holon's public key material (for verifying
assertions attributed to the holon), the service endpoint for the
boundary membrane shapes graph, and the controller relationship (the
DID of the authority responsible for the holon).

For agents, the DID serves as the agent's persistent identity across all
holons they may inhabit. An agent presents the same DID regardless of
which holon they are currently in; their context graph carries the
accumulated verification history from all holons they have passed through.

The key rotation property of DIDs is architecturally significant: when
a holon's controlling authority changes, the DID document is updated
with new key material, but the holon's IRI — and therefore all historical
references to it — remains stable. The graph history is not disturbed
by authority changes.

### 7.5 Credential-Based Access: Verifiable Credentials {#verifiable-credentials}

The CGA adopts the W3C Verifiable Credentials Data Model 2.0 as the
mechanism for asserting claims at portal boundaries and in access control
decisions.

A Verifiable Credential is a set of claims made by an issuer about a
subject, cryptographically signed by the issuer and presentable by the
subject to verifiers. In CGA terms, the typical flow is:

1. A portal's boundary membrane declares that access requires a credential
   of a specific type (e.g., a `cga:ResearcherCredential` with a
   `cga:hasAffiliation` claim).
2. The focus agent's wallet selects a VC satisfying the requirement and
   derives a presentation — potentially using selective disclosure (BBS+
   cryptosuite) to reveal only the required claims, not the full credential.
3. The presentation is verified against the issuer's public key (obtained
   via DID resolution).
4. On successful verification, the verified claims are injected into the
   agent's context graph as trusted assertions, via the trust boundary
   protocol.
5. The portal's SHACL access shape queries the agent's context graph
   for the required claims and, finding them, permits traversal.

The critical architectural point is that the VC itself is never written
into the interior graph. What enters the graph is the **verified claim**
— the semantic content of the credential, expressed as RDF triples,
attributed to the issuing DID, and annotated with the verification
timestamp and method. The cryptographic envelope around the claim is
consumed at the trust boundary; the semantic content crosses it.

### 7.6 Device Signals and the IoT Trust Chain {#iot-trust-chain}

IoT devices occupy a distinct position in the trust model because they
cannot, in general, present Verifiable Credentials for each signal they
emit. A temperature sensor emitting readings at one-second intervals
cannot perform a full DID-based verification per reading.

The IoT trust chain therefore relies on a **device registry** — a named
graph in the hypergraph that records the registered devices, their trust
levels, their signal schemas, their expected frequencies, and their
historical precision metrics. A device is registered by its controller
(a human agent with appropriate credentials) at deployment time, and
its signals are trusted to the degree that the registry certifies.

Signal validation checks three conditions: that the signal's source
device is in the registry with a trust level above the minimum required
for this type of signal; that the signal's sequence number is consistent
with the expected ordering from this device; and that the signal's
timestamp is within a plausible window given the expected signal frequency
and known network latency for this device type.

Precision weighting — the ongoing assessment of how reliable a device's
signals are — is described in Section 9.2. A device that consistently
produces accurate signals accumulates a high precision weight; a device
showing calibration drift or communication errors accumulates a lower
weight, and the confidence annotations on the beliefs derived from its
signals reflect this.

### 7.7 Derived Assertions and Rule-Generated Triples {#derived-assertions}

SHACL rules generate new triples as their output. These triples are
assertions, but their provenance is different from agent-asserted or
device-attested facts: they are the product of an inference process
operating on trusted inputs.

The architecture treats rule-derived triples as trusted at the level of
their weakest input, bounded by the validation status of the rule set
itself. A rule operating on `cga:CryptoVerified` inputs and producing
an output triple annotates that triple's reifier with `cga:RuleDerived`
and a reference to the specific rule that produced it. This traceability
is required — a rule-derived triple without a provenance reference is
non-conformant.

The implication is that rule sets must themselves be versioned and
validated. The shapes graph that governs a holon is S-Box content with
its own version annotation; rules derived from version 1.0 of the
shapes graph are distinguishable from rules derived from version 1.1,
and the event history records which version was active when each
rule-derived triple was produced.

---

## 8. State, Event, and Transition {#state-event-transition}

### 8.1 The Event Sourcing Commitment {#event-sourcing}

The CGA is committed to the event sourcing pattern as the basis for state
management. This is an architectural commitment with significant
consequences, and it is worth stating plainly.

**Current state is always derived, never authoritative.** The
authoritative record of a holon's history is its event history graph —
an append-only log of every ContextEvent that has affected the holon.
The interior graph — the operational state that agents query and rules
fire against — is a materialised view derived from the event history
by the S-Box rule machinery.

This commitment delivers four properties that are difficult or impossible
to achieve in architectures that treat current state as primary:

**Auditability.** Every state in the interior graph can be traced to the
events that produced it. "How did the spectrometer come to be in the
Running state?" is answered by querying the event history.

**Replay.** If the S-Box shapes graph is updated, the new shapes can be
applied to the existing event history to reconstruct the current state
under the new rules. Schema migration is a replay operation, not a
data transformation.

**Recovery.** If the interior graph is corrupted or lost, it can be
reconstructed from the event history. The event history is the single
source of truth.

**Temporal query.** "What was the state of the holon at a specific
past time?" is answered by replaying the event history up to that time.
This is bi-temporal querying by construction.

The event sourcing commitment does impose a cost: the interior graph is
a derived structure that must be kept synchronised with the event history.
The S-Box rule machinery provides the synchronisation mechanism; the
system must ensure that rules fire promptly and that the interior graph
is not queried against stale event history.

### 8.2 State as Derived from History {#state-derived}

The current state of an entity in the interior graph is the result of
applying the S-Box rule set to the event history in causal order. Each
event selects focus nodes, evaluates preconditions, and generates new
triples that supersede the previous state. The architecture requires
that state triples carry version annotations in their reifier, enabling
rules to verify that they are operating against the current version of
a triple before updating it.

```turtle
# A state triple with version annotation in the interior graph
GRAPH cga:LabHolon {
    cga:Spectrometer cga:status cga:Idle {|
        cga:version        "7"^^xsd:integer ;
        cga:enteredStateAt "2026-03-15T10:00:00Z"^^xsd:dateTime ;
        cga:priorState     cga:Running ;
        cga:derivedFrom    cga:Event_089 ;
        cga:ruleVersion    "cga:SpectrometerRules_v1.2"
    |} .
}
```

The `cga:version` annotation is the optimistic concurrency token.
A SHACL rule that updates this triple must include a WHERE clause
precondition checking that the current version matches the version
it is operating against. A mismatch indicates a concurrent update has
occurred; the rule generates a conflict event rather than a silent
overwrite.

### 8.3 The Causal Order Model {#causal-order}

Events in a CGA system are not necessarily totally ordered — in a
multi-device, multi-agent environment, events occur concurrently and
arrive at the system in an order that may not reflect their occurrence
order. The architecture adopts a **partial order** model for events.

Events from a single source (a specific device or agent) are totally
ordered by causal sequence number. Events from different sources are
partially ordered: they are concurrent unless one event explicitly
depends on another. This is represented in the event history graph
by the `cga:priorEvent` annotation — an event that has a `cga:priorEvent`
reference to another event is causally downstream of it; events without
a shared `cga:priorEvent` ancestry are concurrent.

The causal sequence number is assigned at promotion time (when an
observation becomes an assertion) using the device or agent timestamp
as the primary ordering key. This is a Lamport-clock-like mechanism:
it does not require global clock synchronisation, only that each source
maintains a monotonically increasing sequence number.

### 8.4 Temporal Semantics: Two Kinds of Time {#temporal-semantics}

The CGA records two distinct timestamps for every event:

**Transaction time** (`cga:recordedAt`) — when the event was recorded
in the system. This is under the system's control and is always a
monotonically increasing value.

**Valid time** (`cga:deviceTime` or `cga:assertedAt`) — when the
described state was actually true in the world. This comes from the
signal source and may precede the transaction time by a latency interval.

The distinction matters for historical queries. "What did the system
believe about the spectrometer at 10:00?" (transaction time query) and
"What was the spectrometer's actual state at 10:00?" (valid time query)
may have different answers if a signal was delayed and arrived after 10:00
but described a state that existed before it.

All queries producing historical reconstructions must specify which
temporal dimension they are querying. Conflating transaction time and
valid time is a common source of incorrect historical analysis.

### 8.5 Partial Ordering Under Asynchrony {#partial-ordering}

In IoT and multi-agent environments, signals routinely arrive out of
order. The architecture handles this through a **hold-and-release**
mechanism in the ingestion pipeline: when a signal arrives with a
sequence number that implies a gap (sequence 49 arriving when the last
processed sequence was 47 — signal 48 is missing), the system holds
the out-of-order signal and waits for the missing signal within a
declared tolerance window.

If the missing signal arrives within the window, both are processed in
causal order. If the window expires, a `cga:MissingSignalEvent` is
inserted into the event history to record the gap explicitly, and the
held signal is promoted with the gap noted in its reifier annotation.
The gap event is a permanent part of the record — it is not retrospectively
removed if the missing signal eventually arrives through a secondary
channel.

This mechanism ensures that the event history is always a complete,
auditable record of what the system knew and when, including the explicit
documentation of what was not known.

### 8.6 State Machine Semantics {#state-machine-semantics}

The CGA's state machine model maps directly onto SHACL 1.2 constructs.

Each **state** is a concept in an X-Box SKOS concept scheme. The valid
states of a given entity class form a concept scheme that may be
hierarchically organised — for example, all active states may be narrower
concepts under `cga:ActiveState`, enabling queries that ask "is this
entity in any active state" using a `skos:broader` path rather than
an enumeration.

Each **transition** is a SHACL rule with an ordering value. The rule's
WHERE clause specifies the preconditions (current state, event type,
trust level, credential requirements). The rule's CONSTRUCT clause
specifies the state update (new state triple with incremented version
and provenance annotation) and any side effects (actuation commands,
derived events, projection updates).

The **ordering** of rules within a transition sequence (`sh:order 10,
20, 30`) defines the execution schedule. Lower-order rules fire first
and their outputs are visible to higher-order rules within the same
pass. The single-pass, closed-world guarantee of SHACL rule execution
ensures that a transition sequence executes atomically from the graph's
perspective: no external writes can interleave with a rule sequence in
progress.

---

## 9. Belief, Uncertainty, and Inference {#belief-uncertainty}

### 9.1 Belief States and Confidence Annotation {#belief-states}

The architecture acknowledges that not all state triples carry equal
certainty. A triple derived from a high-precision sensor with a verified
calibration history carries near-maximum confidence. A triple derived
from a sensor known to be degrading, or from a device signal that arrived
late and was the only data point in a volatile measurement period, carries
lower confidence.

The confidence annotation is carried in the state triple's reifier:

```turtle
GRAPH cga:LabHolon {
    cga:LabTemperature cga:currentValue "21.4"^^xsd:decimal {|
        cga:beliefConfidence  "0.96"^^xsd:decimal ;
        cga:observationWeight "0.7"^^xsd:decimal ;
        cga:priorWeight       "0.3"^^xsd:decimal ;
        cga:priorValue        "21.2"^^xsd:decimal ;
        cga:informedBy        cga:Event_042
    |} .
}
```

The `cga:observationWeight` and `cga:priorWeight` are the Bayesian
mixing coefficients: 70% of the belief is driven by the current
observation, 30% by the prior. For a well-calibrated sensor in a stable
environment, the observation weight will be high. For a sensor showing
calibration drift, the prior weight will be higher, meaning the system
trusts recent history more than the current reading.

The practical effect is that S-Box rules can be written to behave
differently depending on the confidence of their input beliefs — raising
an alert rather than taking action when confidence is below a threshold,
or flagging a result as approximate when the input beliefs are qualified.

### 9.2 Precision Weighting and Signal Reliability {#precision-weighting}

The precision of a signal source is the architecture's representation
of how reliable that source has historically been. Precision is maintained
per device in the device registry and updated based on signal history.

A device with consistently accurate signals (where "accurate" means
confirmed by subsequent higher-quality observations or physical inspection)
accumulates a high precision weight. A device showing systematic bias or
high variance accumulates a lower precision weight. Precision weighting
determines how strongly a new signal from this device shifts the belief
state of the entities it reports on.

Precision weighting has a direct operational consequence for the resolution
of twin sync divergence. When the graph state and physical world state
disagree:

- **High precision signal:** The observation is trusted. The system
  updates the graph to reflect the physical world (perceptual inference).
- **Low precision signal:** The observation is not trusted. The system
  considers acting on the world to bring it into line with the graph's
  expected state (active inference), or flagging the divergence for
  human review.

This choice — trusting the signal or trusting the model — is the
fundamental trade-off in any sensor-driven system. The CGA makes it
explicit and auditable rather than burying it in application code.

### 9.3 The Generative Model Correspondence {#generative-model}

The architecture has an informative correspondence with Karl Friston's
Free Energy Principle and Active Inference framework. Understanding this
correspondence helps explain why the architecture has the structure it has.

In Active Inference, a system that persists through time maintains an
internal **generative model** — a specification of what it expects the
world to look like. The system continuously compares its model's
predictions against observations, and responds to discrepancies (prediction
errors) by either updating the model (perceptual inference) or acting on
the world to reduce the discrepancy (active inference).

In the CGA:

- The **S-Box shapes graph** is the generative model — it specifies what
  conformant state looks like for every entity type.
- A **SHACL validation violation** is a prediction error — the world has
  surprised the model.
- The **SHACL rule pipeline** is the active inference mechanism — it fires
  in response to prediction errors and produces state changes that reduce
  the divergence.
- The **trust boundary** separates the system's predictions from the
  observations it uses to update them.
- The **holarchic structure** corresponds to a hierarchical generative
  model, where each level of the hierarchy handles prediction errors at
  its characteristic timescale and abstraction level.

This correspondence is informative, not normative. The CGA does not
require implementers to use variational inference machinery or to
implement Friston's mathematics. The architecture has the structure it
has because that structure produces correct, auditable, self-correcting
behaviour — the Active Inference framing explains *why* that structure
is correct.

### 9.4 The Discrete/Continuous Interface {#discrete-continuous}

The graph layer of the CGA is discrete and deterministic. State triples
have point values, confidence annotations are scalar decimals, and SHACL
rules fire deterministically. This is appropriate for most operational
scenarios and is compatible with the CWA requirements of the S-Box layer.

For systems with genuinely high-dimensional continuous state spaces —
robotic systems with many degrees of freedom, environmental monitoring
with complex spatial correlation structures, or systems where uncertainty
is not well-approximated by a single confidence scalar — a more
sophisticated probabilistic inference layer may be required.

The architecture explicitly accommodates, but does not specify, an
external probabilistic inference layer that computes maximum a posteriori
(MAP) estimates from raw signal streams and writes them into the graph as
confidence-annotated belief triples. The interface between this layer and
the graph is:

- **Input to inference layer:** Raw observations from the ingestion graph,
  prior belief triples from the interior graph.
- **Output from inference layer:** Updated MAP estimate triples with
  confidence annotations, written into the event history as verified
  assertions.
- **Governance of the interface:** The inference layer must be registered
  as a trusted source in the device registry; its outputs must pass through
  the trust boundary pipeline like any other assertion.

The inference machinery itself — Kalman filters, particle filters,
variational autoencoders — is out of scope for this specification. The
interface requirements are specified in CGA-Twin.

### 9.5 Hierarchical Structure and Inference {#hierarchical-inference}

The holarchic structure of the CGA has a natural correspondence with
hierarchical generative models in predictive processing theory. At each
level of the holarchy, the containing holon's shapes graph specifies
expectations about the aggregate behaviour of its child holons. The
child holons' shapes graphs specify finer-grained expectations about
individual entities within them.

This hierarchy of expectations enables the architecture to handle
prediction errors at the appropriate level:

- A room-level spectrometer status anomaly is handled by room-level
  SHACL rules; it generates a room-level event and updates the
  room-level interior graph.
- If the anomaly persists and affects the building's aggregate resource
  usage, a derived event propagates to the building-level event history,
  where building-level rules can respond.
- If the building-level anomaly affects city-level infrastructure planning,
  a further derived event propagates upward.

This upward propagation of unresolved prediction errors — combined with
the downward flow of contextual expectations from parent holons to child
holons via exterior projections — is the holarchic implementation of
hierarchical predictive processing. Lower levels handle high-frequency,
fine-grained predictions; higher levels handle slower, more abstract ones.

---

## 10. The Presentation and Perception Model {#presentation-model}

### 10.1 The Projection Graph as Derived View {#projection-graph}

An agent inhabiting a holon interior does not perceive the full interior
graph. Physical and contextual constraints limit what is within range,
what the agent is authorised to observe, and what is currently salient
given the agent's goals and context. The projection graph is the CGA's
mechanism for representing these constraints formally.

A projection graph is a named graph derived from the interior graph by
a SHACL rule that takes into account the agent's current position, their
perceptual radius (the distance within which they can observe entities),
their access level, and the current state of the entities within range.
The projection graph contains only what this specific agent can currently
perceive, with position and state information appropriate to their vantage.

Projection graphs are ephemeral and agent-specific. They are regenerated
whenever the underlying interior graph changes in a way that affects the
agent's perception. The delta between successive projection states — not
the full projection graph — is what is transmitted to the client for
presentation.

### 10.2 Appearance as Declarative Style {#appearance-graph}

The appearance graph separates the representation of entities from their
data. Just as CSS separates visual presentation from HTML structure, the
CGA's appearance graph separates how entities are presented from what
they are and what state they are in.

The appearance graph is declarative and cascading. At the top of the
cascade are class-level appearance declarations — what a generic entity
of type `cga:Spectrometer` looks like by default. Below that are
instance-level overrides for specific entities. At any level, appearance
declarations may be conditional: `cga:appliesWhen cga:Operational` means
this appearance applies only when the entity's status matches the declared
condition.

The cascade resolution — selecting the highest-priority applicable
appearance declaration for each entity — is performed by a SHACL rule
that writes the resolved appearance into the projection graph. The client
receives a projection graph that contains not only what entities are
visible and where, but also how they should be presented.

The appearance graph is client-agnostic. A client rendering a 3D spatial
environment interprets appearance properties differently from a client
rendering a tabular data view or a voice interface. The appearance graph
declares semantic presentation properties (`cga:operationalState`,
`cga:accessLevel`, `cga:currentActivity`); the client maps these to
its specific presentation modality.

### 10.3 Perception as Agent-Relative Projection {#agent-perception}

The multi-agent case requires that different agents simultaneously
inhabiting the same holon may perceive different projections of it.
Agent A, standing at one position in the scene graph, perceives a
different set of entities than Agent B, standing at another position.
An agent with higher access clearance perceives entities invisible to
an agent with lower clearance.

Each agent has their own projection graph, maintained independently and
updated independently. The interior graph is shared; the projection
graphs are private.

This has implications for agent identity. When Agent B appears in Agent A's
projection, what Agent A sees of Agent B is not Agent B's full context graph
— it is Agent B's exterior representation within this holon: a projection
of Agent B's current position, visible status, and whatever aspects of
identity Agent B has chosen to make visible in this context. Agent B's
full identity, carried items, and context graph are not exposed to other
agents through the projection mechanism.

### 10.4 The Delta Subscription Model {#delta-subscription}

A client maintaining a presentation of a holon cannot re-query the full
projection graph on every interior state change — this would be prohibitively
expensive for high-frequency IoT environments or densely populated holons.

The CGA adopts a delta subscription model: the client establishes a
subscription to an agent's projection graph and receives incremental
updates — additions and deletions of triples — rather than full projection
snapshots. The format for these delta updates is RDF Patch, a community
draft format for expressing incremental changes to named graphs.

The delta stream is scoped to a single agent's projection graph. The
client does not receive updates for interior graph changes that do not
affect the agent's current projection — changes to entities outside
the agent's perceptual radius, state changes to entities the agent is
not authorised to observe, or appearance changes for entity classes
not currently represented in the agent's view.

### 10.5 Multi-Agent Consistency {#multi-agent-consistency}

When multiple agents inhabit the same holon interior simultaneously, the
architecture must address the consistency of their shared view of the
interior graph. Two agents observing the same entity at the same time
should see the same state for that entity (modulo their respective access
levels), even though they are receiving separate delta streams to their
separate projection graphs.

The architecture achieves this through the event sourcing model: the
interior graph is the single source of truth, and all projection graphs
are derived from it. As long as each agent's projection is derived from
the same interior graph state, consistency is guaranteed by construction.

The challenge arises at the boundary of state transitions: when an event
triggers a state change in the interior graph, the delta must be
propagated to all affected projection graphs before any agent can act
on the new state. The architecture requires that projection graph updates
be atomic with the interior graph update — they occur in the same SHACL
rule pass, not as a subsequent operation.

---

## 11. Lifecycle and Governance {#lifecycle-governance}

### 11.1 Entity and Holon Lifecycle {#entity-lifecycle}

Every entity and holon in a CGA system progresses through a defined
lifecycle. The canonical stages are:

**Created.** The entity's IRI has been minted and its signature triple
asserted in the appropriate interior graph. Initial state triples are
present. The entity is not yet active in the scene graph.

**Active.** The entity is a full participant in the holon's scene graph —
visible in projection graphs (subject to access constraints), responsive
to events, and governable by the S-Box rule machinery.

**Suspended.** The entity is temporarily inactive. It remains in the
interior graph but is excluded from projection graph generation. Events
addressed to it are queued rather than processed. Suspension is reversible.

**Archived.** The entity has completed its active lifecycle and its
primary content has been transferred to an archive named graph. Its IRI
remains resolvable and points to the archive record. The entity no longer
participates in scene graph operations.

**Tombstoned.** The entity's IRI has been permanently retired. A tombstone
triple in the provenance graph records the entity's full lifecycle history,
the reason for retirement, and the timestamp. The IRI is never reassigned.

The principle that **IRIs are never reassigned** is foundational. It
ensures that any historical reference to an entity's IRI — in event
records, provenance chains, external documents — continues to resolve
to something meaningful (the entity's tombstone record) rather than to
a new, unrelated entity.

### 11.2 Schema Evolution and Versioning {#schema-evolution}

The S-Box shapes graph for a holon will evolve over time: new constraints
will be added, existing constraints will be tightened or relaxed, state
enumerations will be extended. Schema evolution is the process of managing
this evolution without breaking the historical event record or requiring
mass data migration.

The architecture's approach to schema evolution exploits the event
sourcing commitment: because the interior graph is always derivable from
the event history by applying the current shapes, changing the shapes is
equivalent to changing the derivation rule. The event history itself does
not change; only the rules applied to it do.

In practice, schema evolution proceeds as follows:

1. A new version of the shapes graph is minted with a new version IRI.
2. The new version is associated with the holon's controller DID.
3. A migration event is added to the event history recording the schema
   transition.
4. New events are processed against the new shapes version.
5. For entities that become non-conformant under the new shapes, a
   remediation pass replays their event history against the new shapes
   to produce conformant current state.

Backward compatibility is declared using `owl:deprecated` on retired
terms and `skos:exactMatch` alignments to their replacements. Consumers
that still use retired terms should follow the `skos:exactMatch` link
to find the current term.

### 11.3 Vocabulary Governance {#vocabulary-governance}

The X-Box concept schemes are shared resources across the holarchy. Changes
to them affect every holon that references the changed concepts. Vocabulary
governance is therefore a community process, not a unilateral decision
by any individual holon controller.

The governance model the CGA recommends is:

- **Addition** of new concepts is low-risk and may be done by any authorised
  contributor following a defined review process.
- **Refinement** of existing concepts (making the definition more precise
  without changing its meaning) requires review by affected holon controllers.
- **Deprecation** of existing concepts requires a defined migration period,
  a replacement concept with `skos:exactMatch` alignment, and notification
  to all registered consumers.
- **Removal** of deprecated concepts may only occur after the migration
  period has ended and all known consumers have migrated.

The specific governance procedures are delegated to the normative
CGA-Vocab specification.

### 11.4 Conflict Resolution {#conflict-resolution}

The CGA commits to **optimistic concurrency with version annotations**
as the primary conflict resolution mechanism. This choice is described
in Appendix A (Design Decision Log), where the alternatives — last-write-wins
and conflict-free replicated data types — are discussed and rejected.

The optimistic concurrency model requires:

1. Every state triple carries a version number in its reifier annotation.
2. Every SHACL rule that updates a state triple includes a precondition
   check that the triple's current version matches the version the rule
   is operating against.
3. When a version mismatch is detected (indicating a concurrent update),
   the rule does not silently overwrite the concurrent update. Instead,
   it generates a `cga:ConflictEvent` in the event history and halts the
   current transition sequence.
4. The conflict event is visible to monitoring systems and to the agents
   whose actions triggered it. Resolution may be automatic (retry with
   the current version) or manual (escalate for human decision).

The maximum retry count and escalation path for unresolved conflicts are
configuration parameters of the deployment, not specified in this document.

### 11.5 Graph Compaction and Archival {#compaction-archival}

The append-only event history grows without bound in a live system. For
high-frequency IoT deployments, the event history may accumulate millions
of records per day. The architecture must provide a mechanism for
compacting the event history without losing the audit trail.

The compaction strategy the architecture endorses is **snapshot
compaction**: at defined intervals, the current interior graph state is
captured as a verified snapshot, signed by the holon's controller, and
added to the event history as a `cga:SnapshotEvent`. Events prior to
the snapshot may then be archived — moved to an archive store with slower
access but indefinite retention — because the snapshot provides a
sufficient ground state for replaying subsequent events.

The snapshot does not replace the archived events — they remain available
for deep historical query. It simply provides a checkpoint from which
forward replay is computationally tractable without requiring the full
history from the holon's inception.

---

## 12. Relationship to Adjacent Standards {#adjacent-standards}

The CGA is designed to compose with existing W3C specifications and
related standards. This section characterises the relationship with
each major adjacent standard: what the CGA adopts without modification,
what it extends, and what it does not use.

**RDF 1.2.** Adopted in full. The CGA uses RDF 1.2 named graphs as the
primary structural unit, and the `{| |}` reification notation as the
primary mechanism for qualifying assertions with metadata. The CGA does
not use the older, verbose reification vocabulary (`rdf:Statement`,
`rdf:subject`, `rdf:predicate`, `rdf:object`).

**SHACL 1.2.** Adopted in full, with specific reliance on features
introduced or clarified in SHACL 1.2: `sh:SPARQLRule` for state
transitions, `sh:reifierShape` for validating `{| |}` annotations,
`sh:if`/`sh:then`/`sh:else` for conditional constraint logic, and
`sh:rootClass` for interior population constraints. The CGA does not
use the SHACL JavaScript Extension profile; all behaviour is expressible
in SPARQL.

**SPARQL 1.2.** Adopted in full. SPARQL is the query and manipulation
language for all CGA operations, underlying both SHACL rules and direct
graph queries.

**OWL 2.** Used at the T-Box layer for domain modelling, class
definitions, and property declarations. The CGA does not rely on OWL
reasoning (entailment regimes) for operational behaviour; rule-based
inference in SHACL is preferred for operational use cases.

**SKOS.** Adopted in full and extended with CGA-specific concept schemes.
The CGA uses SKOS `skos:broader`/`skos:narrower` property paths
extensively in SHACL rules and SPARQL queries to enable hierarchical
classification queries.

**W3C DID Core 1.0.** Adopted in full for holon and agent identity.
The CGA does not specify a preferred DID method; `did:web` is recommended
for holons in well-governed network environments, but any conformant
DID method is acceptable.

**W3C Verifiable Credentials Data Model 2.0.** Adopted in full.
The CGA specifically uses the Data Integrity Proof mechanism with
the RDFC-1.0 canonicalisation pathway, which enables VC content to be
represented natively as RDF triples after verification. The VC-JOSE-COSE
pathway is supported for interoperability but requires an additional
deserialization step at the trust boundary.

**GeoSPARQL 1.1.** Adopted for continuous spatial representation.
`geo:wktLiteral` is the primary encoding for continuous geometry.
The CGA extends GeoSPARQL with indexed spatial classification using
SKOS concept schemes (H3, S2, postal codes) as a complementary
discrete spatial representation.

**OWL Time.** Adopted for temporal representation. `time:Instant` and
`time:Interval` are the primary temporal constructs. The CGA uses both
`xsd:dateTime` for precise timestamps and OWL Time intervals for
durations and temporal extents.

**PROV-O.** The W3C Provenance Ontology is an informative reference
for the CGA's event history and provenance chain model. The CGA does
not directly extend PROV-O but its event history model is compatible
with PROV-O's `prov:Activity`, `prov:Entity`, and `prov:Agent` concepts.

**RDF Patch.** The W3C community draft format for expressing incremental
changes to named graphs is adopted for the delta subscription model.
CGA implementations are expected to support RDF Patch as the format
for projection graph delta streams.

**Solid Project.** The Solid Project's data pod model — where each user
controls a personal data store structured as a collection of linked data
resources — is informationally related to the CGA's agent context graph
model. A Solid pod may serve as the backing store for an agent's context
graph. The CGA does not depend on Solid and is compatible with it.

**W3C Web of Things (WoT).** The WoT Thing Description model provides
a complementary description of IoT device capabilities. A WoT Thing
Description may serve as the T-Box definition for a CGA digital twin
entity, with the CGA providing the state management, event history,
and trust model on top of the WoT capability description.

---

## 13. The CGA Specification Family {#specification-family}

This foundational document underlies a family of normative specifications,
each addressing a distinct aspect of the Context Graph Architecture. The
normative specifications are Community Group Reports in progress and are
referenced here to give readers a roadmap of the full architecture.

**CGA-Holon** defines the normative requirements for holon representation:
the schema of the self-referential signature triple, the required and
optional properties of a conformant holon interior, the holarchy
relationship model, and the lifecycle state machine. Presupposes §§3,
4, 5, and 11 of this document.

**CGA-Portal** defines the normative requirements for portal representation
and traversal: the structure of the portal triple and its reifier
annotation, the state machine for portal lifecycle (Locked, Unlocking,
Open, Traversing, Closing), the credential validation pipeline, and the
SHACL rule sequence for traversal. Presupposes §§5, 7, and 8.

**CGA-Trust** defines the normative requirements for the trust model:
the device registry schema, the signal validation pipeline, the VC
verification and injection protocol, the trust level vocabulary, and
the trust boundary implementation requirements. Presupposes §7.

**CGA-Event** defines the normative requirements for event sourcing:
the ContextEvent schema, the causal ordering model, the hold-and-release
mechanism for out-of-order signals, the snapshot compaction protocol,
and the bi-temporal query model. Presupposes §8.

**CGA-Presentation** defines the normative requirements for the projection
and appearance model: the projection graph derivation algorithm, the
appearance graph cascade model, the delta subscription protocol using
RDF Patch, and the multi-agent consistency requirements. Presupposes §10.

**CGA-Twin** defines the normative requirements for digital twin
integration: the twin sync state model, the inference engine interface,
the actuation command and acknowledgement protocol, and the divergence
detection and resolution mechanisms. Presupposes §§3.4, 7.6, and 9.

**CGA-Vocab** defines the normative CGA core vocabulary: the full set
of classes, properties, and SKOS concept schemes that constitute the
`cga:` namespace, with normative definitions, usage notes, and
governance procedures for vocabulary evolution.

---

## Appendix A: Design Decision Log {#design-decisions}

This appendix records the significant design decisions made during the
development of this specification, the alternatives considered, and
the rationale for the choices made. It is intended to help future
contributors understand why the architecture has the shape it does and
to prevent relitigating settled decisions without awareness of the
prior deliberation.

---

**Decision A.1: Event Sourcing as the State Management Model**

*Alternatives considered:* Direct state mutation (treating the interior
graph as a mutable current-state store, without a separate event history).
Change data capture (maintaining a current-state store with a separate
log of changes, but treating the current store as primary).

*Decision:* Event sourcing — event history is primary, interior graph
is derived.

*Rationale:* Direct state mutation does not support replay, makes schema
migration destructive, and loses the audit trail. Change data capture
maintains auditability but treats the current-state store as primary,
which means schema migration requires data transformation rather than
replay. Event sourcing is the only model that provides all four properties
(auditability, replay, recovery, temporal query) without compromise. The
cost — maintaining the interior graph as a derived structure — is
acceptable given that SHACL rule machinery provides the synchronisation
mechanism.

---

**Decision A.2: Optimistic Concurrency Over Last-Write-Wins and CRDTs**

*Alternatives considered:* Last-write-wins (most recent write determines
current state). Conflict-free replicated data types (CRDT semantics for
concurrent writes, eliminating conflicts by construction).

*Decision:* Optimistic concurrency with version annotations.

*Rationale:* Last-write-wins produces silent, counterintuitive results
in multi-agent scenarios where the "last" write may not represent the
correct semantic resolution (both Alice and Bob trying to acquire the
spectrometer — whoever's event arrived last "wins" with no record of
the contention). CRDTs are mathematically elegant but require committing
to specific CRDT types (G-Set, LWW-Register, OR-Set) per property,
which constrains the data model and has no mature RDF implementation.
Optimistic concurrency produces explicit conflict events that are
auditable and resolvable through defined processes, at the cost of
requiring version checks in rule preconditions. This is an acceptable
cost for a standards-grade architecture where correctness is more
important than throughput.

---

**Decision A.3: CWA/OWA Stratification By Layer**

*Alternatives considered:* Adopt OWA throughout (pure semantic web
approach). Adopt CWA throughout (pure database approach). Hybrid
with ad hoc rules about when each applies.

*Decision:* Stratification by layer — OWA for T-Box and X-Box domain
modelling; CWA for A-Box operational state and S-Box constraint evaluation.

*Rationale:* Pure OWA is unsuitable for operational systems that must
make definitive decisions. Pure CWA is unsuitable for domain modelling
in open, federated knowledge environments. The stratification reflects
the actual epistemic situation: the domain genuinely extends beyond
any current dataset (OWA is correct for modelling it), but operational
rules must fire against the graph as presented (CWA is correct for
constraint evaluation). The belief model at the A-Box/S-Box interface
acknowledges the residual uncertainty without undermining operational
determinism.

---

**Decision A.4: SHACL 1.2 as the Primary Rule and Constraint Layer**

*Alternatives considered:* Datalog rules. OWL RL entailment. SPARQL
UPDATE as the direct rule mechanism. A custom rule language.

*Decision:* SHACL 1.2, specifically `sh:SPARQLRule`.

*Rationale:* Datalog is well-studied but not a W3C standard and has
limited tooling in the RDF ecosystem. OWL RL entailment operates under
the OWA and produces materialised triples without the provenance
tracking the architecture requires. SPARQL UPDATE is powerful but
operates outside any shape-governed framework, making it difficult to
govern which rules may fire and under what conditions. SHACL 1.2 provides
a governed, shape-targeted rule mechanism with built-in conditional logic
(`sh:if`/`sh:then`/`sh:else`), reifier shape validation
(`sh:reifierShape`), and ordering control (`sh:order`). Its constraint
model is directly aligned with the S-Box layer's role as generative model.

---

**Decision A.5: DID as the Identity Mechanism for Holons and Agents**

*Alternatives considered:* HTTP IRIs with no cryptographic backing.
PKI certificates with a centralised CA. OAuth 2.0 / OIDC tokens.

*Decision:* W3C Decentralised Identifiers (DID) Core 1.0.

*Rationale:* HTTP IRIs provide no cryptographic verifiability and require
a functioning HTTP server for resolution — unsuitable for long-lived,
authority-independent holon identity. PKI certificates require a
centralised certificate authority and do not natively support the key
rotation and controller transfer properties needed for holon governance.
OAuth 2.0 tokens are session-scoped and not suitable as persistent entity
identifiers. DIDs provide globally unique, cryptographically verifiable,
controller-independent, persistently resolvable identifiers with native
support for key rotation and authority delegation — exactly the properties
the CGA requires for holon and agent identity.

---

**Decision A.6: The Belief Model Over Binary Truth**

*Alternatives considered:* Treat all interior graph triples as
definitively true (binary truth). Use a separate, non-integrated
uncertainty management system.

*Decision:* Explicit belief model using `{| |}` reifier annotations
for confidence, precision, and Bayesian weight metadata.

*Rationale:* Binary truth is incorrect for any system receiving signals
from imprecise physical sensors. A system that treats a degraded sensor's
reading as definitively true will propagate errors into downstream rules
and decisions. A separate uncertainty management system creates an
architectural seam that is difficult to maintain and makes uncertainty
invisible to the SHACL rule machinery. The `{| |}` reifier annotation
integrates confidence metadata directly into the graph, making it
accessible to SPARQL queries and SHACL preconditions without requiring
separate system calls.

---

## Appendix B: Comparison With Related Architectures {#comparisons}

### B.1 Web Architecture (HTTP/HTML/CSS)

The web architecture provides the closest familiar analogue to the CGA
at the interaction model level. A web page is a bounded context (the
HTML document) rendered within an agent's viewport (the browser). The
URL identifies the resource; HTTP delivers a representation; CSS governs
presentation; JavaScript governs dynamic behaviour.

The CGA extends this analogy in several important directions. A holon
interior is more than a document: it is a persistent, inhabited environment
in which the agent's presence is a first-class fact. A web page does not
know you are reading it; a holon interior contains a triple asserting the
agent's current presence. A web page has no coordinate system for the
agent's position within it; a holon interior has a formal spatial model
with multiple coordinate representations. A web page's state does not
change because you are looking at it; a holon's interior state changes
in response to agent interactions.

The web architecture is the right conceptual starting point for explaining
the CGA to a broad audience. The departures from the web model — persistent
presence, coordinate grounding, event-driven state change, multi-agent
consistency — are precisely the features that make the CGA architecturally
richer than the web.

### B.2 Property Graph Databases (Neo4j, AWS Neptune)

Property graph databases share the CGA's commitment to graph structure
for data representation and to rich edge properties (analogous to the
CGA's reifier annotations). They are operationally mature, well-tooled,
and widely deployed.

The CGA differs from property graph databases in three significant ways.
First, the CGA uses RDF's globally shared identifier space (IRIs), enabling
interoperability across deployments without schema coordination. Property
graphs use local node and edge identifiers that are not globally meaningful.
Second, the CGA has a formal semantics layer (OWL, SHACL) that property
graph databases lack, enabling constraint validation, rule-based inference,
and formal vocabulary alignment. Third, the CGA's named graph model provides
first-class multi-graph architecture; property graph databases typically
use a single graph with label-based logical partitioning.

### B.3 Digital Twin Standards (ISO 23247, DTDL, NGSI-LD)

Several digital twin standards address the relationship between graph
representations and physical systems. ISO 23247 (Digital Twin for
Manufacturing) defines the architecture for manufacturing digital twins.
Microsoft's Digital Twin Definition Language (DTDL) provides a JSON-based
schema for digital twin properties and relationships. NGSI-LD (ETSI)
provides an RDF-based context information management standard widely used
in smart city deployments.

The CGA is complementary to all of these. The twin sync state model,
the IoT trust chain, and the actuation command protocol address
requirements that are common to all digital twin architectures.
The CGA's distinctive contributions are the event sourcing model for
state history, the holarchic containment model for spatial and organisational
structure, and the belief model for uncertainty quantification — none of
which are addressed in depth by existing digital twin standards.

NGSI-LD is the most architecturally proximate existing standard: it uses
RDF and JSON-LD, supports context resolution, and has a mature IoT
deployment history. CGA-Twin is expected to define alignment with NGSI-LD
to enable migration and interoperability for existing NGSI-LD deployments.

### B.4 Solid Project

The Solid Project's data pod architecture shares the CGA's commitment to
decentralised, IRI-identified, access-controlled personal data stores.
A Solid pod is, in some respects, a simple holon: it has an interior
(the pod's data), an access control model (Web Access Control or ACP),
and an identity (the WebID or DID of the pod owner).

The CGA extends the Solid model in the direction of operational,
multi-entity, multi-agent environments. Solid pods are designed for
personal data storage and sharing; they do not have state machines,
scene graphs, event histories, or multi-agent interaction models. A Solid
pod could serve as the backing store for a CGA agent's context graph, and
the CGA's trust model is compatible with Solid's access control model.

---

## Appendix C: Worked Example {#worked-example}

This appendix provides an illustrative example that demonstrates the
foundational concepts of the CGA in a concrete scenario. The scenario
is simplified for clarity; a production deployment would include additional
shapes, rules, and event history detail.

**Scenario.** A research building has a laboratory (Lab Holon) within it
(Building Holon). The Lab contains a spectrometer (a digital twin entity)
and is occupied by two researchers, Alice and Bob. Alice requests an assay
using the spectrometer.

**Step 1: Holon Structure.**

```turtle
# Building Holon — outer context
GRAPH cga:BuildingHolon {
    cga:BuildingHolon a cga:Holon {|
        cga:parentHolon  owl:Nothing ;
        cga:controller   <did:web:building.example.org>
    |} ;
        rdfs:label "Research Building"@en .

    # Portal to the Lab (open — no credential required)
    cga:Corridor cga:leadsTo cga:LabHolon {|
        cga:transitState  cga:Open ;
        cga:portalType    cga:SpatialPortal
    |} .
}

# Lab Holon — inner context
GRAPH cga:LabHolon {
    cga:LabHolon a cga:Holon {|
        cga:parentHolon  cga:BuildingHolon ;
        cga:controller   <did:web:lab.example.org> ;
        cga:validatedBy  cga:LabHolon_shapes
    |} ;
        rdfs:label "Research Laboratory"@en .

    # Spectrometer entity — digital twin
    cga:Spectrometer a cga:SceneEntity , cga:DigitalTwin {|
        cga:physicalId    "SN-SPEC-0042"^^xsd:string ;
        cga:twinSyncState cga:Synchronised
    |} ;
        cga:status    cga:Idle {|
            cga:version        "1"^^xsd:integer ;
            cga:beliefConfidence "1.0"^^xsd:decimal
        |} ;
        cga:operatedBy owl:Nothing .
}
```

**Step 2: Alice Enters the Lab.**

Alice's DID is `did:web:alice.researcher.org`. She traverses the corridor
portal. The traversal rule fires, writing her presence into the Lab's
interior and her trajectory into her context graph.

```turtle
GRAPH cga:AgentContext_Alice {
    <did:web:alice.researcher.org>
        cga:currentLocation cga:LabHolon ;
        cga:currentPosition "POINT(5 5)"^^geo:wktLiteral ;
        cga:arrivedAt       "2026-03-15T09:58:00Z"^^xsd:dateTime .
}
```

**Step 3: Alice Requests an Assay.**

Alice asserts an interaction event into the Lab's event graph:

```turtle
GRAPH cga:LabHolon_events {
    cga:Event_007 a cga:InteractionEvent {|
        cga:eventType     cga:RequestAssay ;
        cga:initiatedBy   <did:web:alice.researcher.org> ;
        cga:targetEntity  cga:Spectrometer ;
        cga:causalOrder   7 ;
        cga:timestamp     "2026-03-15T10:10:00Z"^^xsd:dateTime
    |} .
}
```

**Step 4: SHACL Rules Fire.**

The Lab's S-Box shapes include a rule targeting `cga:Spectrometer` that
responds to `cga:RequestAssay` events. The rule validates that the
spectrometer is idle and Alice has the required credentials, then
transitions the state:

```turtle
# After rule firing — updated interior graph
GRAPH cga:LabHolon {
    cga:Spectrometer cga:status cga:Running {|
        cga:version        "2"^^xsd:integer ;
        cga:priorState     cga:Idle ;
        cga:authorisedBy   <did:web:alice.researcher.org> ;
        cga:derivedFrom    cga:Event_007 ;
        cga:beliefConfidence "1.0"^^xsd:decimal
    |} ;
        cga:operatedBy <did:web:alice.researcher.org> .
}
```

**Step 5: Projection Graphs Update.**

The projection graph for both Alice and Bob is updated to reflect the
spectrometer's new state. Alice's projection includes her full view;
Bob's projection includes the spectrometer's state (he can see it is now
occupied) but not the detail of Alice's assay parameters (which are in
Alice's session context, not the public interior graph).

This example demonstrates the key foundational concepts operating
together: holon containment, digital twin state, event sourcing,
SHACL rule-driven state transitions, belief annotation, and
agent-specific projection.

---

## Appendix D: Acknowledgements {#acknowledgements}

The Context Graph Community Group acknowledges the foundational
contributions of the W3C RDF Working Group, the W3C SHACL Community
Group and Working Group, the W3C Verifiable Credentials Working Group,
and the W3C Decentralised Identifier Working Group, whose specifications
this architecture builds upon.

The concept of the holon is due to Arthur Koestler, whose *The Ghost
in the Machine* (1967) and *Janus: A Summing Up* (1978) provided the
systems-theoretic foundation for the holarchic model.

The informative correspondence with the Free Energy Principle and Active
Inference is drawn from the work of Karl Friston and colleagues, whose
theoretical framework provided a principled basis for the architecture's
approach to belief, uncertainty, and prediction-error-driven state
transitions.

---

## Appendix E: References {#references}

### E.1 Normative References {#normative-references}

These specifications are directly referenced in this document and in
the normative CGA specifications that build upon this foundation.

**[RDF12-CONCEPTS]** Cyganiak, R., Wood, D., Lanthaler, M. (Eds.).
*RDF 1.2 Concepts and Abstract Syntax.* W3C Recommendation.
https://www.w3.org/TR/rdf12-concepts/

**[RDF12-TURTLE]** Prud'hommeaux, E., Carothers, G., et al. (Eds.).
*RDF 1.2 Turtle.* W3C Recommendation.
https://www.w3.org/TR/rdf12-turtle/

**[SHACL]** Knublauch, H., Kontokostas, D. (Eds.).
*Shapes Constraint Language (SHACL).* W3C Recommendation.
https://www.w3.org/TR/shacl/

**[SHACL-AF]** Knublauch, H. (Ed.).
*SHACL Advanced Features.* W3C Working Group Note.
https://www.w3.org/TR/shacl-af/

**[SPARQL11-QUERY]** Harris, S., Seaborne, A. (Eds.).
*SPARQL 1.1 Query Language.* W3C Recommendation.
https://www.w3.org/TR/sparql11-query/

**[OWL2-OVERVIEW]** Hitzler, P., et al. (Eds.).
*OWL 2 Web Ontology Language: Document Overview.* W3C Recommendation.
https://www.w3.org/TR/owl2-overview/

**[SKOS-REFERENCE]** Miles, A., Bechhofer, S. (Eds.).
*SKOS Simple Knowledge Organization System Reference.* W3C Recommendation.
https://www.w3.org/TR/skos-reference/

**[DID-CORE]** Sporny, M., et al. (Eds.).
*Decentralized Identifiers (DIDs) v1.0.* W3C Recommendation.
https://www.w3.org/TR/did-core/

**[VC-DATA-MODEL]** Sporny, M., et al. (Eds.).
*Verifiable Credentials Data Model v2.0.* W3C Recommendation.
https://www.w3.org/TR/vc-data-model-2.0/

**[VC-DATA-INTEGRITY]** Sporny, M., et al. (Eds.).
*Verifiable Credential Data Integrity 1.0.* W3C Recommendation.
https://www.w3.org/TR/vc-data-integrity/

**[GEOSPARQL]** Car, N., et al. (Eds.).
*OGC GeoSPARQL — A Geographic Query Language for RDF Data, v1.1.*
OGC Standard. https://www.ogc.org/standard/geosparql/

**[OWL-TIME]** Cox, S., Little, C. (Eds.).
*Time Ontology in OWL.* W3C Recommendation.
https://www.w3.org/TR/owl-time/

### E.2 Informative References {#informative-references}

**[KOESTLER-1967]** Koestler, A.
*The Ghost in the Machine.* Hutchinson, 1967.

**[KOESTLER-1978]** Koestler, A.
*Janus: A Summing Up.* Hutchinson, 1978.

**[FRISTON-2010]** Friston, K.
"The free-energy principle: a unified brain theory?"
*Nature Reviews Neuroscience,* 11(2), 127–138, 2010.

**[FRISTON-2017]** Friston, K., et al.
"Active inference: a process theory."
*Neural Computation,* 29(1), 1–49, 2017.

**[PROV-O]** Lebo, T., et al. (Eds.).
*PROV-O: The PROV Ontology.* W3C Recommendation.
https://www.w3.org/TR/prov-o/

**[SOLID-PROTOCOL]** Sarven, C., et al.
*The Solid Protocol.* W3C Solid Community Group Report.
https://solidproject.org/TR/protocol

**[WOT-THING-DESCRIPTION]** Kaebisch, S., et al. (Eds.).
*Web of Things (WoT) Thing Description 1.1.* W3C Recommendation.
https://www.w3.org/TR/wot-thing-description11/

**[NGSI-LD]** ETSI GS CIM 009 V1.6.1.
*Context Information Management (CIM); NGSI-LD API.*
ETSI, 2023.

**[RDF-PATCH]** Seaborne, A.
*RDF Patch — Describing Changes to an RDF Dataset.*
W3C Community Draft.
https://afs.github.io/rdf-patch/

**[LAMPORT-1978]** Lamport, L.
"Time, clocks, and the ordering of events in a distributed system."
*Communications of the ACM,* 21(7), 558–565, 1978.

---

*End of Document*

---

> **Copyright Notice**
>
> Copyright © 2026 the Contributors to the Context Graph Architecture:
> Foundational Specification, published by the
> [Context Graph Community Group](https://www.w3.org/community/context-graph/)
> under the [W3C Community Contributor License Agreement (CLA)](https://www.w3.org/community/about/agreements/cla/).
> A human-readable summary is available.
