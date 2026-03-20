# Context Graph Architecture: Holonic Context Graph Specification

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
> This document is **explanatory** rather than normative: it establishes the
> conceptual architecture of holonic context graphs, illustrates that architecture
> with worked examples, and situates it within the broader CGA specification family.
> Conformance requirements, where needed, are delegated to the normative
> specifications referenced in [§12](#spec-family).
>
> Feedback is invited via the group's
> [GitHub repository](https://github.com/W3C-Context-Graph-Community-Group/Semantic-Alignment)
> issue tracker and mailing list.

---

| Field | Value |
|---|---|
| **Title** | Context Graph Architecture: Holonic Context Graph Specification |
| **Shortname** | `cga-holon` |
| **Version** | 0.1 (Draft) |
| **Date** | 2026-03-20 |
| **Status** | Draft Community Group Report |
| **This version** | https://w3c-context-graph-community-group.github.io/Semantic-Alignment/cga-holon/ |
| **Latest version** | https://github.com/W3C-Context-Graph-Community-Group/Semantic-Alignment/blob/main/cga-holon.md |
| **Repository** | https://github.com/W3C-Context-Graph-Community-Group/Semantic-Alignment |
| **Editors** | Context Graph CG Chairs |
| **License** | [W3C Software and Document License](https://www.w3.org/Consortium/Legal/2015/copyright-software-and-document) |

---

## Abstract {#abstract}

This document specifies the **Holonic Context Graph** model — the
architectural framework within which Context Graph instances are organised
into nested, self-describing, boundary-governed units called *holons*.
It defines the four-layer graph structure of each holon (interior,
boundary membrane, projection, and context layers), the role of *portals*
as first-class boundary objects governing traversal between holons, the
integration of SHACL 1.2 as the formal specification of boundary membranes,
the use of PROV-O for provenance and process modelling, and the use of SKOS
concept schemes as the controlled vocabulary layer within and across holons.

The document draws upon and reconciles two prior CGA working documents:
the [Glossary](Glossary.md) and the
[CGA Foundational Concepts](CGA%20Foundational%20Concepts.md).
Where those documents are in tension, this specification identifies the
discrepancy explicitly and proposes a resolution.

The intended audience is practitioners with working knowledge of the W3C RDF
stack — Turtle, SPARQL, and SHACL — who require a conceptual and structural
framework for representing bounded, navigable, multi-resolution knowledge
domains as holarchies of context graphs.

---

## Table of Contents {#toc}

- [1. Introduction](#introduction)
  - [1.1 Motivation](#motivation)
  - [1.2 Scope](#scope)
  - [1.3 Relationship to Prior CGA Documents](#prior-docs)
  - [1.4 Terminology Note: Reconciling Discrepancies](#discrepancies)
  - [1.5 Document Conventions](#conventions)
- [2. Foundational Concepts](#foundations)
  - [2.1 The Holon](#holon-concept)
  - [2.2 The Holarchy](#holarchy-concept)
  - [2.3 The Four Graph Layers](#four-layers)
  - [2.4 The T/A/S/X-Box Layering Model](#box-model)
  - [2.5 The Epistemic Stance](#epistemic-stance)
- [3. Namespace and Prefix Declarations](#namespaces)
- [4. The Interior Graph](#interior-graph)
  - [4.1 Self-Referential Identity](#self-identity)
  - [4.2 Scene Graph Structure](#scene-graph)
  - [4.3 Coordinate Frames and Tensor Values](#coordinates)
  - [4.4 Belief Annotation](#belief)
- [5. The Boundary Membrane](#boundary)
  - [5.1 SHACL 1.2 as Formal Membrane](#shacl-membrane)
  - [5.2 Node Shapes and Closed-World Enforcement](#node-shapes)
  - [5.3 Reifier Shapes](#reifier-shapes)
  - [5.4 SHACL Rules as Membrane Logic](#shacl-rules)
  - [5.5 Membrane Health and Severity Mapping](#severity)
- [6. Portals](#portals)
  - [6.1 Portals as First-Class Boundary Objects](#portal-nature)
  - [6.2 Portal Types](#portal-types)
  - [6.3 Portal Activation and Traversal](#portal-traversal)
  - [6.4 Frame Transformation at Portal Boundaries](#portal-transform)
  - [6.5 Shared Portals: Routes and Networks](#shared-portals)
  - [6.6 Reification of Portal Bindings](#portal-reification)
- [7. The Projection Graph](#projection)
  - [7.1 Projection as Curated Exterior](#projection-exterior)
  - [7.2 External Bindings](#external-bindings)
  - [7.3 Projection Shapes](#projection-shapes)
- [8. The Context Graph Layer](#context-layer)
  - [8.1 Two Strata: Structural and Temporal](#strata)
  - [8.2 The Structural Stratum as Scene Graph](#structural-stratum)
  - [8.3 The Temporal Stratum as Event-Driven Datalog](#temporal-stratum)
  - [8.4 Signals, Rules, and Annotation Deposition](#signals-rules)
  - [8.5 PROV-O Integration](#provo)
- [9. SKOS Vocabulary Layer](#skos)
  - [9.1 Concept Schemes in Holonic Graphs](#skos-schemes)
  - [9.2 inScheme over Broader: Rationale and Pattern](#inscheme)
  - [9.3 Cross-Holon Vocabulary Alignment](#skos-alignment)
  - [9.4 SKOS and the X-Box](#skos-xbox)
- [10. Worked Examples](#examples)
  - [10.1 Geographic Holarchy: Continent to City](#geo-example)
  - [10.2 Dungeon Holarchy: Chambers and Portals](#dungeon-example)
  - [10.3 Musical Composition Holarchy](#music-example)
  - [10.4 Transport Route as Independent Holon](#route-example)
- [11. Discrepancy Log: Glossary vs. Foundational Concepts](#discrepancy-log)
- [12. The CGA Specification Family](#spec-family)
- [Appendix A: cga: Vocabulary Summary](#vocab-summary)
- [Appendix B: References](#references)

---

## 1. Introduction {#introduction}

### 1.1 Motivation {#motivation}

The W3C RDF stack — triples, named graphs, SPARQL, SHACL, OWL — provides
powerful machinery for representing knowledge. What it does not provide is a
principled framework for representing knowledge that has *structure*: things
that exist at multiple scales simultaneously, that have inside/outside
distinctions, that are governed by different authorities at different levels,
and that are navigable from any entry point.

A geographic hierarchy (continent, country, province, city), an organisational
chart (corporation, division, department, team), a musical composition
(work, movement, section, motif), a software system (platform, service,
module, function) — all share a common structural pattern: each level is
simultaneously a whole in itself and a part of something larger. The city
is complete as a concept; it is also a part of a province. The sonata
movement is complete as a musical statement; it is also a part of the work.

Arthur Koestler named this pattern the **holon** (*The Ghost in the Machine*,
1967): an entity with an inward face (its interior, self-governing, coherent)
and an outward face (its membership in a larger whole). A system of nested
holons is a **holarchy**.

This specification defines how holons are represented in RDF 1.2, how their
boundaries are governed by SHACL 1.2, how context graphs serve both as the
structural context layer of the holarchy and as the event-driven temporal
record of each holon's history, and how SKOS concept schemes provide the
controlled vocabulary that connects holons within and across holarchies.

### 1.2 Scope {#scope}

This document covers:

- The four-layer graph structure of a holon (interior, boundary, projection,
  context)
- Portals as first-class boundary objects governing inter-holon traversal
- SHACL 1.2 as the formal specification of boundary membranes
- PROV-O as the process and provenance vocabulary for temporal annotation
- SKOS concept schemes as the X-Box vocabulary layer, with emphasis on
  `skos:inScheme` over `skos:broader` for concept enumeration
- Coordinate systems as abstract reference frames expressible as holons
- Worked examples across geographic, narrative, musical, and transport domains

This document does not cover:

- Trust model and verifiable credentials (CGA-Trust)
- Digital twin synchronisation (CGA-Twin)
- Multi-agent session management (CGA-Agent)
- The Coherence Protocol (CGA-Coherence)

### 1.3 Relationship to Prior CGA Documents {#prior-docs}

This document builds upon and reconciles two prior working documents in the
Semantic-Alignment repository:

- **[Glossary](Glossary.md)** — Working Draft v0.2, defining foundational,
  structural, and operational concepts
- **[CGA Foundational Concepts](CGA%20Foundational%20Concepts.md)** — Draft
  Community Group Report v0.1, providing the architectural layering model,
  holon model, and semantic commitments

Where these documents are in agreement, this specification adopts their
definitions without modification. Where they are in tension, [§11](#discrepancy-log)
records the discrepancy, proposes a resolution, and states which interpretation
this document follows. Neither source document is normative; both inform this draft.

### 1.4 Terminology Note: Reconciling Discrepancies {#discrepancies}

The most consequential discrepancy between the two prior documents is the
definition of *Context Graph*:

The Glossary defines a Context Graph as "an auditable, append-only event log
representing the state of coherence at one or more system boundaries." This
identifies the Context Graph with the event log.

The Foundational Concepts document defines it as "a named graph that carries
interpretation context — the framing information needed to resolve the meaning
of terms, policies, and state values at a specific decision or interaction
point." This identifies the Context Graph with epistemic framing, distinct
from the event log.

This specification adopts the Foundational Concepts interpretation, for reasons
given in [§11](#discrepancy-log), and treats the event log as the *temporal stratum*
of the context layer — a component of the context graph in the holonic sense,
not the whole of it.

### 1.5 Document Conventions {#conventions}

**Informative vs. explanatory.** This document is entirely explanatory.
It establishes architectural patterns and illustrates them with examples.
No statement in this document establishes a conformance requirement.

**RDF 1.2 notation.** All Turtle examples use RDF 1.2 syntax, including
the `{| |}` annotation syntax and the optional `~ <name>` reifier naming
syntax. A reified triple with an anonymous reifier:

```turtle
<subject> <predicate> <object> {|
    <annotation-property> <annotation-value>
|} .
```

A reified triple with a named reifier:

```turtle
<subject> <predicate> <object> ~ <reifier-iri> {|
    <annotation-property> <annotation-value>
|} .
```

The base triple is always asserted independently of its reifier annotations.
The reifier annotates the triple as a whole without changing its assertional
status.

**Named graph blocks** use the SPARQL/Turtle `GRAPH` keyword:

```turtle
GRAPH <graph-iri> {
    # triples within this named graph
}
```

**SHACL 1.2.** SHACL examples reference the Working Draft dated 2026-03-19:
https://www.w3.org/TR/2026/WD-shacl12-core-20260319/
SHACL Rules examples reference the Advanced Features sub-specification
linked from that document.

**Defined terms** appear in **bold** at their point of definition.
Cross-references to defined terms are presented as hyperlinks within
the document.

---

## 2. Foundational Concepts {#foundations}

### 2.1 The Holon {#holon-concept}

> *Definition adopted from CGA Foundational Concepts §5.1, extended for
> this specification.*

A **holon** is an entity that is simultaneously a self-contained whole and a
component of a larger structure. In the CGA, a holon is represented as a
*cluster of named graphs* sharing a common IRI. That IRI serves three roles:

1. It is the persistent, globally unique identity of the entity.
2. It is the graph name of the holon's **interior graph** — the named graph
   containing the holon's own assertions about itself.
3. It is the subject of triples in other holon layers that describe the
   holon's membership, boundary, and external presentation.

The dual inward/outward nature of the holon — Koestler's *Janus property* —
is architecturally realised by the four-layer graph structure: an interior
that is self-governing, a boundary that is actively selective, a projection
that is curated for external consumption, and a context that records where
the holon belongs.

A conformant holon in this specification has:

- A persistent **IRI** serving as identity and interior graph name
- A **boundary membrane** — a SHACL 1.2 shapes graph — governing interior
  validity and portal access
- A **projection graph** — a named graph presenting the holon's exterior face
- A **context graph** — a named graph (or pair of named sub-graphs) recording
  structural membership and temporal event history
- A **self-referential identity triple** within the interior graph, annotated
  via `{| |}` with structural metadata

A holon's interior graph IRI and the four associated layer IRIs follow a
consistent naming pattern:

| Layer | IRI pattern |
|---|---|
| Interior | `<base-iri>` |
| Boundary membrane | `<base-iri>/boundary` |
| Projection | `<base-iri>/projection` |
| Context (structural) | `<base-iri>/context/structural` |
| Context (temporal) | `<base-iri>/context/temporal` |

### 2.2 The Holarchy {#holarchy-concept}

A **holarchy** is a hierarchically organised system of holons, where each
holon is both a whole relative to the holons it contains and a part relative
to the holon that contains it.

In RDF, a holarchy is represented through the `cga:partOf` and `cga:contains`
properties, asserted as inverses but both stated explicitly — the CGA does not
rely on OWL inference to derive one from the other in operational contexts:

```turtle
GRAPH <urn:holon:geo:canada> {
    <urn:holon:geo:canada>
        cga:partOf   <urn:holon:geo:north-america> ;
        cga:contains <urn:holon:geo:canada:british-columbia> ,
                     <urn:holon:geo:canada:ontario> ,
                     <urn:holon:geo:canada:quebec> .
}
```

The **holonic depth** counter (`cga:holonDepth`) records position in the
holarchy. Depth 0 is the root holon; each nesting level increments the counter.
Depth provides a first approximation of resolution: queries can filter by depth
to retrieve information at a specific scale without traversing the full hierarchy.

### 2.3 The Four Graph Layers {#four-layers}

Every holon is described by four distinct named graph layers, each answering
a different question:

| Layer | Question answered | Epistemic role |
|---|---|---|
| **Interior graph** | What is true *inside* this holon? | A-Box assertions, CWA |
| **Boundary membrane** | What is *valid* here, and how do you get in or out? | S-Box shapes, CWA |
| **Projection graph** | What does this holon *look like* to the outside? | Curated A-Box, external bindings |
| **Context graph** | What *larger whole* does this holon belong to? | Structural (stable) + Temporal (accumulating) |

The four layers share the holon's IRI as their identity anchor. A SPARQL
query using that IRI as subject can retrieve information from any or all
layers through their respective named graph addresses.

The **cell analogy** remains the most accessible mental model for the four
layers: the interior is the cell's contents; the boundary is the cell membrane;
the projection is the signals the cell broadcasts to neighbouring cells; the
context is the tissue, organ, and organism hierarchy locating the cell in
the body.

### 2.4 The T/A/S/X-Box Layering Model {#box-model}

> *Adopted from CGA Foundational Concepts §4.*

The holonic graph architecture stratifies knowledge into four interdependent
layers with distinct governing assumptions:

| Layer | Name | Content | Primary technology | Assumption |
|---|---|---|---|---|
| **T-Box** | Terminology | Class and property definitions | OWL 2, RDFS | Open World |
| **A-Box** | Assertions | Instance data, state, events | RDF 1.2 | Closed World (per graph) |
| **S-Box** | Shapes | Constraints and rules | SHACL 1.2 | Closed World |
| **X-Box** | Vocabulary | Concept schemes, taxonomies | SKOS | Open World |

A critical design preference in this specification: **SHACL 1.2 shapes are
preferred over OWL axioms** for expressing operational constraints. OWL
reasoning operates under the Open World Assumption and does not produce the
closed-world validation behaviour required for operational membrane enforcement.
SHACL shapes enforce the Closed World Assumption explicitly and are directly
executable by conformant validators. OWL axioms remain appropriate for
domain modelling in the T-Box; they are not appropriate as substitutes for
SHACL shapes in the S-Box.

This preference is particularly important for cardinality constraints. An OWL
`owl:maxCardinality 1` axiom says "at most one value is conceivable in the
open world"; a SHACL `sh:maxCount 1` constraint says "the graph currently
being validated must contain at most one value, and if it contains more, the
entity is invalid." These are different statements, and the SHACL statement
is the one the boundary membrane requires.

### 2.5 The Epistemic Stance {#epistemic-stance}

> *Adopted from CGA Foundational Concepts §3.1.*

The holonic graph architecture adopts a **stratified epistemic model**.
Different named graph layers carry different epistemic statuses:

- The **interior graph** is the system's operational best belief about the
  holon's current state, governed by the Closed World Assumption.
- The **context/temporal graph** (event history) is the authoritative record
  of what was asserted, by whom, and when.
- The **projection graph** is what the holon chooses to present externally —
  curated, not necessarily complete.
- The **boundary membrane** (shapes graph) is normative, not truth-apt: it
  specifies what conformant states look like.

All state triples in the interior graph are **beliefs** — assertions with
an implicit or explicit confidence value. Confidence is carried in the
`{| |}` reifier annotation:

```turtle
GRAPH <urn:holon:geo:canada:bc:vancouver> {
    <urn:holon:geo:canada:bc:vancouver>
        geo:population "675218"^^xsd:integer
        {|
            cga:beliefConfidence "0.99"^^xsd:decimal ;
            cga:informedBy       <urn:event:census:canada-2026> ;
            prov:wasAttributedTo <urn:org:statistics-canada>
        |} .
}
```

---

## 3. Namespace and Prefix Declarations {#namespaces}

> **Note on namespace resolution.** The `cga:` namespace IRI is provisional.
> The Foundational Concepts document uses `https://w3c.github.io/context-graph/ns#`;
> this specification proposes `https://w3c-context-graph-community-group.github.io/ns/cga#`
> to reflect the Community Group's GitHub organisation. A CG decision is required
> to stabilise this IRI before any dependent specification is published.
> See [§11](#discrepancy-log) for the full discrepancy record.

All examples in this document use the following prefix declarations:

```turtle
@prefix rdf:    <http://www.w3.org/1999/02/22-rdf-syntax-ns#> .
@prefix rdfs:   <http://www.w3.org/2000/01/rdf-schema#> .
@prefix owl:    <http://www.w3.org/2002/07/owl#> .
@prefix xsd:    <http://www.w3.org/2001/XMLSchema#> .
@prefix sh:     <http://www.w3.org/ns/shacl#> .
@prefix skos:   <http://www.w3.org/2004/02/skos/core#> .
@prefix dct:    <http://purl.org/dc/terms/> .
@prefix prov:   <http://www.w3.org/ns/prov#> .
@prefix geo:    <http://www.opengis.net/ont/geosparql#> .
@prefix schema: <https://schema.org/> .
@prefix time:   <http://www.w3.org/2006/time#> .
@prefix cga:    <https://w3c-context-graph-community-group.github.io/ns/cga#> .
@prefix tensor: <https://w3c-context-graph-community-group.github.io/ns/tensor#> .
@prefix crs:    <https://w3c-context-graph-community-group.github.io/ns/crs#> .
```

The `tensor:` and `crs:` namespaces support coordinate frame and tensor value
representations described in [§4.3](#coordinates). These are proposed extensions
requiring separate CG consideration.

---

## 4. The Interior Graph {#interior-graph}

The interior graph is the holon's private world. It contains the triples
constituting the holon's own assertions about itself and its contents.
Nothing outside the holon placed these facts here; nothing outside the holon
is responsible for their consistency.

The interior graph is governed by the Closed World Assumption within its
named graph scope. SHACL shapes applied to the interior treat it as
complete: the absence of a triple is the absence of that fact, not the
unknown status of that fact.

### 4.1 Self-Referential Identity {#self-identity}

Every conformant holon interior contains a **self-referential identity triple**
— a triple in which the graph's own IRI is both subject and the target of
a `rdf:type cga:Holon` assertion, annotated via `{| |}` with structural
metadata:

```turtle
GRAPH <urn:holon:geo:canada:bc:vancouver> {

    <urn:holon:geo:canada:bc:vancouver>
        a cga:Holon, geo:City
        ~ cga:VancouverIdentity
        {|
            cga:holonDepth   "3"^^xsd:integer ;
            cga:schemaVersion "1.0"^^xsd:string ;
            cga:validatedBy  <urn:holon:geo:canada:bc:vancouver/boundary> ;
            cga:createdAt    "2026-01-01T00:00:00Z"^^xsd:dateTime ;
            prov:wasAttributedTo <urn:org:city-of-vancouver>
        |} ;
        rdfs:label "Vancouver"@en .
}
```

The named reifier `cga:VancouverIdentity` makes the identity assertion
independently addressable — it can be the subject of further provenance
assertions or referenced by validation rules. The `~` notation is defined
in RDF 1.2 and supported in Turtle 1.2.

The self-referential triple is not logically circular. Making statements
about a graph's own IRI within the graph it names is well-defined in RDF 1.2.
The reifier annotation carries metadata about the *holon as a structural
entity*, distinct from the scene graph content that follows.

### 4.2 Scene Graph Structure {#scene-graph}

> *Adopted from CGA Foundational Concepts §5.5.*

The interior graph is a **scene graph** in the graph-theoretic sense: a
collection of entities with positions in a coordinate space and relationships
to one another. The term is borrowed from computer graphics and game engine
design, where a scene graph is the primary data structure for organising a
virtual environment.

In the CGA, the scene graph is an operational data model, not a visualisation
construct. Its nodes are addressable IRIs; its edges are typed RDF properties;
its spatial annotations are typed literals using coordinate frames declared
in the boundary layer.

The scene graph abstraction extends beyond spatial holons:

- A **geographic holon's** scene graph has city entities with GeoSPARQL
  geometry positions
- A **musical holon's** scene graph has motif entities with metric score
  positions (movement, measure, beat, subdivision)
- A **document holon's** scene graph has section entities with ordinal
  positions in a table of contents
- A **dungeon holon's** scene graph has chamber entities with positions in
  a local Cartesian coordinate frame

All of these are scene graphs in the same structural sense; they differ only
in the coordinate frame declared in the boundary membrane.

### 4.3 Coordinate Frames and Tensor Values {#coordinates}

A coordinate value without a frame is numerically meaningless. `49.2827`
is not a latitude until you specify it is measured in degrees, relative to
the WGS84 reference ellipsoid. The frame is the interpretive context that
gives the value meaning.

The CGA treats reference frames as **first-class holons** with their own
four-layer structure. Each frame has an interior (axis definitions, units,
origin), a boundary (conformance constraints on values expressed in this
frame), and a projection (binding to external standards such as EPSG codes).

```turtle
GRAPH <urn:holon:crs:wgs84> {
    <urn:holon:crs:wgs84> a cga:Holon, crs:GeographicCRS ;
        rdfs:label     "WGS 84"@en ;
        crs:epsgCode   "4326"^^xsd:integer ;
        crs:axes       ( crs:Latitude crs:Longitude crs:Altitude ) ;
        crs:units      ( <urn:unit:degree> <urn:unit:degree> <urn:unit:metre> ) ;
        owl:sameAs     <https://www.opengis.net/def/crs/EPSG/0/4326> .
}
```

Spatial coordinates in the interior graph are expressed as **tensor values**
— resources carrying rank, shape, component data, and frame reference:

```turtle
@prefix tensor: <https://w3c-context-graph-community-group.github.io/ns/tensor#> .

# Rank-1 contravariant tensor: position vector in WGS84 frame
<urn:tensor:vancouver:wgs84-centroid> a tensor:Value ;
    tensor:rank       1 ;
    tensor:shape      "3" ;
    tensor:variance   tensor:Contravariant ;
    tensor:frame      <urn:holon:crs:wgs84> ;
    tensor:components "[49.2827, -123.1207, 5.0]"^^xsd:string .
```

Frame-independence is the key property: every tensor value carries a reference
to its frame, so any consuming system can interpret the value without knowledge
of which holon it came from. A portal that crosses a frame boundary carries
a **transformation tensor** in its boundary layer — a 4×4 homogeneous matrix
for affine transformations, or a procedure IRI for non-linear projections.

The coordinate system need not be geographic. Musical position is a tensor
in an abstract metric frame with axes [movement, measure, beat, subdivision]:

```turtle
<urn:tensor:op27n2:mvt1:start> a tensor:Value ;
    tensor:rank       1 ;
    tensor:shape      "4" ;
    tensor:frame      <urn:holon:crs:metric-score-position> ;
    tensor:components "[1, 1, 1, 0]"^^xsd:string .
    # Movement 1, measure 1, beat 1, tick 0
```

### 4.4 Belief Annotation {#belief}

All state triples in the interior graph are beliefs — qualified by source,
confidence, and provenance. Belief annotation is carried in the `{| |}`
reifier and should be present on any triple whose confidence is less than
certain or whose source requires attribution:

```turtle
GRAPH <urn:holon:geo:canada:bc:vancouver> {

    <urn:holon:geo:canada:bc:vancouver>
        geo:population "675218"^^xsd:integer
        ~ cga:VancouverPopulationBelief
        {|
            cga:beliefConfidence  "0.99"^^xsd:decimal ;
            cga:observationWeight "0.85"^^xsd:decimal ;
            cga:priorWeight       "0.15"^^xsd:decimal ;
            cga:informedBy        <urn:event:census:canada-2026> ;
            prov:wasAttributedTo  <urn:org:statistics-canada> ;
            dct:created           "2026-03-01T00:00:00Z"^^xsd:dateTime ;
            cga:validFrom         "2026-01-01"^^xsd:date
        |} .
}
```

The `cga:observationWeight` and `cga:priorWeight` record the Bayesian mixing
coefficients: what fraction of the current belief is driven by the new
observation versus the accumulated prior. This allows S-Box rules to behave
differently for high-confidence versus low-confidence inputs — raising an
alert rather than taking automatic action when confidence falls below a
declared threshold.

---

## 5. The Boundary Membrane {#boundary}

The boundary membrane is the most architecturally consequential of the four
layers. It is the layer that transforms a named graph into a holon by giving
it genuine inside/outside distinction.

The membrane is the SHACL 1.2 shapes graph stored at `<holon-iri>/boundary`.
It performs three distinct functions simultaneously:

- **Structural validation** — it defines what entities may exist within the
  interior and what properties they must have (S-Box)
- **Operational constraint** — it governs valid state transitions and relationships
- **Portal governance** — it defines the controlled openings through which
  traversal into and out of the interior is permitted

A SHACL validation failure against the boundary membrane is not merely a data
quality issue in this model. It is a **broken membrane** — the holon's interior
has leaked something that does not conform to its own boundary definition.

### 5.1 SHACL 1.2 as Formal Membrane {#shacl-membrane}

The CGA uses SHACL 1.2 as specified in:
https://www.w3.org/TR/2026/WD-shacl12-core-20260319/

Key SHACL 1.2 features used in this specification:

| Feature | CGA use |
|---|---|
| `sh:NodeShape` with `sh:closed true` | Closed-world interior enforcement |
| `sh:PropertyShape` | Property-level interior constraints |
| `sh:reifierShape` | Validation of `{| |}` annotation content |
| `sh:SPARQLConstraint` | Cross-holon relational constraints |
| `sh:SPARQLRule` | State transition rules (membrane logic) |
| `sh:deactivated true` | Formal suspension of constraints on subtypes |
| `sh:severity` | Membrane health grading |

SHACL Rules are defined in the Advanced Features sub-specification linked
from the 2026-03-19 Working Draft.

### 5.2 Node Shapes and Closed-World Enforcement {#node-shapes}

The boundary membrane uses `sh:closed true` to enforce that the interior
is impermeable to unspecified predicates. Any property asserted in the
interior that is not declared in the shape is a membrane violation:

```turtle
GRAPH <urn:holon:geo:canada:bc:vancouver/boundary> {

    cga:CityHolonShape a sh:NodeShape ;
        sh:targetClass geo:City ;
        sh:closed      true ;
        sh:ignoredProperties ( rdf:type rdfs:label ) ;

        sh:property [
            sh:path     cga:holonDepth ;
            sh:minCount 1 ;
            sh:maxCount 1 ;
            sh:datatype xsd:integer ;
            sh:hasValue "3"^^xsd:integer ;
            sh:message  "A city holon must be at holonic depth 3."@en ;
            sh:severity sh:Violation
        ] ;

        sh:property [
            sh:path     geo:population ;
            sh:maxCount 1 ;
            sh:datatype xsd:integer ;
            sh:minInclusive "0"^^xsd:integer ;
            sh:severity sh:Warning   # absent is advisory, not critical
        ] ;

        sh:property [
            sh:path     cga:partOf ;
            sh:minCount 1 ;
            sh:maxCount 1 ;   # exclusive containment
            sh:nodeKind sh:IRI ;
            sh:class    geo:Province ;
            sh:severity sh:Violation
        ] .
}
```

The `sh:closed true` declaration, combined with `sh:ignoredProperties`,
specifies exactly which predicates may appear in the interior. This is the
formal expression of the membrane's selective permeability.

### 5.3 Reifier Shapes {#reifier-shapes}

SHACL 1.2 introduces `sh:reifierShape`, which validates the content of
`{| |}` annotations on specific triple patterns. This allows the boundary
membrane to enforce not just that a triple exists but that its annotation
is well-formed:

```turtle
GRAPH <urn:holon:geo:canada:bc:vancouver/boundary> {

    # Validate that belief annotations include confidence and attribution
    cga:BeliefAnnotationShape a sh:NodeShape ;
        sh:reifierShape [
            sh:property [
                sh:path     cga:beliefConfidence ;
                sh:minCount 1 ;
                sh:datatype xsd:decimal ;
                sh:minInclusive "0.0"^^xsd:decimal ;
                sh:maxInclusive "1.0"^^xsd:decimal ;
                sh:severity sh:Warning ;
                sh:message  "Belief triples should carry a confidence annotation."@en
            ] ;
            sh:property [
                sh:path     prov:wasAttributedTo ;
                sh:minCount 1 ;
                sh:nodeKind sh:IRI ;
                sh:severity sh:Info
            ]
        ] .
}
```

### 5.4 SHACL Rules as Membrane Logic {#shacl-rules}

The boundary membrane contains not just constraint shapes but **SHACL Rules**
— forward-chaining inference expressions that fire when a validated event
satisfies a defined pattern, producing new triples as output.

In the holonic model, SHACL Rules perform two membrane functions:

1. **State transition rules** — when a signal or event triggers a valid state
   change, rules update interior graph state and record the transition in the
   temporal context stratum
2. **Projection derivation rules** — rules that read the interior graph and
   construct the outward-facing projection

A state transition rule using `sh:SPARQLRule`:

```turtle
GRAPH <urn:holon:dungeon:blackspire:ossuary/boundary> {

    # Rule: when a DiscoverPortalEvent is validated, set portal status to Known
    cga:DiscoverPortalRule a sh:NodeShape ;
        sh:targetClass cga:Portal ;
        sh:rule [
            a sh:SPARQLRule ;
            sh:order 10 ;
            sh:prefixes cga: ;
            sh:construct """
                CONSTRUCT {
                    ?portal cga:discoveryStatus cga:Known {|
                        cga:version    ?newVersion ;
                        cga:derivedBy  <urn:rule:discover-portal> ;
                        dct:created    ?now
                    |}
                }
                WHERE {
                    ?portal a cga:Portal ;
                            cga:discoveryStatus cga:Unknown .
                    FILTER EXISTS {
                        GRAPH ?eventGraph {
                            ?event a cga:DiscoverPortalEvent ;
                                   cga:targetPortal ?portal .
                        }
                    }
                    BIND (NOW() AS ?now)
                    BIND ("1"^^xsd:integer AS ?newVersion)
                }
            """
        ] .
}
```

SHACL Rules are the preferred mechanism over SPARQL UPDATE in this
specification because they are shape-targeted (they fire only on focus nodes
satisfying the shape's conditions), ordered (`sh:order`), and governed by
the same shapes machinery as constraints — preventing unconstrained writes
to the interior graph.

### 5.5 Membrane Health and Severity Mapping {#severity}

SHACL severity levels map onto a holonic **membrane health** model:

| `sh:severity` | Membrane interpretation | Consequence |
|---|---|---|
| `sh:Violation` | Membrane breach — interior data is incoherent | Holon is invalid; traversal through it is unsafe |
| `sh:Warning` | Membrane weakness — data valid but incomplete | Holon navigable; projection may be unreliable |
| `sh:Info` | Membrane advisory — best practice not followed | Holon fully functional; refinement recommended |

A SHACL validation report against a holonic graph is therefore a
**membrane health report** — interpretable in structural terms, not merely
as a data quality checklist.

---

## 6. Portals {#portals}

### 6.1 Portals as First-Class Boundary Objects {#portal-nature}

A **portal** is a boundary membrane object that belongs to the source holon
but resolves to the target holon's identity. It is the controlled opening
through which traversal from one holon to another is possible.

Portals are not simple containment links. A triple like:

```turtle
<urn:holon:geo:canada> cga:contains <urn:holon:geo:canada:british-columbia> .
```

asserts containment but says nothing about how traversal works, what conditions
govern it, what the traversal looks like to a rendering agent, or whether the
connection is currently active. A portal reifies all of that into a first-class
entity with its own IRI, its own type, its own state, and its own provenance.

The key architectural principle: **traversal in a holonic graph is a transition
between epistemic contexts**. When crossing from the Canada holon into the
British Columbia holon, the agent moves from one named graph (one set of
assertions, one authority, one boundary ruleset) into another. A portal is
the object that makes that transition legible, governable, and annotatable.

Portals live in the **boundary membrane of their source holon** — not in the
interior, not in the projection, not in the context graph. This placement is
load-bearing:

- The interior is the holon's private domain; portals are structural boundary
  features, not domain content
- The projection is what the holon broadcasts outward; portals govern inward
  access, not outward signals
- The context graph records membership and history; portals are mechanisms,
  not membership assertions

### 6.2 Portal Types {#portal-types}

Portal types are expressed as SKOS concepts within the `cga:PortalTypeScheme`
concept scheme (see [§9](#skos)). Core portal types:

```turtle
cga:PortalTypeScheme a skos:ConceptScheme ;
    rdfs:label "CGA Portal Type Scheme"@en ;
    dct:description "Controlled vocabulary of portal types in the holonic model."@en .

cga:Portal a skos:Concept ;
    skos:inScheme     cga:PortalTypeScheme ;
    skos:prefLabel    "Portal"@en ;
    skos:definition   "Base concept for all portal types."@en .

cga:UnidirectionalPortal a skos:Concept ;
    skos:inScheme     cga:PortalTypeScheme ;
    skos:prefLabel    "Unidirectional portal"@en ;
    skos:definition   "Traversal permitted in source-to-target direction only."@en .

cga:BidirectionalPortal a skos:Concept ;
    skos:inScheme     cga:PortalTypeScheme ;
    skos:prefLabel    "Bidirectional portal"@en ;
    skos:definition   "Traversal permitted in both directions."@en .

cga:SealedPortal a skos:Concept ;
    skos:inScheme     cga:PortalTypeScheme ;
    skos:prefLabel    "Sealed portal"@en ;
    skos:definition   "Portal exists but traversal is currently blocked."@en .

cga:IconPortal a skos:Concept ;
    skos:inScheme     cga:PortalTypeScheme ;
    skos:prefLabel    "Icon portal"@en ;
    skos:definition   "Portal whose activation mechanism is a visual icon in the source holon's boundary layer."@en .

cga:RoutePortal a skos:Concept ;
    skos:inScheme     cga:PortalTypeScheme ;
    skos:prefLabel    "Route portal"@en ;
    skos:definition   "Portal belonging to a route holon, connecting the route to a terminus holon."@en .
```

Note the use of `skos:inScheme` rather than `skos:broader` for concept
membership. The preference for `skos:inScheme` in this specification is
explained fully in [§9.2](#inscheme).

### 6.3 Portal Activation and Traversal {#portal-traversal}

An **activation event** is the interaction type that triggers portal traversal.
Activation events are declared in the portal's boundary definition and are
SKOS concepts within `cga:ActivationEventScheme`:

```turtle
cga:ActivationEventScheme a skos:ConceptScheme ;
    rdfs:label "CGA Portal Activation Event Scheme"@en .

cga:ClickActivation a skos:Concept ;
    skos:inScheme  cga:ActivationEventScheme ;
    skos:prefLabel "Click activation"@en .

cga:AutoActivation a skos:Concept ;
    skos:inScheme  cga:ActivationEventScheme ;
    skos:prefLabel "Automatic activation"@en ;
    skos:definition "Traversal occurs implicitly upon entering the source holon."@en .

cga:CredentialActivation a skos:Concept ;
    skos:inScheme  cga:ActivationEventScheme ;
    skos:prefLabel "Credential-gated activation"@en ;
    skos:definition "Traversal requires presentation of a verifiable credential."@en .
```

A complete icon portal definition:

```turtle
GRAPH <urn:holon:geo:canada/boundary> {

    <urn:holon:geo:canada>
        cga:hasPortal <urn:portal:icon:canada:bc> .

    <urn:portal:icon:canada:bc> a cga:Holon ;
        cga:portalType       cga:IconPortal ;
        cga:sourceHolon      <urn:holon:geo:canada> ;
        cga:targetHolon      <urn:holon:geo:canada:british-columbia> ;
        cga:activationEvent  cga:ClickActivation ;
        cga:iconLabel        "British Columbia"@en ;
        cga:iconImage        <urn:asset:shield:ca-bc> ;
        cga:iconPosition     <urn:tensor:centroid:bc>
        {|
            dct:description  "Provincial shield icon; centroid at 53.7°N 127.6°W"@en ;
            cga:bindingStrength cga:StrongBinding ;
            dct:created      "2026-01-01"^^xsd:date ;
            prov:wasAttributedTo <urn:org:government-canada>
        |} .

    <urn:asset:shield:ca-bc> a schema:ImageObject ;
        schema:contentUrl     <https://assets.example.org/shields/ca-bc.svg> ;
        schema:encodingFormat "image/svg+xml" .

    <urn:tensor:centroid:bc> a tensor:Value ;
        tensor:rank       1 ;
        tensor:shape      "2" ;
        tensor:frame      <urn:holon:crs:wgs84> ;
        tensor:components "[53.7267, -127.6476]"^^xsd:string .
}
```

### 6.4 Frame Transformation at Portal Boundaries {#portal-transform}

When a portal crosses a frame boundary — the source and target holons use
different coordinate reference systems — the portal carries a **frame
transformation** in its boundary definition:

```turtle
GRAPH <urn:holon:dungeon:blackspire:ossuary/boundary> {

    # Portal from Ossuary (local Cartesian) to Throne Room (different local Cartesian)
    # Transformation: rotate 90° about Z, translate [15, 3, 0] metres

    <urn:portal:blackspire:ossuary-to-throne>
        crs:sourceFrame    <urn:holon:crs:ossuary-local> ;
        crs:targetFrame    <urn:holon:crs:throne-room-local> ;
        crs:frameTransform <urn:transform:ossuary-to-throne> .

    # 4×4 homogeneous transformation matrix (row-major)
    <urn:transform:ossuary-to-throne> a crs:AffineTransform ;
        crs:matrix <urn:tensor:transform:ossuary-throne> .

    <urn:tensor:transform:ossuary-throne> a tensor:Value ;
        tensor:rank       2 ;
        tensor:shape      "4,4" ;
        tensor:variance   tensor:Mixed ;
        tensor:components "[[0,-1,0,15],[1,0,0,3],[0,0,1,0],[0,0,0,1]]"^^xsd:string .

    # Decomposed form for tooling compatibility
    <urn:transform:ossuary-to-throne>
        crs:translation <urn:tensor:ossuary-throne-t> ;
        crs:rotation    <urn:tensor:ossuary-throne-r> .

    <urn:tensor:ossuary-throne-t> a tensor:Value ;
        tensor:rank       1 ;
        tensor:shape      "3" ;
        tensor:components "[15.0, 3.0, 0.0]"^^xsd:string .

    <urn:tensor:ossuary-throne-r> a tensor:Value ;
        tensor:rank       1 ;
        tensor:shape      "4" ;        # quaternion [qx, qy, qz, qw]
        tensor:components "[0.0, 0.0, 0.7071, 0.7071]"^^xsd:string .
}
```

A frame transformation is not merely presentation metadata. It is the
object that makes spatial coordinates portable across holons with different
local coordinate systems — the mathematical joint at every portal through
which geometry moves coherently from one context to another.

### 6.5 Shared Portals: Routes and Networks {#shared-portals}

A route (highway, railway, corridor) is not a portal. A portal is owned by
its source holon, lives in that holon's boundary membrane, and is a mechanism
with no existence independent of the source/target relationship it serves.
A route has **ontological independence**: it has its own governance authority,
its own physical properties, its own sub-structure, and it exists whether
or not either terminus exists in the graph.

Routes are therefore **first-class holons** with their own four-layer
structure. Their portals resolve to city or terminus holons. The city holons
*reference* but do not *own* those portals. This produces an ownership
distinction between two portal-related predicates:

- `cga:hasPortal` — ownership: the holon defines and controls this portal
- `cga:exposesPortal` — reference: the holon acknowledges this portal exists
  at its boundary but does not control it

```turtle
# The route owns the portal
GRAPH <urn:holon:route:hwy1-bc/boundary> {
    <urn:portal:hwy1-bc:west-terminus>
        a cga:Holon ;
        cga:portalType      cga:RoutePortal ;
        cga:sourceHolon     <urn:holon:route:hwy1-bc> ;
        cga:targetHolon     <urn:holon:geo:canada:bc:vancouver> .
}

# The city exposes (but does not own) the portal
GRAPH <urn:holon:geo:canada:bc:vancouver/boundary> {
    <urn:holon:geo:canada:bc:vancouver>
        cga:exposesPortal <urn:portal:hwy1-bc:west-terminus> .
}
```

### 6.6 Reification of Portal Bindings {#portal-reification}

Properties of the *connection* — travel time, distance, provenance,
confidence — belong neither to the route nor to the city but to the
assertion that the portal resolves to its target. This is the canonical
use case for RDF 1.2 `{| |}` annotation:

```turtle
GRAPH <urn:holon:route:hwy1-bc/boundary> {

    <urn:portal:hwy1-bc:west-terminus>
        cga:targetHolon <urn:holon:geo:canada:bc:vancouver>
        ~ cga:Hwy1VancouverBinding
        {|
            transport:travelTimeTypical   "PT1H45M"^^xsd:duration ;
            transport:travelTimeWorstCase "PT3H30M"^^xsd:duration ;
            transport:distanceKm         "148.0"^^xsd:decimal ;
            transport:tollRequired       false ;
            dct:created                  "1962-07-30"^^xsd:date ;
            dct:modified                 "2026-01-15"^^xsd:date ;
            prov:wasAttributedTo         <urn:org:bc-ministry-transport> ;
            cga:bindingStrength          cga:StrongBinding ;
            cga:confidence               "0.97"^^xsd:decimal
        |} .
}
```

The reifier `cga:Hwy1VancouverBinding` names the binding assertion, making
it independently addressable for query and further annotation. The travel
time is a fact about the *claim* that this portal connects to Vancouver —
not a fact about the route or about Vancouver independently.

---

## 7. The Projection Graph {#projection}

### 7.1 Projection as Curated Exterior {#projection-exterior}

The projection graph is the holon's outward face — the curated, controlled
representation that external systems consume. It is derived from the interior
by a SHACL Rule that selects, translates, and reduces interior content into
a form appropriate for external consumption.

The projection is never directly writable. It is always derived. An agent
observing the exterior of a holon knows only what that holon has chosen to
project. This is the technical realisation of Koestler's self-assertive
tendency: the holon controls its own representation.

The projection graph also differs from the interior in vocabulary. The
interior may use a local vocabulary understood only within the holarchy;
the projection uses external authority vocabularies (`schema:`, `skos:`,
`geo:`, `dct:`) that downstream systems can consume without knowledge of the
interior's structure.

### 7.2 External Bindings {#external-bindings}

The projection graph is where **external bindings** live — the assertions
that connect this holon to the broader linked data ecosystem. The choice
of binding predicate matters:

| Predicate | Semantics | When to use |
|---|---|---|
| `owl:sameAs` | Full logical identity; triggers OWL inference | Only when the holon *is* the external resource with no representational distinction |
| `skos:exactMatch` | Equivalent in context; no entailment | Strong alignment across vocabularies |
| `skos:closeMatch` | Substantially similar | Approximate alignment |
| `cga:bindsTo` | Directional binding; source is a local representation of target | External resource is authoritative source |
| `cga:projectsFrom` | Target is the authoritative original | Holon is a local projection of external resource |
| `schema:sameAs` | LOD linking; weak identity | Linking to Wikidata, DBpedia for discovery |

The default recommendation is `cga:bindsTo` for linking a holon to its
canonical external identifier, with `skos:exactMatch` for vocabulary-level
alignment. `owl:sameAs` should be used sparingly because it collapses all
inferred properties of both resources — typically not the intended effect
when binding a local holon to an external authority.

```turtle
GRAPH <urn:holon:geo:canada:bc:vancouver/projection> {

    <urn:holon:geo:canada:bc:vancouver>
        cga:bindsTo    <https://sws.geonames.org/6173331/>
        {|
            cga:bindingStrength  cga:StrongBinding ;
            dct:created          "2026-01-01"^^xsd:date ;
            prov:wasAttributedTo <urn:org:cga-editorial>
        |} ;

        skos:exactMatch <https://www.wikidata.org/entity/Q24639> ;
        schema:sameAs   <https://dbpedia.org/resource/Vancouver> .

    # Outward-facing description in schema.org vocabulary
    <urn:holon:geo:canada:bc:vancouver>
        a schema:City ;
        schema:name          "Vancouver"@en ;
        schema:addressRegion "British Columbia" ;
        schema:addressCountry "CA" ;
        schema:geo [
            a schema:GeoCoordinates ;
            schema:latitude  "49.2827"^^xsd:decimal ;
            schema:longitude "-123.1207"^^xsd:decimal
        ] .
}
```

### 7.3 Projection Shapes {#projection-shapes}

The boundary membrane includes SHACL shapes that govern the projection —
ensuring that the external face meets interoperability requirements
independently of whether the interior is fully populated:

```turtle
GRAPH <urn:holon:geo:canada:bc:vancouver/boundary> {

    cga:CityProjectionShape a sh:NodeShape ;
        sh:targetClass geo:City ;
        sh:property [
            sh:path [ sh:alternativePath (
                cga:bindsTo skos:exactMatch schema:sameAs
            ) ] ;
            sh:minCount 1 ;
            sh:nodeKind sh:IRI ;
            sh:severity sh:Warning ;
            sh:message  "City projection has no external IRI binding."@en
        ] ;
        sh:sparql [
            a sh:SPARQLConstraint ;
            sh:message "External binding {?value} is not from a recognised authority."@en ;
            sh:severity sh:Info ;
            sh:prefixes cga: ;
            sh:select """
                SELECT $this ?value WHERE {
                    $this cga:bindsTo|skos:exactMatch ?value .
                    FILTER (
                        !STRSTARTS(STR(?value), "https://sws.geonames.org/") &&
                        !STRSTARTS(STR(?value), "https://www.wikidata.org/entity/") &&
                        !STRSTARTS(STR(?value), "https://dbpedia.org/resource/")
                    )
                }
            """
        ] .
}
```

---

## 8. The Context Graph Layer {#context-layer}

### 8.1 Two Strata: Structural and Temporal {#strata}

> **Discrepancy note.** The Glossary defines a Context Graph as "an
> auditable, append-only event log." The Foundational Concepts document
> defines it as "a named graph that carries interpretation context." These
> are not the same concept. This specification resolves the discrepancy
> by treating the context layer as having **two strata** — one structural
> (scene graph membership, stable) and one temporal (event log, accumulating).
> Neither stratum alone is "the context graph"; both are components of the
> context layer of the holon. See [§11](#discrepancy-log) for full discussion.

The context graph layer of a holon contains:

- **`/context/structural`** — stable membership in the holarchy, adjacency,
  and cross-cutting associations. Changes only on structural reorganisation.
- **`/context/temporal`** — event-derived temporal annotations accumulated
  over time via validated signal processing and SHACL rule firing.

The parent IRI `<holon-iri>/context` serves as a virtual union view over
both strata for queries that need the full context picture.

### 8.2 The Structural Stratum as Scene Graph {#structural-stratum}

The structural stratum records the holon's stable membership in the holarchy
and any cross-cutting affiliations that do not change with normal operation:

```turtle
GRAPH <urn:holon:geo:canada:bc:vancouver/context/structural> {

    <urn:holon:geo:canada:bc:vancouver>
        cga:memberOf   <urn:holon:geo:canada:british-columbia>
        {|
            prov:wasAttributedTo <urn:org:statistics-canada> ;
            dct:created          "2026-01-01"^^xsd:date ;
            cga:bindingStrength  cga:StrongBinding
        |} ;

        cga:memberOf   <urn:holon:region:metro-vancouver> ;
        cga:adjacentTo <urn:holon:geo:canada:bc:burnaby> ,
                       <urn:holon:geo:canada:bc:richmond> ,
                       <urn:holon:geo:canada:bc:north-vancouver> ;

        cga:participatesIn <urn:holon:network:translink-transit-authority> ,
                           <urn:holon:network:bc-hydro-distribution> .
}
```

The `cga:memberOf` property records containment in the holarchy from the child's
perspective. It is distinct from `cga:partOf` (which asserts geographic or
organisational containment) — `cga:memberOf` asserts holarchic membership, which
may be in multiple containing holons simultaneously (a city is in its province
holarchically, but also a member of a metropolitan region and a transit authority
network).

### 8.3 The Temporal Stratum as Event-Driven Datalog {#temporal-stratum}

The temporal stratum is an append-only record of temporal annotations derived
from validated signals. Each annotation is a PROV-O `prov:Entity` with
provenance tracing to the signal and rule that produced it:

```turtle
GRAPH <urn:holon:geo:canada:bc:vancouver/context/temporal> {

    <urn:holon:geo:canada:bc:vancouver>
        cga:hasTemporalAnnotation <urn:annotation:vancouver:pop-2026> .

    <urn:annotation:vancouver:pop-2026>
        a cga:TemporalAnnotation, prov:Entity ;
        cga:annotates     <urn:holon:geo:canada:bc:vancouver> ;
        cga:effectOn      geo:population ;
        rdf:value         "675218"^^xsd:integer ;
        prov:wasGeneratedBy <urn:activity:census-processing-2026> ;
        prov:wasDerivedFrom <urn:signal:statistics-canada:census-2026>
        {|
            cga:validFrom      "2026-01-01"^^xsd:date ;
            cga:confidence     "0.99"^^xsd:decimal ;
            dct:created        "2026-03-01T09:00:00Z"^^xsd:dateTime ;
            cga:derivedByRule  <urn:rule:population-update-from-census>
        |} .
}
```

The temporal stratum is distinct from the structural stratum in management
characteristics: it is append-only and grows continuously, has different
retention requirements, and requires different indexing strategies for
temporal range queries.

### 8.4 Signals, Rules, and Annotation Deposition {#signals-rules}

Signals enter the model through the boundary membrane. A signal is not an
interior fact and not a structural membership assertion — it is something
crossing the membrane from outside. The boundary layer governs this crossing
through two types of membrane rule:

- **SHACL shapes** — validate structural conformance of the signal
- **SHACL Rules** — fire on signal patterns and produce temporal annotations
  deposited into `/context/temporal`

The signal ingestion pipeline is:

```
External signal
  → authenticated (Verifiable Credentials)
  → validated (SHACL shapes on signal graph)
  → matched (SHACL Rules antecedent patterns)
  → consequents derived (new triples)
  → deposited as TemporalAnnotations into /context/temporal
```

A SHACL Rule for population update from a census signal:

```turtle
GRAPH <urn:holon:geo:canada:bc:vancouver/boundary> {

    cga:PopulationUpdateRule a sh:NodeShape ;
        sh:targetClass cga:CensusSignal ;
        sh:rule [
            a sh:SPARQLRule ;
            sh:order 20 ;
            sh:prefixes cga: ;
            sh:construct """
                CONSTRUCT {
                    <urn:holon:geo:canada:bc:vancouver>
                        cga:hasTemporalAnnotation [
                            a cga:TemporalAnnotation, prov:Entity ;
                            cga:annotates    <urn:holon:geo:canada:bc:vancouver> ;
                            cga:effectOn     geo:population ;
                            rdf:value        ?population ;
                            prov:wasDerivedFrom ?signal ;
                            cga:derivedByRule cga:PopulationUpdateRule ;
                            dct:created      ?now
                        ] .
                }
                WHERE {
                    ?signal a cga:CensusSignal ;
                            cga:targetHolon <urn:holon:geo:canada:bc:vancouver> ;
                            cga:payloadValue ?population ;
                            cga:signalTime   ?sigTime .
                    BIND (NOW() AS ?now)
                }
            """
        ] .
}
```

### 8.5 PROV-O Integration {#provo}

The W3C Provenance Ontology (PROV-O) provides the process and provenance
vocabulary for the temporal stratum. PROV-O's three core classes map onto
holonic temporal annotation:

| PROV-O class | CGA role |
|---|---|
| `prov:Entity` | `cga:TemporalAnnotation` — the derived fact |
| `prov:Activity` | The rule execution or processing activity that produced the annotation |
| `prov:Agent` | The authority responsible: the signal source, the rule set, or the human annotator |

The PROV-O relationships in the temporal stratum:

```turtle
GRAPH <urn:holon:geo:canada:bc:vancouver/context/temporal> {

    # The annotation is a prov:Entity
    <urn:annotation:vancouver:pop-2026>
        a cga:TemporalAnnotation, prov:Entity ;
        prov:wasGeneratedBy   <urn:activity:census-processing-2026> ;
        prov:wasDerivedFrom   <urn:signal:statistics-canada:census-2026> ;
        prov:wasAttributedTo  <urn:org:statistics-canada> .

    # The processing activity
    <urn:activity:census-processing-2026>
        a prov:Activity ;
        prov:startedAtTime  "2026-03-01T09:00:00Z"^^xsd:dateTime ;
        prov:endedAtTime    "2026-03-01T09:00:01Z"^^xsd:dateTime ;
        prov:wasAssociatedWith <urn:rule:population-update-from-census> ;
        prov:used           <urn:signal:statistics-canada:census-2026> .

    # The signal as source prov:Entity
    <urn:signal:statistics-canada:census-2026>
        a prov:Entity, cga:ValidatedSignal ;
        prov:wasAttributedTo <urn:org:statistics-canada> ;
        dct:created         "2026-03-01T08:59:00Z"^^xsd:dateTime .
}
```

PROV-O integration ensures that every temporal annotation carries a complete,
queryable provenance chain from signal source through processing activity to
derived annotation. This enables post-hoc audit of how any temporal fact was
produced — a requirement for accountable knowledge graphs in operational
deployments.

---

## 9. SKOS Vocabulary Layer {#skos}

### 9.1 Concept Schemes in Holonic Graphs {#skos-schemes}

The X-Box vocabulary layer in the holonic model is expressed as SKOS concept
schemes. SKOS provides structure beyond flat enumeration: concept schemes can
express hierarchical classification, equivalence across vocabularies, and
preferred/alternate labelling across languages.

In the holonic model, SKOS concept schemes serve several roles:

- **Portal type enumeration** — the controlled vocabulary of portal types
- **Activation event types** — how traversal is triggered
- **Binding strength levels** — how confident a binding assertion is
- **Holonic lifecycle states** — the valid states of a holon through its
  existence
- **Coordinate system types** — the classification of CRS families
- **Trust levels** — the epistemic classification of assertion sources

All of these are X-Box content: shared vocabularies consumed by T-Box class
definitions, A-Box instance assertions, and S-Box constraint shapes.

### 9.2 inScheme over Broader: Rationale and Pattern {#inscheme}

> **Design preference.** This specification consistently uses `skos:inScheme`
> to assert concept membership within a scheme, rather than `skos:broader`
> to encode hierarchy.

The rationale is architectural. `skos:broader` asserts a hierarchical
relationship between two concepts — that one concept is more general than
another. It is appropriate for encoding *is-a* relationships within a
classification hierarchy.

`skos:inScheme` asserts that a concept is a member of a scheme — that it
belongs to a controlled vocabulary. This is the correct assertion for
enumerating the members of a closed controlled vocabulary such as portal
types, lifecycle states, or trust levels.

Conflating the two produces schemes where hierarchy is implicit in
membership — which makes it impossible to distinguish flat enumerations
from genuine hierarchical taxonomies. The pattern in this specification
separates the two concerns:

1. Use `skos:inScheme` to state that a concept belongs to a scheme
2. Use `skos:broader` / `skos:narrower` *additionally* when genuine
   hierarchical relationships exist within the scheme

```turtle
# Example: Trust Level scheme — flat enumeration
cga:TrustLevelScheme a skos:ConceptScheme ;
    rdfs:label   "CGA Trust Level Scheme"@en ;
    dct:isPartOf cga:CoreVocabulary .

cga:Unverified a skos:Concept ;
    skos:inScheme   cga:TrustLevelScheme ;
    skos:prefLabel  "Unverified"@en ;
    skos:definition "Raw signal in the ingestion graph; no assertional status."@en .

cga:DeviceAttested a skos:Concept ;
    skos:inScheme   cga:TrustLevelScheme ;
    skos:prefLabel  "Device attested"@en .

cga:CredentialTrusted a skos:Concept ;
    skos:inScheme   cga:TrustLevelScheme ;
    skos:prefLabel  "Credential trusted"@en .

cga:CryptoVerified a skos:Concept ;
    skos:inScheme   cga:TrustLevelScheme ;
    skos:prefLabel  "Cryptographically verified"@en .

cga:RuleDerived a skos:Concept ;
    skos:inScheme   cga:TrustLevelScheme ;
    skos:prefLabel  "Rule derived"@en ;
    skos:definition "Assertion generated by a validated SHACL rule set."@en .
```

When a scheme *does* have genuine hierarchy — such as a geographic
classification where nation-state is broader than province — `skos:broader`
is appropriate *in addition to* `skos:inScheme`:

```turtle
cga:Province a skos:Concept ;
    skos:inScheme  cga:AdminDivisionScheme ;
    skos:prefLabel "Province"@en ;
    skos:broader   cga:NationState .     # also inScheme, stated separately

cga:NationState a skos:Concept ;
    skos:inScheme  cga:AdminDivisionScheme ;
    skos:prefLabel "Nation state"@en .
```

### 9.3 Cross-Holon Vocabulary Alignment {#skos-alignment}

When two holons use different SKOS vocabularies for the same domain — a common
condition in federated knowledge graphs — SKOS alignment properties record
the correspondence in the projection graph of the source holon:

```turtle
GRAPH <urn:holon:geo:canada:bc:vancouver/projection> {

    # Local concept aligned to external Wikidata concept
    cga:City skos:exactMatch <https://www.wikidata.org/entity/Q515>
    {|
        dct:created          "2026-01-01"^^xsd:date ;
        prov:wasAttributedTo <urn:org:cga-editorial>
    |} .

    cga:Province skos:exactMatch <https://www.wikidata.org/entity/Q34763> .
}
```

Cross-holon SPARQL constraints in the boundary membrane can validate
alignment consistency — flagging when a binding uses `skos:exactMatch`
for a concept that is actually only a `skos:closeMatch`:

```turtle
GRAPH <urn:holon:geo:canada:bc:vancouver/boundary> {

    cga:AlignmentConsistencyShape a sh:NodeShape ;
        sh:targetClass skos:Concept ;
        sh:sparql [
            a sh:SPARQLConstraint ;
            sh:message "Concept {$this} uses exactMatch but definitions diverge significantly."@en ;
            sh:severity sh:Info ;
            sh:select """
                SELECT $this ?external WHERE {
                    $this skos:exactMatch ?external .
                    $this skos:definition ?localDef .
                    ?external skos:definition ?extDef .
                    FILTER (?localDef != ?extDef)
                }
            """
        ] .
}
```

### 9.4 SKOS and the X-Box {#skos-xbox}

The X-Box in the CGA holonic model is composed of multiple SKOS concept
schemes, each maintained as a named graph addressable within the holarchy.
The X-Box is a shared resource across all holons — changes to it require
community governance as described in the Foundational Concepts §11.3.

The following core concept schemes are defined in the `cga:` X-Box:

| Scheme IRI | Domain |
|---|---|
| `cga:PortalTypeScheme` | Portal classification |
| `cga:ActivationEventScheme` | Portal activation event types |
| `cga:BindingStrengthScheme` | Binding confidence levels |
| `cga:HolonLifecycleScheme` | Holon lifecycle states |
| `cga:TrustLevelScheme` | Assertion source trust classification |
| `cga:MembraneHealthScheme` | SHACL severity / membrane health levels |
| `cga:CoordinateSystemTypeScheme` | CRS family classification |
| `cga:TemporalAnnotationTypeScheme` | Event and annotation classification |

Each scheme is declared as `skos:inScheme` of a parent meta-scheme
`cga:CoreVocabulary` — the X-Box root. This allows a SPARQL query to
enumerate all CGA concept schemes without hardcoding their IRIs.

---

## 10. Worked Examples {#examples}

### 10.1 Geographic Holarchy: Continent to City {#geo-example}

A four-level holarchy (depth 0–3) demonstrating containment, portals,
external bindings, and context layers:

```turtle
@prefix cga:    <https://w3c-context-graph-community-group.github.io/ns/cga#> .
@prefix geo:    <http://www.opengis.net/ont/geosparql#> .
@prefix skos:   <http://www.w3.org/2004/02/skos/core#> .
@prefix dct:    <http://purl.org/dc/terms/> .
@prefix prov:   <http://www.w3.org/ns/prov#> .
@prefix rdfs:   <http://www.w3.org/2000/01/rdf-schema#> .
@prefix xsd:    <http://www.w3.org/2001/XMLSchema#> .

# ── Depth 0: Continent ──────────────────────────────────────────────

GRAPH <urn:holon:geo:north-america> {
    <urn:holon:geo:north-america>
        a cga:Holon, geo:Feature ;
        rdfs:label    "North America"@en ;
        cga:holonDepth "0"^^xsd:integer ;
        cga:contains  <urn:holon:geo:canada> ,
                      <urn:holon:geo:usa> ,
                      <urn:holon:geo:mexico> .
}

GRAPH <urn:holon:geo:north-america/boundary> {

    cga:ContinentShape a sh:NodeShape ;
        sh:targetClass geo:Feature ;
        sh:closed true ;
        sh:property [
            sh:path     cga:holonDepth ;
            sh:hasValue "0"^^xsd:integer ;
            sh:severity sh:Violation
        ] .

    # Icon portals to child countries
    <urn:holon:geo:north-america>
        cga:hasPortal
            <urn:portal:icon:na:canada> ,
            <urn:portal:icon:na:usa> ,
            <urn:portal:icon:na:mexico> .

    <urn:portal:icon:na:canada> a cga:Holon ;
        cga:portalType      cga:IconPortal ;
        cga:sourceHolon     <urn:holon:geo:north-america> ;
        cga:targetHolon     <urn:holon:geo:canada> ;
        cga:activationEvent cga:ClickActivation ;
        cga:iconLabel       "Canada"@en ;
        cga:iconImage       <urn:asset:flag:ca> .
}

# ── Depth 1: Country ────────────────────────────────────────────────

GRAPH <urn:holon:geo:canada> {
    <urn:holon:geo:canada>
        a cga:Holon, geo:Feature ;
        rdfs:label     "Canada"@en ;
        cga:holonDepth "1"^^xsd:integer ;
        cga:partOf     <urn:holon:geo:north-america> ;
        cga:contains   <urn:holon:geo:canada:british-columbia> ,
                       <urn:holon:geo:canada:ontario> ,
                       <urn:holon:geo:canada:quebec> .
}

GRAPH <urn:holon:geo:canada/projection> {
    <urn:holon:geo:canada>
        skos:exactMatch <https://www.wikidata.org/entity/Q16>
        {| cga:bindingStrength cga:StrongBinding |} ;
        cga:bindsTo <https://sws.geonames.org/6251999/> .
}

GRAPH <urn:holon:geo:canada/context/structural> {
    <urn:holon:geo:canada>
        cga:memberOf <urn:holon:geo:north-america> ;
        cga:memberOf <urn:holon:org:g7> ;
        cga:memberOf <urn:holon:org:un> .
}

# ── Depth 2: Province ───────────────────────────────────────────────

GRAPH <urn:holon:geo:canada:british-columbia> {
    <urn:holon:geo:canada:british-columbia>
        a cga:Holon, geo:Feature ;
        rdfs:label     "British Columbia"@en ;
        cga:holonDepth "2"^^xsd:integer ;
        cga:partOf     <urn:holon:geo:canada> ;
        cga:contains   <urn:holon:geo:canada:bc:vancouver> ,
                       <urn:holon:geo:canada:bc:victoria> .
}

GRAPH <urn:holon:geo:canada:british-columbia/boundary> {
    <urn:holon:geo:canada:british-columbia>
        cga:hasPortal
            <urn:portal:icon:bc:vancouver> ,
            <urn:portal:icon:bc:victoria> .

    <urn:portal:icon:bc:vancouver> a cga:Holon ;
        cga:portalType      cga:IconPortal ;
        cga:sourceHolon     <urn:holon:geo:canada:british-columbia> ;
        cga:targetHolon     <urn:holon:geo:canada:bc:vancouver> ;
        cga:activationEvent cga:ClickActivation ;
        cga:iconLabel       "Vancouver"@en .
}

# ── Depth 3: City ───────────────────────────────────────────────────

GRAPH <urn:holon:geo:canada:bc:vancouver> {
    <urn:holon:geo:canada:bc:vancouver>
        a cga:Holon, geo:Feature ;
        rdfs:label     "Vancouver"@en ;
        cga:holonDepth "3"^^xsd:integer ;
        cga:partOf     <urn:holon:geo:canada:british-columbia> ;
        geo:population "675218"^^xsd:integer
        {|
            cga:beliefConfidence "0.99"^^xsd:decimal ;
            prov:wasAttributedTo <urn:org:statistics-canada> ;
            cga:validFrom        "2026-01-01"^^xsd:date
        |} .
}
```

### 10.2 Dungeon Holarchy: Chambers and Portals {#dungeon-example}

A fictional holarchy demonstrating lateral portals, sealed portals,
and directional traversal constraints:

```turtle
# ── Dungeon root holon ──────────────────────────────────────────────

GRAPH <urn:holon:dungeon:blackspire> {
    <urn:holon:dungeon:blackspire>
        a cga:Holon ;
        rdfs:label     "Blackspire Vault"@en ;
        cga:holonDepth "0"^^xsd:integer ;
        cga:contains   <urn:holon:dungeon:blackspire:antechamber> ,
                       <urn:holon:dungeon:blackspire:ossuary> ,
                       <urn:holon:dungeon:blackspire:throne-room> .
}

# ── Ossuary chamber holon ───────────────────────────────────────────

GRAPH <urn:holon:dungeon:blackspire:ossuary/boundary> {

    <urn:holon:dungeon:blackspire:ossuary>
        cga:hasPortal
            <urn:portal:blackspire:ossuary-to-throne> ,
            <urn:portal:blackspire:ossuary-collapse> .

    # Lateral portal: Ossuary → Throne Room (peer-to-peer, not containment)
    <urn:portal:blackspire:ossuary-to-throne> a cga:Holon ;
        cga:portalType      cga:BidirectionalPortal ;
        cga:sourceHolon     <urn:holon:dungeon:blackspire:ossuary> ;
        cga:targetHolon     <urn:holon:dungeon:blackspire:throne-room> ;
        cga:activationEvent cga:ClickActivation ;
        rdfs:label          "Hidden Passage to Throne Room"@en ;
        cga:isConcealed     true
        {|
            dct:description "Concealed door behind tapestry; DC 18 Perception"@en ;
            cga:bindingStrength cga:StrongBinding
        |} .

    # Sealed portal: Ossuary → Subvault (holon exists but is unreachable)
    <urn:portal:blackspire:ossuary-collapse> a cga:Holon ;
        cga:portalType      cga:SealedPortal ;
        cga:sourceHolon     <urn:holon:dungeon:blackspire:ossuary> ;
        cga:targetHolon     <urn:holon:dungeon:blackspire:subvault> ;
        cga:isTraversable   false ;
        rdfs:label          "Collapsed Eastern Passage"@en .

    # SHACL 1.2: deactivate icon constraint for sealed portals
    cga:SealedPortalShape a sh:NodeShape ;
        sh:targetNode cga:SealedPortal ;
        sh:property [
            sh:path        cga:iconImage ;
            sh:deactivated true     # sealed portals have no presentation surface
        ] .
}
```

### 10.3 Musical Composition Holarchy {#music-example}

A non-geospatial holarchy demonstrating abstract coordinate frames and
motivic cross-references via projection annotations:

```turtle
@prefix music: <https://w3c-context-graph-community-group.github.io/ns/music#> .

# ── Work holon ──────────────────────────────────────────────────────

GRAPH <urn:holon:music:beethoven:op27n2> {
    <urn:holon:music:beethoven:op27n2>
        a cga:Holon, music:MusicalWork ;
        rdfs:label     "Piano Sonata No. 14 in C♯ minor, Op. 27 No. 2"@en ;
        music:popularTitle "Moonlight Sonata"@en ;
        cga:holonDepth "0"^^xsd:integer ;
        cga:contains   <urn:holon:music:beethoven:op27n2:mvt1> ,
                       <urn:holon:music:beethoven:op27n2:mvt2> ,
                       <urn:holon:music:beethoven:op27n2:mvt3> .
}

# Attacca portal between movements — a tonal frame transformation
GRAPH <urn:holon:music:beethoven:op27n2/boundary> {
    <urn:portal:op27n2:mvt1-to-mvt2> a cga:Holon ;
        cga:portalType      cga:UnidirectionalPortal ;
        cga:sourceHolon     <urn:holon:music:beethoven:op27n2:mvt1> ;
        cga:targetHolon     <urn:holon:music:beethoven:op27n2:mvt2> ;
        music:transition    music:AttaccaSubito ;
        crs:sourceFrame     <urn:holon:crs:csharp-minor-tonal> ;
        crs:targetFrame     <urn:holon:crs:dflat-major-tonal> ;
        crs:frameTransform  <urn:transform:enharmonic-csharp-dflat>
        {|
            dct:description "Enharmonic pivot: C♯ minor → D♭ major"@en
        |} .
}

# ── Motif holon (depth 3) — with abstract tensor coordinate ─────────

GRAPH <urn:holon:music:beethoven:op27n2:mvt1:motif-ostinato> {
    <urn:holon:music:beethoven:op27n2:mvt1:motif-ostinato>
        a cga:Holon, music:Motif ;
        rdfs:label      "Triplet ostinato"@en ;
        cga:holonDepth  "3"^^xsd:integer ;
        cga:partOf      <urn:holon:music:beethoven:op27n2:mvt1:opening> ;
        music:pitchContent <urn:tensor:op27n2:ostinato:pitches> .

    <urn:tensor:op27n2:ostinato:pitches> a tensor:Value ;
        tensor:rank       1 ;
        tensor:shape      "3" ;
        tensor:frame      <urn:holon:crs:tonal-harmonic> ;
        tensor:components "[8, 1, 4]"^^xsd:string .
        # pitch classes: G♯=8, C♯=1, E=4
}

# Motivic cross-reference via projection (reified transformation claim)
GRAPH <urn:holon:music:beethoven:op27n2:mvt1:motif-ostinato/projection> {
    <urn:holon:music:beethoven:op27n2:mvt1:motif-ostinato>
        music:motivicEchoIn
            <urn:holon:music:beethoven:op27n2:mvt3:ostinato-augmented>
        {|
            music:transformation  music:Augmentation ;
            music:transformFactor "2.0"^^xsd:decimal ;
            dct:description       "Triplet cell doubled in rhythmic value in mvt III"@en ;
            prov:wasAttributedTo  <urn:agent:analytical-annotator>
        |} .
}
```

### 10.4 Transport Route as Independent Holon {#route-example}

Demonstrating the shared portal ownership pattern and reification of
relational properties:

```turtle
@prefix transport: <https://w3c-context-graph-community-group.github.io/ns/transport#> .

# ── Route holon ─────────────────────────────────────────────────────

GRAPH <urn:holon:route:trans-canada-hwy1-bc> {
    <urn:holon:route:trans-canada-hwy1-bc>
        a cga:Holon, transport:Highway ;
        rdfs:label          "Trans-Canada Highway · BC segment"@en ;
        transport:routeNumber "1" ;
        transport:operator  <urn:org:bc-ministry-transport> ;
        cga:holonDepth      "2"^^xsd:integer .
}

# Route context: situated in BC geographically but not contained by it holarchically
GRAPH <urn:holon:route:trans-canada-hwy1-bc/context/structural> {
    <urn:holon:route:trans-canada-hwy1-bc>
        cga:memberOf    <urn:holon:network:trans-canada-highway> ;
        cga:memberOf    <urn:holon:network:bc-highway-system> ;
        geo:sfWithin    <urn:holon:geo:canada:british-columbia> .
        # geo:sfWithin (GeoSPARQL spatial relation) is distinct from cga:memberOf
        # — geographic containment without holarchic membership
}

# ── Route boundary: portals to terminus city holons ─────────────────

GRAPH <urn:holon:route:trans-canada-hwy1-bc/boundary> {

    <urn:holon:route:trans-canada-hwy1-bc>
        cga:hasPortal <urn:portal:hwy1-bc:west-terminus> ,
                      <urn:portal:hwy1-bc:east-terminus> .

    <urn:portal:hwy1-bc:west-terminus> a cga:Holon ;
        cga:portalType    cga:RoutePortal ;
        cga:sourceHolon   <urn:holon:route:trans-canada-hwy1-bc> ;
        cga:targetHolon   <urn:holon:geo:canada:bc:vancouver>
        ~ cga:Hwy1VancouverBinding
        {|
            transport:travelTimeTypical   "PT1H45M"^^xsd:duration ;
            transport:distanceKm         "148.0"^^xsd:decimal ;
            dct:created                  "1962-07-30"^^xsd:date ;
            prov:wasAttributedTo         <urn:org:bc-ministry-transport> ;
            cga:confidence               "0.97"^^xsd:decimal
        |} .
}

# ── Vancouver boundary: exposes (but does not own) the route portal ─

GRAPH <urn:holon:geo:canada:bc:vancouver/boundary> {
    <urn:holon:geo:canada:bc:vancouver>
        cga:exposesPortal <urn:portal:hwy1-bc:west-terminus> .
        # cga:exposesPortal — reference, not ownership
        # The route's boundary layer owns and defines this portal
}
```

---

## 11. Discrepancy Log: Glossary vs. Foundational Concepts {#discrepancy-log}

The following table records the significant discrepancies identified between
the [Glossary](Glossary.md) (v0.2) and the [CGA Foundational Concepts](CGA%20Foundational%20Concepts.md)
(v0.1), the resolution adopted in this specification, and the open questions
that require CG deliberation.

| # | Term | Glossary definition | Foundational Concepts definition | Discrepancy | Resolution in this spec | Open |
|---|---|---|---|---|---|---|
| D1 | **Context Graph** | "Auditable, append-only event log representing the state of coherence at one or more system boundaries" | "Named graph that carries interpretation context — framing information needed to resolve the meaning of terms, policies, and state values at a specific decision point" | These are distinct concepts: event log vs. epistemic framing | This spec adopts Foundational Concepts: epistemic framing. The event log is the *temporal stratum* of the context layer | CG should formally resolve which definition is canonical |
| D2 | **Signal** | Foundational concept with detailed ingestion pipeline (authentication → well-formedness → validation) | Listed as "synonym of observation" — raw unverified input | Foundational Concepts treats Signal as pre-trust; Glossary treats Signal as having already passed some pipeline gates | This spec uses Signal in the Foundational Concepts sense (raw/unverified) and uses *Validated Signal* for post-trust signals | Terminology requires harmonisation |
| D3 | **cga: namespace** | Not stated | `https://w3c.github.io/context-graph/ns#` | No canonical namespace IRI established | This spec proposes `https://w3c-context-graph-community-group.github.io/ns/cga#` reflecting the CG's GitHub organisation | CG resolution required before any dependent spec is published |
| D4 | **Four Facets Model** (Data, Meaning, Structure, Context) | Defined as foundational model | Not referenced | Parallel framework to T/A/S/X-Box without explicit mapping | No mapping currently; the two frameworks address different concerns (Facets = what kind of information; Boxes = where it lives architecturally) | CG should document or reconcile the two frameworks |
| D5 | **Portal** | PLACEHOLDER — "reified triple in parent holon's interior graph" (stub only) | Fully defined in §5.3 as a first-class boundary membrane object | Glossary is incomplete | This spec follows Foundational Concepts portal model, extended with portal types and frame transformation | Glossary PLACEHOLDER should be completed |
| D6 | **Holon definition** | Four properties: persistent IRI, shapes graph, exterior graph, controller DID | Looser: "may be represented as named graphs and interact through reifications" | Foundational Concepts is more architecturally precise | This spec adopts Foundational Concepts four-property definition | Glossary should be updated to align |
| D7 | **SKOS usage** | `skos:broader` path queries used in X-Box examples | `skos:broader+` path queries used throughout for hierarchical queries | Both documents prefer `skos:broader` for hierarchy; no mention of `skos:inScheme` preference | This spec introduces `skos:inScheme` preference for concept scheme membership (see §9.2) | Neither prior document discusses this preference; CG should adopt or reject |
| D8 | **Scene Graph** | Not defined in Glossary | Full section (§5.5) in Foundational Concepts | Missing from Glossary | This spec follows Foundational Concepts §5.5 | Glossary should add a Scene Graph entry |
| D9 | **Ontology, Protocol, or Both** | Extended section in Glossary arguing the context graph is both simultaneously | Not addressed in Foundational Concepts | Glossary has philosophical content not present in Foundational Concepts | This spec does not reproduce the philosophical section; it treats the question as settled by the four-layer model | The Glossary's argument is informative; it could become a design rationale appendix |

---

## 12. The CGA Specification Family {#spec-family}

This document is part of the CGA specification family. It contributes the
holonic architecture specification and depends upon the foundational
concepts established in [CGA Foundational Concepts](CGA%20Foundational%20Concepts.md).

The specification family:

| Shortname | Title | Status | Relation to this doc |
|---|---|---|---|
| `cga-foundation` | CGA Foundational Concepts | Draft CG Report | This doc extends |
| `cga-holon` | **Holonic Context Graph** (this document) | Draft CG Report | — |
| `cga-portal` | Portal Traversal and Lifecycle | Planned | Uses portal model from §6 |
| `cga-trust` | Trust Model and Credentials | Planned | Referenced in §8.4 |
| `cga-event` | Event Sourcing and ContextEvent | Planned | Referenced in §8.3 |
| `cga-vocab` | CGA Core Vocabulary | Planned | Formalises Appendix A |
| `cga-provo` | PROV-O Integration Profile | Planned | Extends §8.5 |
| `cga-shacl` | SHACL 1.2 Membrane Patterns | Planned | Extends §5 |
| `cga-twin` | Digital Twin Integration | Planned | Uses §4.3 coordinate model |

---

## Appendix A: cga: Vocabulary Summary {#vocab-summary}

The following table summarises the `cga:` vocabulary terms introduced
or used in this specification. All IRIs use the provisional namespace
`https://w3c-context-graph-community-group.github.io/ns/cga#`.

### Classes

| Term | Definition |
|---|---|
| `cga:Holon` | A bounded entity with four-layer graph structure, persistent IRI identity, and dual whole/part nature |
| `cga:Portal` | A first-class boundary membrane object governing traversal between two holons |
| `cga:TemporalAnnotation` | An event-derived annotation deposited into a holon's temporal context stratum |
| `cga:ValidatedSignal` | A signal that has passed authentication, well-formedness, and SHACL validation |
| `cga:DatalogRule` | A SHACL Rule operating as a membrane rule, firing on signal patterns to produce temporal annotations |

### Object Properties

| Term | Domain | Range | Definition |
|---|---|---|---|
| `cga:contains` | `cga:Holon` | `cga:Holon` | Holarchic containment (parent perspective) |
| `cga:partOf` | `cga:Holon` | `cga:Holon` | Holarchic containment (child perspective); inverse of `cga:contains` |
| `cga:memberOf` | `cga:Holon` | `cga:Holon` | Holarchic membership, including multi-membership |
| `cga:adjacentTo` | `cga:Holon` | `cga:Holon` | Peer-level adjacency |
| `cga:hasPortal` | `cga:Holon` | `cga:Portal` | Portal ownership — the source holon defines this portal |
| `cga:exposesPortal` | `cga:Holon` | `cga:Portal` | Portal reference — the holon acknowledges but does not own this portal |
| `cga:sourceHolon` | `cga:Portal` | `cga:Holon` | The holon whose boundary layer owns this portal |
| `cga:targetHolon` | `cga:Portal` | `cga:Holon` | The holon to which traversal resolves |
| `cga:activationEvent` | `cga:Portal` | `skos:Concept` | Interaction type triggering traversal |
| `cga:iconImage` | `cga:Portal` | `schema:ImageObject` | Visual representation of an icon portal |
| `cga:iconPosition` | `cga:Portal` | `tensor:Value` | Coordinate position for icon rendering |
| `cga:bindsTo` | `cga:Holon` | `rdfs:Resource` | Directional binding to external authority IRI |
| `cga:projectsFrom` | `cga:Holon` | `rdfs:Resource` | Holon is a local projection of external resource |
| `cga:hasTemporalAnnotation` | `cga:Holon` | `cga:TemporalAnnotation` | Link to temporal stratum annotation |
| `cga:annotates` | `cga:TemporalAnnotation` | `cga:Holon` | The holon this annotation qualifies |
| `cga:effectOn` | `cga:TemporalAnnotation` | `rdf:Property` | The property being updated by this annotation |
| `cga:derivedByRule` | `cga:TemporalAnnotation` | `sh:SPARQLRule` | The SHACL rule that produced this annotation |

### Datatype Properties

| Term | Domain | Definition |
|---|---|---|
| `cga:holonDepth` | `cga:Holon` | `xsd:integer` — position in holarchy; 0 = root |
| `cga:beliefConfidence` | (reifier) | `xsd:decimal` [0.0–1.0] — confidence of an asserted belief |
| `cga:observationWeight` | (reifier) | `xsd:decimal` — Bayesian weight of observation vs. prior |
| `cga:priorWeight` | (reifier) | `xsd:decimal` — Bayesian weight of prior |
| `cga:isTraversable` | `cga:Portal` | `xsd:boolean` — false for sealed portals |
| `cga:isConcealed` | `cga:Portal` | `xsd:boolean` — true for portals requiring discovery |
| `cga:schemaVersion` | (reifier) | `xsd:string` — shapes graph version at time of assertion |
| `cga:bindingStrength` | (reifier) | `skos:Concept` from `cga:BindingStrengthScheme` |

### SKOS Concept Schemes (X-Box)

| Scheme | Concepts |
|---|---|
| `cga:PortalTypeScheme` | `cga:UnidirectionalPortal`, `cga:BidirectionalPortal`, `cga:SealedPortal`, `cga:IconPortal`, `cga:RoutePortal` |
| `cga:ActivationEventScheme` | `cga:ClickActivation`, `cga:AutoActivation`, `cga:CredentialActivation`, `cga:SelectActivation` |
| `cga:BindingStrengthScheme` | `cga:StrongBinding`, `cga:WeakBinding`, `cga:ProvisionalBinding` |
| `cga:TrustLevelScheme` | `cga:Unverified`, `cga:DeviceAttested`, `cga:CredentialTrusted`, `cga:CryptoVerified`, `cga:RuleDerived` |
| `cga:HolonLifecycleScheme` | `cga:Created`, `cga:Active`, `cga:Suspended`, `cga:Archived`, `cga:Tombstoned` |

---

## Appendix B: References {#references}

### B.1 Normative References

**[RDF12-CONCEPTS]** Cyganiak, R., Wood, D., Lanthaler, M.
*RDF 1.2 Concepts and Abstract Syntax.* W3C.
https://www.w3.org/TR/rdf12-concepts/

**[RDF12-TURTLE]** Prud'hommeaux, E., Carothers, G., et al.
*RDF 1.2 Turtle.* W3C.
https://www.w3.org/TR/rdf12-turtle/

**[SHACL12-CORE]** Knublauch, H., et al.
*Shapes Constraint Language (SHACL) 1.2 Core.* W3C Working Draft 2026-03-19.
https://www.w3.org/TR/2026/WD-shacl12-core-20260319/

**[SHACL-AF]** Knublauch, H.
*SHACL Advanced Features.* W3C Working Group Note.
https://www.w3.org/TR/shacl-af/

**[SPARQL11-QUERY]** Harris, S., Seaborne, A.
*SPARQL 1.1 Query Language.* W3C Recommendation.
https://www.w3.org/TR/sparql11-query/

**[SKOS-REFERENCE]** Miles, A., Bechhofer, S.
*SKOS Simple Knowledge Organization System Reference.* W3C Recommendation.
https://www.w3.org/TR/skos-reference/

**[PROV-O]** Lebo, T., Sahoo, S., McGuinness, D. et al.
*PROV-O: The PROV Ontology.* W3C Recommendation.
https://www.w3.org/TR/prov-o/

**[OWL2-OVERVIEW]** Hitzler, P., et al.
*OWL 2 Web Ontology Language: Document Overview.* W3C Recommendation.
https://www.w3.org/TR/owl2-overview/

**[GEOSPARQL]** Car, N., et al.
*OGC GeoSPARQL 1.1.* OGC Standard.
https://www.ogc.org/standard/geosparql/

**[OWL-TIME]** Cox, S., Little, C.
*Time Ontology in OWL.* W3C Recommendation.
https://www.w3.org/TR/owl-time/

**[DID-CORE]** Sporny, M., et al.
*Decentralized Identifiers (DIDs) v1.0.* W3C Recommendation.
https://www.w3.org/TR/did-core/

**[VC-DATA-MODEL]** Sporny, M., et al.
*Verifiable Credentials Data Model v2.0.* W3C Recommendation.
https://www.w3.org/TR/vc-data-model-2.0/

### B.2 Informative References

**[CGA-FOUNDATION]** Context Graph Community Group.
*Context Graph Architecture: Foundational Specification.* Draft CG Report.
https://github.com/W3C-Context-Graph-Community-Group/Semantic-Alignment/blob/main/CGA%20Foundational%20Concepts.md

**[CGA-GLOSSARY]** Context Graph Community Group.
*Context Graph Specification Glossary.* Working Draft v0.2.
https://github.com/W3C-Context-Graph-Community-Group/Semantic-Alignment/blob/main/Glossary.md

**[KOESTLER-1967]** Koestler, A.
*The Ghost in the Machine.* Hutchinson, 1967.

**[KOESTLER-1978]** Koestler, A.
*Janus: A Summing Up.* Hutchinson, 1978.

**[FRISTON-2010]** Friston, K.
*The free-energy principle: a unified brain theory?*
Nature Reviews Neuroscience 11, 127–138 (2010).
https://doi.org/10.1038/nrn2787

**[RDF-PATCH]** Seaborne, A.
*RDF Patch — Describing Changes to an RDF Dataset.* W3C Community Draft.
https://afs.github.io/rdf-patch/

---

*Last updated: 2026-03-20. Maintained by the W3C Context Graph Community Group.*
*Repository: https://github.com/W3C-Context-Graph-Community-Group/Semantic-Alignment*
