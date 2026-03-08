# ContextEvent SHACL 1.2 Schema: Design Reference

> **Status:** Working Draft — v0.1  
> **Group:** W3C Context Graph Community Group  
> **Related files:** `context-event-shapes.ttl`, `context-event-examples.ttl`, `ContextEvent-Examples-Guide.md`  
> **Feedback:** Submit as GitHub issues against the [Semantic-Alignment repository](https://github.com/W3C-Context-Graph-Community-Group/Semantic-Alignment)

---

## Purpose of This Document

`context-event-shapes.ttl` defines the SHACL 1.2 shapes, constraints, and inference rules governing `cg:ContextEvent` — the primary unit of the Context Graph's append-only event log. This document explains the schema's architecture section by section, motivates the design decisions embedded in it, and identifies where the group still needs to reach consensus before the schema can be considered normative.

It is written for readers who are familiar with RDF and SHACL 1.1 and are encountering SHACL 1.2 features — in particular `sh:reifierShape`, `sh:if`/`sh:then`/`sh:else`, `sh:rootClass`, and `sh:SPARQLRule` — for the first time in the context of this specification.

---

## Conceptual Foundation: What Is a ContextEvent?

Before examining the shapes, it is worth being precise about what they are governing. A `cg:ContextEvent` is **not an activity** — it is the **durable, immutable record** produced after an activity has already occurred and been validated. By the time an instance appears in the context graph, three things have already happened in the control plane:

1. A Signal was received at a holon boundary.
2. The Signal was authenticated and validated.
3. The resulting state change was committed as a ground assertion to the graph.

The `ContextEvent` records all of this: what changed (the Four Facets), who caused it (provenance and authentication), at which boundary it crossed (boundary metadata), and what triple was actually asserted into the graph (the annotated ground assertion). The schema exists to ensure that this record is complete, consistent, and traceable.

This framing has one important implication for the schema's design: **it can be strict**. Because events are only written after the control plane has already accepted them, every event in the graph should in principle be validatable. Conformance failures indicate either a control-plane defect (an event that should have been rejected was not) or a data hygiene problem (an event was loaded from an external source that did not enforce the schema). Both are situations the schema should detect and report clearly.

---

## SHACL 1.2 Features Used

The schema uses five SHACL 1.2 features that are not available in SHACL 1.1. Understanding each one before reading the schema sections makes the structure considerably clearer.

### `sh:reifierShape`

In RDF 1.2, any triple can be **annotated** by attaching additional triples to an implicit *reifier node* that represents the statement itself:

```turtle
ex:Subject  ex:predicate  ex:Object
    {| cg:producedByEvent ex:Event001 ;
       cg:assertedAt      "2026-03-08T09:14:22Z"^^xsd:dateTimeStamp |} .
```

The `{| |}` block creates a reifier node. In SHACL 1.1 there was no mechanism to apply a shape to that node. SHACL 1.2 adds `sh:reifierShape`, which attaches a `sh:NodeShape` to the reifier node created by a specific property. This schema uses it in Section 3 to enforce that every annotated ground assertion carries the required event provenance metadata.

### `sh:if` / `sh:then` / `sh:else`

SHACL 1.2 adds conditional validation: a shape can test whether a node satisfies one shape (`sh:if`) and, depending on the result, apply either a second shape (`sh:then`) or a third (`sh:else`). This allows the base `ContextEvent` shape to remain clean and compact while adding subtype-specific requirements through separate conditional shapes. Section 4 uses this for spatial, policy, and authentication event types.

### `sh:rootClass`

In SHACL 1.1, if you declared a class as a `sh:NodeShape`, instances of its subclasses were not automatically targeted unless you explicitly declared `sh:targetClass` on a separate shape. `sh:rootClass` closes this gap: declaring `sh:rootClass cg:ContextEvent` on the root class means `cgsh:ContextEventShape` targets all instances of `cg:ContextEvent` and every subclass in its hierarchy, without needing one `sh:targetClass` declaration per subclass.

### `sh:SPARQLRule`

SHACL rules (introduced in the SHACL-AF extension, now part of SHACL 1.2) allow shapes to **derive new triples** via SPARQL CONSTRUCT queries. Unlike `sh:sparql` constraints, which only report violations, `sh:SPARQLRule` shapes populate the graph with inferred data. Section 7 uses two rules chained via `sh:order` to derive `cg:facetCompleteness` and `cg:eventStatus` from the structure of each event.

### `sh:NodeExpression`

Used within the SPARQL rule bodies for inline graph pattern access. In practice the rule bodies in this schema are standard SPARQL CONSTRUCT with `BIND`/`IF`/`EXISTS` expressions; the `sh:NodeExpression` mechanism is implied by the way those expressions are embedded in the shape graph rather than in a separate SPARQL endpoint.

---

## Schema Architecture: Eight Sections

The schema is organised into eight sections. The table below gives the section map; each section is then discussed in detail.

| Section | Content | SHACL feature |
|---------|---------|---------------|
| §0 | Shared prefix declarations | `sh:declare` |
| §1 | `cg:ContextEvent` type hierarchy | `sh:rootClass`, `owl:Class` |
| §2 | Core `cgsh:ContextEventShape` | `sh:targetClass`, `sh:property`, `sh:sparql` |
| §3 | Reifier shape for ground assertions | `sh:reifierShape` |
| §4 | Subtype conditionals | `sh:if`/`sh:then`/`sh:else` |
| §5 | Four Facet shapes | `sh:node` |
| §6 | Auxiliary component shapes | `sh:node`, `sh:sparql` |
| §7 | SPARQL inference rules | `sh:SPARQLRule`, `sh:order` |
| §8 | Non-normative inline example | (commented out) |

---

## Section 0: Shared Prefix Declarations

```turtle
cgsh:CommonPrefixes sh:declare
    [ sh:prefix "prov" ; sh:namespace "http://www.w3.org/ns/prov#"^^xsd:anyURI ],
    [ sh:prefix "cg"   ; sh:namespace "..."^^xsd:anyURI ] .
```

All SPARQL constraint bodies and rule constructs share a single `sh:declare` block on `cgsh:CommonPrefixes`. This is then referenced in each `sh:sparql` and `sh:SPARQLRule` via `sh:prefixes cgsh:CommonPrefixes`, ensuring that prefix expansions are consistent across every SPARQL body in the schema and that adding a new prefix in one place propagates everywhere it is needed.

This is a maintenance decision as much as a technical one: SPARQL bodies embedded in SHACL shapes are the part of the schema most likely to break silently if a prefix changes, because prefix errors in embedded SPARQL appear as runtime failures rather than parse errors. Centralising all declarations in one named resource makes that surface area manageable.

---

## Section 1: The Type Hierarchy and `sh:rootClass`

```turtle
cg:ContextEvent  a owl:Class, sh:NodeShape ;
    sh:rootClass cg:ContextEvent .

cg:DataEvent      rdfs:subClassOf cg:ContextEvent .
cg:RelationEvent  rdfs:subClassOf cg:ContextEvent .
cg:PolicyEvent    rdfs:subClassOf cg:ContextEvent .
cg:AuthEvent      rdfs:subClassOf cg:ContextEvent .
cg:SpatialEvent   rdfs:subClassOf cg:ContextEvent .
cg:BoundaryEvent  rdfs:subClassOf cg:ContextEvent .
```

`cg:ContextEvent` is declared both as an `owl:Class` (enabling OWL reasoning over the hierarchy) and as a `sh:NodeShape` (enabling SHACL to use `sh:rootClass`). The `sh:rootClass` declaration on the root means that `cgsh:ContextEventShape` in Section 2, which has `sh:targetClass cg:ContextEvent`, applies transitively to all six subclasses. Without `sh:rootClass`, you would need to add `sh:targetClass cg:DataEvent`, `sh:targetClass cg:RelationEvent`, and so on to `cgsh:ContextEventShape` — six additional declarations that would need to be maintained in sync with the class hierarchy.

The six subclasses specialise by the **nature of the state change** recorded, not by the domain of the subject matter. A temperature reading and a location update are both `cg:DataEvent` instances; a read-access rule change and a schema update are both `cg:PolicyEvent` instances. The type hierarchy is an event-type classification, not a domain ontology.

> **\[OPEN\]** The six subclasses are illustrative. The group should produce the full event type classification as a `skos:ConceptScheme` alongside this schema, and decide whether additional subclasses (e.g., `cg:DeletionEvent`, `cg:MergeEvent`) are needed.

---

## Section 2: The Core `ContextEventShape`

This is the primary shape. It is large but internally organised into six property clusters, plus two SPARQL constraints and two rule references.

### Identity

```turtle
sh:property [
    sh:path    cg:localId ;
    sh:datatype xsd:string ;
    sh:minCount 1 ; sh:maxCount 1 ;
    sh:pattern  "^[a-zA-Z0-9\\-_\\.]{1,128}$" ;
] ;
```

Every event has a stable IRI as its node identity and a `cg:localId` string for display and logging. The pattern `[a-zA-Z0-9\-_\.]{1,128}` excludes characters that are problematic in log systems, URL paths, and most key-value stores. The 128-character maximum allows for descriptive identifiers like `"evt-2026-03-08-temperature-reading-unit7"` while preventing unbounded strings.

The schema does **not** enforce global uniqueness of `cg:localId` via SHACL — that would require a SPARQL constraint that queries across the entire graph, which is expensive and violates the per-node design of SHACL. Uniqueness within a named graph is expected to be enforced by the control plane at ingestion time.

### Temporal metadata

Three properties record time, with different roles:

- `prov:generatedAtTime` — **mandatory**, system-generated, records when the event was committed to the graph. This is the authoritative timestamp for ordering and audit purposes. It is typed `xsd:dateTimeStamp` (not `xsd:dateTime`) because `dateTimeStamp` requires an explicit timezone offset, preventing the ambiguity of floating timestamps.
- `prov:startedAtTime` — **optional**, actor-claimed, records when the underlying real-world activity began.
- `prov:endedAtTime` — **optional**, actor-claimed, records when the underlying activity concluded.

The distinction between `generatedAtTime` (system commit) and `startedAtTime`/`endedAtTime` (activity window) matters for events with latency — a field sensor that buffers readings and uploads in batch will have `startedAtTime` hours before `generatedAtTime`.

The SPARQL constraint on temporal ordering:

```sparql
SELECT $this
WHERE {
    $this prov:startedAtTime ?start .
    $this prov:endedAtTime   ?end .
    FILTER( ?end < ?start )
}
```

...fires only when **both** optional times are present. A single-timestamp event (only `generatedAtTime`) is not checked. This is correct: the constraint is about the consistency of the claimed activity window, not about the relationship between the claimed window and the system commit time.

### Provenance

`cg:initiatedBy` links to the `prov:Agent` that submitted the triggering Signal. This is deliberately distinct from `cg:credentialHolder` in the authentication context. The initiating agent is the semantic actor (a service, a person, a device) whose action caused the state change. The credential holder is the authenticated identity under which the session was opened — in most cases they are the same, but in delegation scenarios (a human authorising an automated agent) they can differ. The schema allows this by keeping the two as separate properties with separate shapes.

`cg:eventClassification` links to a `skos:Concept` from the event type classification scheme. The `sh:if`/`sh:then`/`sh:else` conditionals in Section 4 do **not** branch on this property directly — they branch on `rdf:type`. The classification concept is a richer, human-readable tag for querying and filtering; the `rdf:type` is the structural hook for conditional shapes.

### Ground assertion link

`cg:producedAssertion` is `sh:minCount 1` with no `sh:maxCount`. An event may produce more than one ground assertion (see C-06 in the examples guide). The shape enforces that at least one assertion is produced, but does not restrict how many.

> **\[OPEN\]** Should `cg:producedAssertion` have an upper bound? An event producing many assertions may indicate a poorly-scoped event boundary. The group should decide whether a `sh:maxCount` is warranted and, if so, what it should be.

> **\[OPEN\]** Should `cg:AuthEvent` be exempt from `sh:minCount 1`? An auth event where a credential is rejected may not produce a durable assertion — it produces only a validation report. The current schema requires one, which means implementations must create a placeholder session record even for rejected credentials.

### Event trail

`cg:precedingEvent` links to the previous event in the same session, forming a traversable event trail. It is `sh:maxCount`-unconstrained (multiple preceding events would represent a merge point in the event trail) but typed `sh:class cg:ContextEvent` to prevent links to non-event resources.

The ordering constraint — that a preceding event must have an earlier `prov:generatedAtTime` — is not enforced by the current schema. Adding it would require a SPARQL constraint that follows the `cg:precedingEvent` link and compares timestamps, which is straightforward but creates a recursive traversal risk in long chains. The group should decide whether this constraint belongs in the schema or in the control plane.

### Authentication context and boundary reference

Both `cg:authenticationContext` and `cg:atBoundary` are `sh:minCount 1 / sh:maxCount 1` — exactly one of each per event. Neither is a string or a literal: both are structured nodes validated by their own shapes (`cgsh:AuthenticationContextShape` in §6a, `cgsh:BoundaryMetadataShape` in §6b). This means authentication and boundary data are always first-class structured resources, never flat annotations.

### Four Facets

All four facets are `sh:minCount 1 / sh:maxCount 1`, delegating to separate shapes in Section 5. The strict cardinality (`sh:maxCount 1` as well as `sh:minCount 1`) prevents an event from carrying two competing data facets or two conflicting context facets, which could make the event's meaning indeterminate. If multiple interpretations need to be recorded, they should be expressed within a single facet node using the appropriate sub-properties.

### Derived properties and rule references

`cg:eventStatus` and `cg:facetCompleteness` are declared on the shape with their valid value ranges, but they carry no `sh:minCount` — they are **not required in input data**. They are derived by the SPARQL rules in Section 7. The `sh:in` constraint on `cg:eventStatus` ensures that if a value is present (whether derived or, in error, directly asserted), it must be one of the three defined statuses. NC-06 in the examples tests what happens when an invalid status is directly asserted.

The rules are attached to `cgsh:ContextEventShape` via:

```turtle
sh:rule cgsh:EventStatusRule, cgsh:FacetCompletenessRule .
```

Attaching the rules to the shape rather than to the class ensures they are only applied when the shape's validator is run. A system that loads the schema but runs only constraint validation (not rules) will not generate status triples, which is the correct behaviour for a read-only audit pass.

---

## Section 3: The Reifier Shape

This section introduces the schema's most architecturally novel feature. The design problem it addresses is: how do you enforce that every triple committed to the graph by a ContextEvent carries a provenance annotation?

In RDF 1.1, this would require named graphs or reification via blank nodes — both of which have well-known ergonomic and query-complexity problems. RDF 1.2 introduces a first-class annotation mechanism, and SHACL 1.2's `sh:reifierShape` provides the corresponding validation hook.

### Structure

```turtle
cgsh:AssertionPropertyShape a sh:PropertyShape ;
    sh:path         cg:producedAssertion ;
    sh:reifierShape cgsh:AssertionAnnotationShape .
```

`cgsh:AssertionPropertyShape` is a property shape on the path `cg:producedAssertion`. The `sh:reifierShape` declaration on it means: *for any triple whose predicate is `cg:producedAssertion` on a ContextEvent node, apply `cgsh:AssertionAnnotationShape` to the reifier node created for that triple's annotation*.

The reifier shape itself enforces four things on every annotation node:

- `cg:producedByEvent` — the event that produced this assertion (mandatory, exactly one, typed `cg:ContextEvent`). This creates the back-link from the annotated triple to its producing event, enabling graph traversal in both directions.
- `cg:assertedAt` — the timestamp of the assertion commit (mandatory, `xsd:dateTimeStamp`).
- `cg:assertionAuthority` — the `prov:Agent` under whose authority the assertion was made (mandatory, exactly one).
- `cg:assertionGraph` — the named graph IRI into which the assertion was committed (optional, at most one).
- `cg:coherenceContribution` — the coherence score for this specific assertion, in `[0.0, 1.0]` (optional). This is distinct from `cg:measuredCoherence` on the context facet, which is the event-level coherence aggregate.

### Why `sh:reifierShape` rather than a separate shape on the annotation node?

The reifier node created by `{| |}` syntax is anonymous (unless a named reifier IRI is used). In SHACL 1.1, you could not apply a `sh:targetNode` or `sh:targetClass` shape to an anonymous node that has no stable identifier. `sh:reifierShape` bypasses this by applying the shape at the point of the annotated property rather than at the node itself — the validator finds the reifier through the structural relationship, not through any identifier on the node.

This also means the shape is applied **regardless of whether the reifier is anonymous or named**. Both `{| |}` inline reifiers and `~ex:ReifierIRI` named reifiers are validated by `cgsh:AssertionAnnotationShape`, because both are reifiers on the same property path.

> **\[OPEN\]** Engine support for `sh:reifierShape` is thin as of March 2026. Apache Jena SHACL and TopBraid Composer have partial support; pyshacl does not yet implement it. The group should maintain an engine compatibility matrix and track this as a blocking issue for normative publication.

---

## Section 4: Conditional Shapes

SHACL 1.2's `sh:if`/`sh:then`/`sh:else` allows the schema to express a single branching rule as a single shape rather than as multiple overlapping property constraints scattered across the document.

All three conditionals in this section follow the same pattern:

```turtle
cgsh:SomethingConditional  a sh:NodeShape ;
    sh:targetClass cg:ContextEvent ;
    sh:if  [ sh:class cg:SubtypeX ] ;
    sh:then cgsh:RequiredForSubtypeX ;
    sh:else cgsh:AbsentForNonSubtypeX .  # (where applicable)
```

The `sh:targetClass cg:ContextEvent` means the conditional is evaluated against **every** ContextEvent instance, not only those of the relevant subtype. For each event, the validator runs the `sh:if` test; if it passes, `sh:then` fires; if it fails, `sh:else` fires (where present).

### 4a. SpatialEvent conditional — the `sh:else` branch matters

The spatial conditional is the only one in the schema with both `sh:then` and `sh:else` branches:

- **`sh:then`** (`cgsh:SpatialContextRequiredShape`): a `cg:SpatialEvent` must have `cg:spatialContext sh:minCount 1`.
- **`sh:else`** (`cgsh:SpatialContextAbsentShape`): a non-`cg:SpatialEvent` must have `cg:spatialContext sh:maxCount 0`.

The `sh:else` branch is not a cosmetic choice — it enforces **data hygiene**. Without it, a `cg:DataEvent` that happened to have coordinates attached would pass validation silently, creating an ambiguous record: is this event spatial or not? The `sh:else` branch forces the implementer to make an explicit typing decision. If spatial data is relevant, the event must be typed as `cg:SpatialEvent`. There is no middle ground.

The policy and auth conditionals omit `sh:else` because there is no corresponding property that must be absent from non-policy and non-auth events — `cg:modifiedPolicy` and `cg:credentialLifecycleStatus` are simply irrelevant (and harmless if absent) on other event types.

### 4b. PolicyEvent conditional

`cgsh:PolicyChangeRequiredShape` requires `cg:modifiedPolicy` (mandatory, IRI) and permits `cg:priorPolicyVersion` (optional, IRI). The prior version link is architecturally important: without it, a policy event is a record of a change but not a record of what was changed from. The optional character of `cg:priorPolicyVersion` accommodates initial policy creation events, where there is no prior version.

### 4c. AuthEvent conditional

`cgsh:AuthEventRequiredShape` requires `cg:credentialLifecycleStatus` drawn from a four-value `sh:in` list: `cg:CredentialSubmitted`, `cg:CredentialRenewed`, `cg:CredentialExpired`, `cg:CredentialRevoked`. These represent the full lifecycle of a credential session: it is submitted at open, renewed when extended, and either expires naturally or is revoked by a governance action.

The `sh:in` list is deliberately closed. Adding a new lifecycle status requires a schema revision and a group decision, not a local extension. This is intentional: credential lifecycle states have security implications and should not be extended unilaterally.

---

## Section 5: The Four Facet Shapes

The Four Facets Model is the central epistemic framework of the Context Graph. Every ContextEvent must carry exactly one node for each facet. The facet shapes are intentionally **open** (no `sh:closed true`) to allow domain-specific extension without schema violations.

### Data Facet (`cgsh:DataFacetShape`)

The raw informational payload. Required: at least one `cg:rawContent` value. Optional: `dcterms:format` (an IANA media type IRI) and `cg:contentHash` (a hex digest string for integrity verification).

The format IRI convention follows `dcterms:format` semantics and points to IANA media type entries (`https://www.iana.org/assignments/media-types/...`). Using an IRI rather than a string literal makes the format machine-actionable: a query can filter events by format without string matching.

`cg:rawContent` has no datatype constraint — it can be a string, a numeric literal, or a language-tagged string. The schema does not prescribe how the raw content is serialised; that is a domain decision. What it does enforce is that the raw content is present: a ContextEvent with no data payload has an incomplete record.

### Meaning Facet (`cgsh:MeaningFacetShape`)

The semantic interpretation of the data payload. Required: `cg:interpretedUnder` (an ontology or vocabulary IRI, at least one) and `rdfs:label` (a plain string, exactly one). Optional: `cg:primaryConcept` (the primary class from the vocabulary, at most one).

The `rdfs:label` requirement is noteworthy. It enforces that every event carries a human-readable description of what it means in context. This is not a documentation nicety — it is an audit requirement. An event log that cannot be read by a human reviewer without dereferencing multiple IRIs is not a functional audit log.

`cg:interpretedUnder` allows multiple values. An event may be interpretable under both a local domain ontology and a standard vocabulary (e.g., both `ex:SensorOntology` and `<https://schema.org/>`), and both can be declared.

### Structure Facet (`cgsh:StructureFacetShape`)

The constraint context: which shapes the event content was validated against. Required: at least one `cg:conformsToShape` IRI. Optional: a `cg:constraintEvaluationRecord` node for recording specific constraint outcomes.

The constraint evaluation record (shaped by `cgsh:ConstraintEvaluationShape`) carries `cg:evaluatedConstraint` (the shape IRI) and `cg:evaluationResult` from a three-value `sh:in` list: `cg:ResultPassed`, `cg:ResultFailed`, `cg:ResultWarning`. This allows the structure facet to record that a constraint was evaluated and passed — not just that the event conforms, but that a specific validation step was performed. This is important for audit trails where the question is not only "is this event valid?" but "was it validated by the expected shape?"

### Context Facet (`cgsh:ContextFacetShape`)

The boundary conditions and resolution metadata. Required: `cg:measuredCoherence` (a decimal in `[0.0, 1.0]`, exactly one) and `cg:resolutionStatus` (exactly one, from `{cg:Resolved, cg:PartiallyResolved, cg:Unresolved}`). Optional: `cg:attributedIntent` (a structured intent node), `cg:prerequisite` (zero or more IRI references to required contextual conditions).

`cg:measuredCoherence` is the context facet's most architecturally load-bearing property. A value of `1.0` means the event crossed the boundary with complete semantic alignment between source and target holon. A value below `1.0` records the degree of residual ambiguity. The `[0.0, 1.0]` range is enforced by `sh:minInclusive`/`sh:maxInclusive` — NC-09 in the examples tests the upper bound violation.

`cg:resolutionStatus` is a coarse three-state encoding of the coherence outcome. The relationship between `cg:measuredCoherence` (a scalar) and `cg:resolutionStatus` (a categorical) is not normatively defined in the current schema — the group should decide whether the two should be derivationally linked (e.g., coherence above a threshold implies `cg:Resolved`) or whether they remain independent.

---

## Section 6: Auxiliary Component Shapes

These shapes define the structure of sub-graph nodes that appear as property values on ContextEvent instances. Each is a `sh:NodeShape` without its own `sh:targetClass` — they are applied via `sh:node` references from the parent shapes, not via independent targeting.

### Authentication Context (`cgsh:AuthenticationContextShape`)

Five property constraints plus one SPARQL constraint. The properties record credential identity, holder, session window, and permission scope. The SPARQL constraint is the most structurally complex piece in the schema:

```sparql
SELECT $this
WHERE {
    $this cg:sessionValidUntil ?expiry .
    ?event cg:authenticationContext $this ;
           prov:generatedAtTime     ?commitTime .
    FILTER( ?commitTime > ?expiry )
}
```

This constraint validates the **authentication context node** (`$this`), but it must navigate **up** to the parent event to retrieve `prov:generatedAtTime`. This two-hop reverse traversal (`?event cg:authenticationContext $this`) works because SHACL's SPARQL constraint bodies have full access to the graph, not only to the focus node's immediate properties.

The constraint enforces that a credential session had not expired at the time the event was committed to the graph. An event committed after the session expired is suspicious: it either means the control plane failed to check expiry, or the event was replayed from a stale buffer. Both are conditions an auditor should be alerted to.

> **\[OPEN\]** Should the session expiry constraint be normative or advisory (a `sh:Violation` vs a `sh:Warning`)? A strict violation would reject backfilled events from systems with clock drift. The group should decide the appropriate severity.

> **\[OPEN\]** Normative alignment with VC-DATA-MODEL 2.0 is pending. The current `cgsh:AuthenticationContextShape` captures the same information as a W3C Verifiable Credential (holder, issuer, validity window, scope) but does not use VC vocabulary. A companion mapping note should be produced.

### Boundary Metadata (`cgsh:BoundaryMetadataShape`)

Identifies the holon boundary at which the event occurred. Required: `cg:boundaryId` (an IRI, exactly one) and `cg:sourceHolon` (an IRI, exactly one). Optional: `cg:targetHolon` (at most one) and `cg:boundaryCoherenceScore` (a decimal in `[0.0, 1.0]`, at most one).

`cg:targetHolon` is optional because some events originate within a single holon rather than crossing a boundary. The distinction between source and target holons is meaningful only for cross-boundary events; for intra-holon events only `cg:sourceHolon` is required.

> **\[OPEN\]** The group has not yet resolved whether a holon boundary is a named graph, a trust domain, an administrative scope, or a composite construct. The `cgsh:BoundaryMetadataShape` is deliberately minimal to avoid prejudging that decision. Once resolved, additional required properties will likely be added.

### Spatial Context (`cgsh:SpatialContextShape`)

Applied only to `cg:SpatialEvent` instances via the Section 4 conditional. Requires `geo:lat` and `geo:long` with full WGS84 range validation (`[-90, 90]` and `[-180, 180]` respectively). Optional: `cg:spatialPrecisionMetres` (the measurement uncertainty radius) and `cg:spatialReferenceSystem` (the CRS IRI, defaulting to WGS84 if absent).

The use of the W3C Basic Geo vocabulary (`geo:lat`, `geo:long`) is pragmatic — it is the most widely deployed geospatial RDF vocabulary. The group should consider whether to adopt GeoSPARQL (`geo:hasGeometry`/`geo:asWKT`) for richer spatial support, at the cost of increased complexity in the shape.

### Intent (`cgsh:IntentShape`)

Records the intent attributed to the initiating actor. Required: `cg:intentType` from the three-value list `{cg:ExplicitIntent, cg:InferredIntent, cg:ComposedIntent}`. Optional: `cg:intentDescription` (a string) and `cg:intentEvidence` (zero or more IRIs pointing to evidence sources — prior events, signals, or external references).

The three intent types capture the epistemic status of the attribution:

- `cg:ExplicitIntent` — the actor stated the intent directly (e.g., via a structured request with an explicit purpose field).
- `cg:InferredIntent` — the intent was inferred from the actor's pattern of behaviour, context, or prior events.
- `cg:ComposedIntent` — the intent was derived from a combination of the actor's action and a system-level constraint or policy (the automated migration scenario in C-05 of the examples).

The classification is important for trust scoring and governance: an event with `cg:InferredIntent` carries a higher epistemic risk than one with `cg:ExplicitIntent`, and that risk should be reflected in `cg:measuredCoherence`.

### Resolution Trace (`cgsh:ResolutionTraceShape`)

> **\[PLACEHOLDER\]** This shape is a minimum viable stub. It is not considered normative in the current draft.

The resolution trace records the history of how a contextual misalignment at a boundary was identified, measured, and either resolved or left unresolved over the lifetime of a context graph instance. It requires a `cg:traceId` (IRI), at least one `cg:traceEvents` (ContextEvent IRI), and a `cg:traceOutcome` from `{cg:TraceResolved, cg:TracePartial, cg:TraceUnresolved}`.

Full specification of the resolution trace is the highest-priority outstanding work item in the schema. The trace is referenced throughout the Glossary and the examples, but its shape is currently insufficient to support the traversal queries a real implementation would require.

---

## Section 7: SPARQL Inference Rules

The two rules in this section are attached to `cgsh:ContextEventShape` via `sh:rule` and are executed by a SHACL advanced features processor (not by the standard constraint validator). They use `sh:order` to guarantee execution sequence.

### `cgsh:FacetCompletenessRule` (`sh:order 0`)

```sparql
CONSTRUCT { $this cg:facetCompleteness ?score }
WHERE {
    BIND(
        ( IF(EXISTS { $this cg:dataFacet      ?d }, 1, 0)
        + IF(EXISTS { $this cg:meaningFacet   ?m }, 1, 0)
        + IF(EXISTS { $this cg:structureFacet ?s }, 1, 0)
        + IF(EXISTS { $this cg:contextFacet   ?c }, 1, 0)
        ) AS ?score
    )
}
```

Counts how many of the four required facets are present and writes the result (an integer 0–4) to `cg:facetCompleteness`. The rule fires on **all** ContextEvent instances, including non-conformant ones: a missing facet produces score 3 rather than causing the rule to fail. This is intentional — the derived score is an input to the status rule, and the status rule must be able to produce `cg:StatusFailed` for incomplete events.

The use of `EXISTS { ... }` sub-patterns rather than `OPTIONAL { ... }` is deliberate. `OPTIONAL` would require explicit handling of unbound variables in the arithmetic; `EXISTS` returns a boolean cleanly convertible to 0 or 1.

### `cgsh:EventStatusRule` (`sh:order 1`)

```sparql
CONSTRUCT { $this cg:eventStatus ?status }
WHERE {
    $this cg:facetCompleteness ?score .
    OPTIONAL { $this cg:authenticationContext ?auth }
    BIND(
        IF( ?score = 4 && BOUND(?auth), cg:StatusValidated,
        IF( ?score = 4,                  cg:StatusPending,
                                         cg:StatusFailed ))
        AS ?status
    )
}
```

Derives `cg:eventStatus` from `cg:facetCompleteness` (produced by the previous rule) and the presence or absence of `cg:authenticationContext`. The three-state logic is:

- All four facets present **and** authentication context present → `cg:StatusValidated`
- All four facets present **but** no authentication context → `cg:StatusPending`
- Fewer than four facets present → `cg:StatusFailed`

The `StatusPending` state captures events that are structurally complete but awaiting authentication resolution — for example, an event submitted during an authentication broker outage. These events are in the graph but flagged for deferred validation. `StatusFailed` events indicate a control-plane defect and should trigger an alert.

### Why rules rather than direct constraint derivation?

An alternative design would be to validate `cg:eventStatus` directly without deriving it — for example, using a SPARQL constraint that checks whether the status value correctly reflects the facet count. The rule approach is preferred for two reasons:

First, the derived triple is a **queryable, indexable property**. A system querying `SELECT ?evt WHERE { ?evt cg:eventStatus cg:StatusFailed }` can use standard SPARQL without embedding the derivation logic in every query. The rule externalises the derivation once; queries consume it without knowing how it was computed.

Second, the `sh:order` chaining creates a **composable pipeline**. Additional rules could be added at higher order values to derive further properties from `cg:eventStatus` — for example, a rule that derives an alert IRI for `StatusFailed` events — without modifying the existing rules.

---

## Design Decisions Summary

| Decision | Choice made | Alternative considered | Rationale |
|----------|-------------|----------------------|-----------|
| Type hierarchy targeting | `sh:rootClass` on `cg:ContextEvent` | One `sh:targetClass` per subclass | Maintainability; new subclasses inherit automatically |
| Subtype specialisation | `sh:if`/`sh:then`/`sh:else` conditional shapes | Merged property constraints on a single large shape | Separation of concerns; each conditional independently testable |
| Annotation validation | `sh:reifierShape` on `cgsh:AssertionPropertyShape` | Separate named-graph shapes | Direct validation of the RDF 1.2 reifier node; no workaround needed |
| Prefix management | Centralised `cgsh:CommonPrefixes` with `sh:declare` | Inline prefix declarations per SPARQL body | Single maintenance point; prevents silent prefix drift |
| Status derivation | `sh:SPARQLRule` with `sh:order` chaining | `sh:sparql` constraint checking pre-asserted status | Status becomes a queryable, indexable property |
| Facet shapes | Open (`sh:closed false`) | Closed shapes | Domain extension without schema violations |
| Spatial absent constraint | `sh:else sh:maxCount 0` on `cg:spatialContext` | Omitting `sh:else` | Prevents silent data contamination on non-spatial events |
| Temporal precision | `xsd:dateTimeStamp` (not `xsd:dateTime`) | `xsd:dateTime` | Requires explicit timezone; prevents floating timestamp ambiguity |
| Auth/initiator separation | Separate `cg:initiatedBy` and `cg:credentialHolder` | Single identity property | Supports delegation scenarios |
| Intent classification | Closed `sh:in` three-value list | Open string annotation | Security-relevant classification; change should require group decision |
| Credential lifecycle | Closed `sh:in` four-value list | Open lifecycle vocabulary | Same rationale as intent; credential states have security implications |

---

## Open Questions

| # | Question | Section | Impact |
|---|----------|---------|--------|
| 1 | Should the `sh:else` spatial constraint be applied to all non-spatial event types, or only to `cg:DataEvent` and `cg:RelationEvent`? | §4a | Affects `cg:PolicyEvent` and `cg:AuthEvent` which are unlikely to have coordinates but could in theory |
| 2 | Should `cg:AuthEvent` be exempt from `cg:producedAssertion sh:minCount 1`? | §2 | Affects control-plane design for credential rejection events |
| 3 | Should `cg:producedAssertion` have a `sh:maxCount` upper bound? | §2 | Affects event boundary scoping conventions |
| 4 | Should the event trail ordering constraint (preceding event must have earlier `generatedAtTime`) be added to the schema? | §2 | Adds a recursive SPARQL traversal risk in long event chains |
| 5 | Should the session expiry constraint be `sh:Violation` severity or `sh:Warning`? | §6a | Affects handling of clock-drift and backfill scenarios |
| 6 | What is the full definition of a holon boundary? Named graph, trust domain, or composite construct? | §6b | Currently under-specified; blocks normative `cgsh:BoundaryMetadataShape` |
| 7 | Should `cg:measuredCoherence` and `cg:resolutionStatus` be derivationally linked? | §5 | Affects whether the context facet has redundant properties |
| 8 | Should the schema adopt GeoSPARQL in addition to or instead of Basic Geo? | §6c | Affects richness of spatial query support |
| 9 | Should `sh:reifierShape` be flagged as a `sh:Warning`-only constraint until engine support is confirmed? | §3 | Blocks pyshacl-based testing workflows |
| 10 | What is the full specification of `cgsh:ResolutionTraceShape`? | §6e | Currently a stub; required for normative completeness |
| 11 | What is the normative VC-DATA-MODEL 2.0 alignment for `cgsh:AuthenticationContextShape`? | §6a | Affects interoperability with Verifiable Credential infrastructure |

---

## Engine Support Notes

The following table summarises known support status for the SHACL 1.2 features used in this schema as of March 2026. This table should be maintained as the group's reference implementation evolves.

| Feature | Apache Jena SHACL | TopBraid Composer | pyshacl (≥0.26) | Notes |
|---------|-------------------|-------------------|-----------------|-------|
| `sh:rootClass` | Partial | Yes | No | pyshacl targets subclasses if explicitly listed |
| `sh:if`/`sh:then`/`sh:else` | Yes | Yes | Partial | pyshacl `--advanced` flag required |
| `sh:reifierShape` | Partial | Yes | No | Blocking for reifier annotation tests |
| `sh:SPARQLRule` | Yes | Yes | Yes (`--advanced`) | Rules only run with advanced features enabled |
| `sh:declare` / `sh:prefixes` | Yes | Yes | Yes | Supported in all tested engines |

For testing with pyshacl, run with `--advanced` to enable rules and partial SHACL 1.2 support:

```bash
pyshacl \
  --shacl context-event-shapes.ttl \
  --format turtle \
  --advanced \
  context-event-examples.ttl
```

Tests involving `sh:reifierShape` (Section 3) and the reifier-specific non-conformant cases (NC-07) require a SHACL 1.2-conformant engine. The group should establish a reference engine and test suite before marking the schema normative.

---

## Namespace Summary

| Prefix | Namespace | Role |
|--------|-----------|------|
| `cg:` | `https://w3c-context-graph-community-group.github.io/ns/context-graph#` | Context Graph vocabulary and classes |
| `cgsh:` | `https://w3c-context-graph-community-group.github.io/ns/context-graph/shapes#` | SHACL shapes and rules |
| `prov:` | `http://www.w3.org/ns/prov#` | PROV-O provenance vocabulary |
| `skos:` | `http://www.w3.org/2004/02/skos/core#` | SKOS concept scheme |
| `geo:` | `http://www.w3.org/2003/01/geo/wgs84_pos#` | W3C Basic Geo |
| `dcterms:` | `http://purl.org/dc/terms/` | Dublin Core Terms |
| `sh:` | `http://www.w3.org/ns/shacl#` | SHACL vocabulary |
| `owl:` | `http://www.w3.org/2002/07/owl#` | OWL vocabulary |
| `xsd:` | `http://www.w3.org/2001/XMLSchema#` | XSD datatypes |

The `cg:` and `cgsh:` namespaces are distinct by design. The `cg:` namespace contains classes, properties, and named individuals that form the Context Graph **vocabulary** — the terms that appear in instance data. The `cgsh:` namespace contains SHACL shapes and rules that form the **schema** — the terms that appear in the shapes graph. Keeping them separate allows the vocabulary to be loaded without the shapes (for systems that do inference only) and allows the shapes to be loaded without the full vocabulary ontology (for systems that do validation only).

---

*Last updated: 2026-03-08. Maintained by the W3C Context Graph Community Group.*
