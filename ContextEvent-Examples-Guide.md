# ContextEvent Examples: A Guided Walkthrough

> **Status:** Non-normative working document — v0.1  
> **Group:** W3C Context Graph Community Group  
> **Related files:** `context-event-shapes.ttl`, `context-event-examples.ttl`  
> **Feedback:** Submit as GitHub issues against the [Semantic-Alignment repository](https://github.com/W3C-Context-Graph-Community-Group/Semantic-Alignment)

---

## Purpose of This Document

`context-event-examples.ttl` contains sixteen Turtle 1.2 instances designed to illustrate the `ContextEvent` SHACL 1.2 schema defined in `context-event-shapes.ttl`. Six are **conformant** — valid events that demonstrate different event subtypes and annotation patterns. Ten are **non-conformant** — deliberately invalid events, each targeting a specific constraint mechanism in the schema.

This document explains the design choices in both sets of examples, clarifies the two RDF 1.2 annotation syntaxes used, maps each non-conformant case to its violated shape, and flags open questions the group should resolve before the examples are considered stable test vectors.

---

## Background: What Is a ContextEvent?

Before reading the examples, it is worth restating what the schema is modelling. A `cg:ContextEvent` is not an activity — it is the **durable record** of an activity that has already taken place. By the time an event appears in the context graph, the real-world thing it describes has already happened. The record captures:

- **What changed** — via the Four Facets (Data, Meaning, Structure, Context)
- **Who caused it** — via `cg:initiatedBy` and `cg:authenticationContext`
- **Where it happened** — via `cg:atBoundary` (and optionally `cg:spatialContext`)
- **When it happened** — via `prov:generatedAtTime` (commit time) and optionally `prov:startedAtTime`/`prov:endedAtTime` (activity window)
- **What it produced** — via `cg:producedAssertion`, with the produced triple annotated using RDF 1.2 reification syntax

The last point — the annotated assertion — is what makes this an RDF 1.2 document rather than a plain RDF 1.1 one, and it is where the most novel syntax appears.

---

## RDF 1.2 Annotation Syntax: Two Forms

The examples use both annotation notations introduced in RDF 1.2 / Turtle 1.2. Understanding the difference is essential before reading the examples.

### Form A: Inline anonymous annotation `{| ... |}`

```turtle
ex:TargetEntity  cg:temperature  "42.7"
    {| cg:producedByEvent    ex:Event-C01 ;
       cg:assertedAt         "2026-03-08T09:14:22Z"^^xsd:dateTimeStamp ;
       cg:assertionAuthority ex:IngestServiceA ;
       cg:assertionGraph     ex:HolonContextGraph01 |} .
```

The `{| ... |}` block attaches annotations directly to the triple `ex:TargetEntity cg:temperature "42.7"`. An anonymous **reifier node** is created implicitly; you cannot refer to that node from any other triple because it has no IRI. Use this form when the annotation itself needs no further cross-reference — the common case for routine data events.

### Form B: Named reifier `~ex:ReifierIRI`

```turtle
ex:PolicyObject-ReadAccess  cg:policyVersion  "v2"
    ~ex:Reifier-C03 .

ex:Reifier-C03
    cg:producedByEvent    ex:Event-C03 ;
    cg:assertedAt         "2026-03-08T11:30:00Z"^^xsd:dateTimeStamp ;
    cg:assertionAuthority ex:PolicyAdminBot ;
    cg:assertionGraph     ex:HolonContextGraph01 .
```

The `~ex:Reifier-C03` suffix assigns an **explicit IRI** to the reifier node. The annotation properties are then written as ordinary triples about that IRI. Because the reifier has a stable identifier, other resources can reference it directly — for example, a `ResolutionTrace` that needs to record precisely which annotation captured a policy change. Use named reifiers whenever downstream cross-referencing is anticipated.

### Which form to choose

| Situation | Recommended form |
|-----------|-----------------|
| Routine data event, no further cross-reference needed | `{| |}` inline |
| PolicyEvent, AuthEvent, BoundaryEvent — reifier likely to be referenced from a ResolutionTrace | `~IRI` named |
| Event produces multiple assertions that may be independently referenced | `~IRI` named, one per assertion |
| Testing the `cgsh:AssertionAnnotationShape` reifier shape explicitly | Either; named preferred for clarity |

---

## Shared Vocabulary (§1 of the examples file)

All sixteen examples share a common set of stub declarations at the top of the file:

- **Six `prov:Agent` instances** — `ex:IngestServiceA`, `ex:PolicyAdminBot`, `ex:FieldSensorUnit7`, `ex:AuthBroker`, `ex:DataMigrationJob`, `ex:BoundaryController`. Each represents a different actor type: automated service, governance bot, IoT device, auth broker, batch job, and system controller.
- **Four holons** — an external feed, the context graph instance, a knowledge layer, and a field operations scope.
- **Six boundaries** — one per agent pairing, linking source and target holons.
- **Credential stubs** — all active sessions set `cg:sessionValidUntil` to `2026-12-31` so that the commit-time expiry check passes. The single exception is `ex:CredExpired`, used in NC-03, which expires at `08:00:00` on the day of the event.
- **SKOS concept stubs** — one classification concept per event subtype. The full `ConceptScheme` is to be defined separately.

In a production deployment, these stubs would live in separate named graphs and be loaded as background data. In the examples file they are inlined for self-containment.

---

## Conformant Examples (§2)

### C-01: DataEvent — inline annotation, all facets present

**Scenario:** An ingest service records a temperature reading of 42.7 from an external feed.

This is the baseline case and the most detailed example. It demonstrates every mandatory property of `cgsh:ContextEventShape` in its simplest form: all four facets are present, the authentication context has an active session, and the boundary metadata identifies source and target holons. The `cg:constraintEvaluationRecord` inside `cg:structureFacet` shows how validation outcomes can be recorded inline.

The `FacetCompletenessRule` derives `cg:facetCompleteness 4`; the `EventStatusRule` then derives `cg:eventStatus cg:StatusValidated`.

The ground assertion uses the inline `{| |}` form. The anonymous reifier is validated by `cgsh:AssertionAnnotationShape` via the `sh:reifierShape` on `cgsh:AssertionPropertyShape`.

```turtle
ex:Event-C01  a cg:DataEvent ;
    cg:localId               "evt-c01-temperature-reading" ;
    prov:generatedAtTime     "2026-03-08T09:14:22Z"^^xsd:dateTimeStamp ;
    prov:startedAtTime       "2026-03-08T09:14:20Z"^^xsd:dateTimeStamp ;
    prov:endedAtTime         "2026-03-08T09:14:22Z"^^xsd:dateTimeStamp ;
    cg:initiatedBy           ex:IngestServiceA ;
    cg:eventClassification   ex:ClassDataChange ;
    cg:producedAssertion     ex:TargetEntity ;
    cg:authenticationContext [ ... ] ;
    cg:atBoundary            [ ... ] ;
    cg:dataFacet             [ cg:rawContent "42.7" ; ... ] ;
    cg:meaningFacet          [ cg:interpretedUnder ex:SensorOntology ; ... ] ;
    cg:structureFacet        [ cg:conformsToShape cgsh:DataFacetShape ; ... ] ;
    cg:contextFacet          [ cg:measuredCoherence "0.92"^^xsd:decimal ; ... ] .

ex:TargetEntity  cg:temperature  "42.7"
    {| cg:producedByEvent    ex:Event-C01 ;
       cg:assertedAt         "2026-03-08T09:14:22Z"^^xsd:dateTimeStamp ;
       cg:assertionAuthority ex:IngestServiceA ;
       cg:assertionGraph     ex:HolonContextGraph01 ;
       cg:coherenceContribution "0.92"^^xsd:decimal |} .
```

**Key observation:** `prov:startedAtTime` and `prov:endedAtTime` are both present and correctly ordered (`endedAtTime` ≥ `startedAtTime`). NC-02 inverts this to trigger the temporal ordering SPARQL constraint.

---

### C-02: SpatialEvent — `sh:if`/`sh:then` conditional in action

**Scenario:** An IoT field sensor reports a geo-located humidity reading from a known WGS84 coordinate.

The critical feature of this example is the `cg:spatialContext` node, which is **required** by the `cgsh:SpatialEventConditional` shape because the event is typed `cg:SpatialEvent`. The `sh:if` test checks `sh:class cg:SpatialEvent`; the test passes, so `sh:then` fires and requires `cg:spatialContext` to be present with valid `geo:lat` and `geo:long` values.

This example also demonstrates **two ground assertions from a single event** — humidity and latitude are both asserted, each annotated independently with its own `{| |}` block but both pointing back to `ex:Event-C02`.

```turtle
# The event carries a spatialContext node — satisfies the sh:then branch
cg:spatialContext [
    geo:lat                   "51.5074"^^xsd:decimal ;
    geo:long                  "-0.1278"^^xsd:decimal ;
    cg:spatialPrecisionMetres "5.0"^^xsd:decimal ;
    cg:spatialReferenceSystem <http://www.opengis.net/def/crs/EPSG/0/4326> ;
] ;
```

NC-04 removes `cg:spatialContext` from a `SpatialEvent` to trigger the `sh:then` violation. NC-05 places `cg:spatialContext` on a plain `DataEvent` to trigger the `sh:else` violation (the shape enforces `sh:maxCount 0` on `cg:spatialContext` for non-spatial events).

---

### C-03: PolicyEvent — named reifier enabling ResolutionTrace cross-reference

**Scenario:** The policy administration bot updates a read-access constraint, recording the prior version for rollback.

This event uses the named reifier form. The justification is in the ResolutionTrace stub at the bottom of the example:

```turtle
ex:ResolutionTrace-C03
    a cg:ResolutionTrace ;
    cg:traceId           ex:ResolutionTrace-C03 ;
    cg:traceEvents       ex:Event-C03 ;
    cg:traceOutcome      cg:TraceResolved ;
    cg:trackedAnnotation ex:Reifier-C03 .   # ← direct named-reifier reference
```

`cg:trackedAnnotation ex:Reifier-C03` refers to the reifier node by IRI. This is only possible with the named form; an anonymous `{| |}` reifier would leave the ResolutionTrace unable to identify which specific annotation captured the policy change.

The event also exercises `cgsh:PolicyChangeRequiredShape` (the `sh:then` branch for `cg:PolicyEvent`): both `cg:modifiedPolicy` and `cg:priorPolicyVersion` are present. NC-08 removes `cg:modifiedPolicy` to trigger that conditional.

---

### C-04: AuthEvent — credential lifecycle, named reifier

**Scenario:** The authentication broker opens a new session by submitting a credential.

`cg:AuthEvent` is the only subtype where the primary payload _is_ the credential rather than a change to domain data. This event demonstrates:

- `cg:credentialLifecycleStatus cg:CredentialSubmitted` — required by `cgsh:AuthEventRequiredShape`
- The session assertion `ex:Session-C04 cg:sessionStatus cg:CredentialSubmitted` as the `cg:producedAssertion`
- A named reifier `~ex:Reifier-C04` that the session record could cross-reference to confirm the exact annotation that opened it

> **\[OPEN\]** The group should decide whether `cg:AuthEvent` warrants an exemption from `sh:minCount 1` on `cg:producedAssertion`. An auth event where a credential is _rejected_ might not produce a durable assertion — it produces only a `Report`. This is the `sh:then` violation tested in NC-10, but the question of whether it should be a violation at all is unresolved.

---

### C-05: RelationEvent — event trail and composed intent

**Scenario:** A scheduled data migration job asserts a `cg:migratedFrom` relationship between two entities as part of a session that began with C-01.

This example demonstrates two features not shown in C-01 through C-04:

**Event trail:** `cg:precedingEvent ex:Event-C01` links this event to its predecessor in the session, forming the traversable event trail described in the Glossary under *Context Event*.

**Composed intent:** The `cg:attributedIntent` node records that the migration job's intent was composed from two sources — the job's own configuration (`ex:Event-C01` as a prior event in the trail) and an external subsystem constraint (`ex:SubsystemX`). The `cg:intentEvidence` property can hold multiple IRI values, each pointing to an evidence source.

```turtle
cg:contextFacet [
    cg:measuredCoherence  "0.79"^^xsd:decimal ;
    cg:resolutionStatus   cg:PartiallyResolved ;
    cg:attributedIntent [
        cg:intentType        cg:ComposedIntent ;
        cg:intentDescription "Migrate source–target association per job config and subsystem constraint" ;
        cg:intentEvidence    ex:Event-C01 ;
        cg:intentEvidence    ex:SubsystemX ;
    ] ;
] .
```

The coherence score of `0.79` and resolution status of `cg:PartiallyResolved` reflect that migration relationships inherently carry residual uncertainty about semantic equivalence between source and target.

---

### C-06: BoundaryEvent — multiple assertions, two named reifiers

**Scenario:** The holon boundary controller adds a new member to an existing boundary, producing two separate ground assertions.

This is the only conformant example with `sh:minCount 1` but no `sh:maxCount` on `cg:producedAssertion` exercised to its limit: two distinct assertions are produced — a membership assertion and a boundary member-count update — each with its own named reifier.

```turtle
cg:producedAssertion  ex:HolonNewMember ;
cg:producedAssertion  ex:BoundaryIngestFeed ;

# First reifier
ex:HolonNewMember  cg:memberOf  ex:HolonContextGraph01
    ~ex:Reifier-C06a .

# Second reifier
ex:BoundaryIngestFeed  cg:memberCount  "5"^^xsd:integer
    ~ex:Reifier-C06b .
```

Both `ex:Reifier-C06a` and `ex:Reifier-C06b` point back to `ex:Event-C06`, but they annotate different triples. A ResolutionTrace could independently reference either reifier to audit the specific assertion it tracks.

> **\[OPEN\]** The group should decide whether `cg:producedAssertion` should have an upper bound (`sh:maxCount`). An event that produces an unbounded number of assertions may be a sign of a poorly scoped event boundary — but restricting to one assertion per event would break legitimate multi-assertion cases like this one.

---

## Non-Conformant Examples (§3)

Each NC example is designed to trigger exactly one type of constraint mechanism. The table below maps each to its mechanism and the specific shape property that fires.

| ID | Event type | Annotation | Violated mechanism | Shape / property |
|----|-----------|------------|-------------------|-----------------|
| NC-01 | DataEvent | `{| |}` | `sh:minCount` on required property | `cgsh:ContextEventShape` → `cg:contextFacet sh:minCount 1` |
| NC-02 | DataEvent | `{| |}` | Cross-property `sh:sparql` constraint | Temporal ordering: `endedAtTime < startedAtTime` |
| NC-03 | DataEvent | `{| |}` | Graph-traversal `sh:sparql` constraint | Session expiry: commit time after `sessionValidUntil` |
| NC-04 | SpatialEvent | `{| |}` | `sh:if`/`sh:then` conditional | `cgsh:SpatialContextRequiredShape`: `cg:spatialContext sh:minCount 1` |
| NC-05 | DataEvent | `{| |}` | `sh:if`/`sh:else` conditional | `cgsh:SpatialContextAbsentShape`: `cg:spatialContext sh:maxCount 0` |
| NC-06 | DataEvent | `{| |}` | `sh:in` enumeration | `cg:eventStatus sh:in (StatusValidated StatusPending StatusFailed)` |
| NC-07 | DataEvent | `~IRI` | `sh:reifierShape` (SHACL 1.2) | `cgsh:AssertionAnnotationShape`: `cg:producedByEvent sh:minCount 1` |
| NC-08 | PolicyEvent | `{| |}` | `sh:if`/`sh:then` conditional | `cgsh:PolicyChangeRequiredShape`: `cg:modifiedPolicy sh:minCount 1` |
| NC-09 | DataEvent | `{| |}` | `sh:maxInclusive` numeric range | `cgsh:ContextFacetShape`: `cg:measuredCoherence sh:maxInclusive 1.0` |
| NC-10 | AuthEvent | `~IRI` | `sh:if`/`sh:then` conditional | `cgsh:AuthEventRequiredShape`: `cg:credentialLifecycleStatus sh:minCount 1` |

### NC-01: Missing mandatory facet

`cg:contextFacet` is absent. All other facets are present. This is the most direct violation: a simple `sh:minCount 1` is not satisfied. The side effect is that `FacetCompletenessRule` derives `cg:facetCompleteness 3`, and `EventStatusRule` therefore derives `cg:eventStatus cg:StatusFailed` — illustrating how the rule pipeline and the constraint system interact.

### NC-02: Temporal ordering violation

`prov:endedAtTime` is set 30 seconds **before** `prov:startedAtTime`. This cannot be caught by any single-property constraint; it requires the SPARQL constraint on `cgsh:ContextEventShape` that compares the two values directly:

```sparql
SELECT $this
WHERE {
    $this prov:startedAtTime ?start .
    $this prov:endedAtTime   ?end .
    FILTER( ?end < ?start )
}
```

This is a representative example of the class of constraints that `sh:sparql` handles and property-level shapes cannot: cross-property consistency rules.

### NC-03: Expired credential session

`prov:generatedAtTime` is `09:14:22` but `cg:sessionValidUntil` is `08:00:00` on the same day. The SPARQL constraint in `cgsh:AuthenticationContextShape` navigates from the `authenticationContext` node up to its parent event to retrieve the commit timestamp:

```sparql
SELECT $this
WHERE {
    $this cg:sessionValidUntil ?expiry .
    ?event cg:authenticationContext $this ;
           prov:generatedAtTime     ?commitTime .
    FILTER( ?commitTime > ?expiry )
}
```

This is the most structurally complex constraint in the schema — it requires a two-hop path traversal and a cross-node temporal comparison. It demonstrates that `sh:sparql` constraints on sub-shapes (here on the `authenticationContext` blank node rather than the event root) are fully supported.

### NC-04 and NC-05: The conditional pair

These two examples should be read together. They test opposite branches of the same `sh:if`/`sh:then`/`sh:else` structure on `cgsh:SpatialEventConditional`:

- **NC-04** (`SpatialEvent` without `cg:spatialContext`): The `sh:if` test matches (`sh:class cg:SpatialEvent` is satisfied), so `sh:then` fires. The `sh:then` shape requires `cg:spatialContext sh:minCount 1`; it is absent → violation.
- **NC-05** (`DataEvent` with `cg:spatialContext`): The `sh:if` test fails (not a `SpatialEvent`), so `sh:else` fires. The `sh:else` shape requires `cg:spatialContext sh:maxCount 0`; it is present → violation.

The `sh:else` branch on NC-05 is the more interesting case architecturally: it enforces that spatial data cannot silently accumulate on events that are not explicitly typed as spatial. This prevents a class of data hygiene errors where coordinates are copied across event types without being semantically acknowledged.

### NC-06: Invalid enumeration value

`cg:eventStatus` is asserted directly as `cg:StatusUnknown`, which is not in the `sh:in` list. In normal operation `cg:eventStatus` is derived by `EventStatusRule` and never asserted in input data. This test case deliberately asserts it to confirm the `sh:in` constraint catches out-of-range values regardless of whether the property was derived or asserted. This matters for implementations that may replay events from external stores where the derived value was serialised.

### NC-07: Orphaned reifier (the SHACL 1.2 test)

This is the most architecturally significant non-conformant case. The named reifier `ex:Reifier-NC07` is present and linked to the triple via `~ex:Reifier-NC07`, but it omits `cg:producedByEvent`:

```turtle
ex:TargetEntity  cg:piValue  "3.14"
    ~ex:Reifier-NC07 .

ex:Reifier-NC07
    # cg:producedByEvent intentionally omitted
    cg:assertedAt         "2026-03-08T09:50:00Z"^^xsd:dateTimeStamp ;
    cg:assertionAuthority ex:IngestServiceA ;
    cg:assertionGraph     ex:HolonContextGraph01 .
```

The violation fires on `ex:Reifier-NC07` itself — not on `ex:Event-NC07` — because `cgsh:AssertionAnnotationShape` is applied to the reifier node via `sh:reifierShape`. This is the defining SHACL 1.2 behaviour being tested: the shape targets the annotation node independently of the triple's subject, predicate, and object, and independently of the event node that produced it.

> **\[OPEN\]** Engine support for `sh:reifierShape` varies as of early 2026. This example is the primary test vector for that feature. The group should maintain it as a conformance test and track engine support status.

### NC-08: PolicyEvent missing its target

`cg:modifiedPolicy` is absent from a `cg:PolicyEvent`. This is the direct counterpart to C-03 (conformant), and it tests the `sh:then` branch of `cgsh:PolicyEventConditional`. Compare with NC-04, which tests `sh:then` for the spatial branch: the pattern is identical, only the event type and required property differ.

### NC-09: Coherence score out of range

`cg:measuredCoherence` is set to `1.5`, which exceeds `sh:maxInclusive "1.0"^^xsd:decimal`. This tests the numeric range constraint on `cgsh:ContextFacetShape`. The violation is simple but covers an important semantic invariant: a coherence score above 1.0 has no meaningful interpretation in the \[0.0, 1.0\] model and would corrupt any downstream coherence aggregation.

### NC-10: AuthEvent without lifecycle status

`cg:credentialLifecycleStatus` is absent from a `cg:AuthEvent`. This is the direct counterpart to C-04 (conformant) and the third `sh:if`/`sh:then` test across the NC set (alongside NC-04 and NC-08). The three together confirm that all three conditional branches in the schema behave independently and correctly.

---

## Design Observations for Group Discussion

### The annotation syntax split is meaningful, not cosmetic

The division of the six conformant examples between `{| |}` and `~IRI` syntax is intentional and reflects the two-tier traceability model implied by the schema. Events that produce routine transactional assertions (C-01, C-02, C-05) do not need their reifier nodes to be independently addressable. Events that modify governance objects — policies, boundaries, authentication sessions — do (C-03, C-04, C-06). The group should consider whether this distinction should be **normative**: that is, whether `cg:PolicyEvent`, `cg:AuthEvent`, and `cg:BoundaryEvent` should require named reifiers by constraint, or whether the choice should remain at the implementer's discretion.

### The `sh:else` branch on the spatial conditional is a data hygiene rule

NC-05 is the only non-conformant case that does not involve a missing required property. Instead, it fires because a property that should not be present _is_ present. The practical purpose is to prevent semantic contamination: an event typed `cg:DataEvent` that happens to carry `cg:spatialContext` is likely a mis-typed `cg:SpatialEvent`, not a legitimately non-spatial event that happens to have coordinates. Enforcing the absence is a stronger contract than merely making the presence optional on `cg:DataEvent`.

### The multi-hop SPARQL constraint in NC-03 has performance implications

The session expiry check navigates from the `authenticationContext` node up to the parent event to retrieve `prov:generatedAtTime`. In a large graph, this reverse traversal could be expensive. Implementers should consider whether the expiry check should be performed at ingestion time (in the control plane) rather than at validation time (in the SHACL engine). The schema enforces it via SHACL for completeness; real deployments may pre-filter at the control plane and treat the SHACL constraint as a post-hoc audit check rather than a hot path.

### NC-01 and the rule pipeline interaction

The interaction between NC-01's missing facet and the `EventStatusRule` is worth understanding precisely. The `FacetCompletenessRule` runs at `sh:order 0` and produces `cg:facetCompleteness 3` (three of four facets present). The `EventStatusRule` runs at `sh:order 1`, consumes that value, and produces `cg:eventStatus cg:StatusFailed`. Both rules fire even though the event is non-conformant — SHACL rules are not gated by constraint conformance. This means a non-conformant event still gets a status derivation, which is intentional: the control plane should be able to query `cg:eventStatus` on any event, conformant or not, and act accordingly.

---

## Open Questions Surfaced by These Examples

| # | Question | Raised by |
|---|----------|-----------|
| 1 | Should `cg:PolicyEvent`, `cg:AuthEvent`, and `cg:BoundaryEvent` require named reifiers by constraint? | C-03, C-04, C-06 |
| 2 | Should `cg:AuthEvent` be exempt from `cg:producedAssertion sh:minCount 1`? | C-04, NC-10 |
| 3 | Should `cg:producedAssertion` have a `sh:maxCount` upper bound? | C-06 |
| 4 | Should the session expiry SPARQL constraint be normative or advisory? | NC-03 |
| 5 | What is the current `sh:reifierShape` support status across target engines? | NC-07 |
| 6 | Should the `{| |}` / `~IRI` choice be normatively prescribed by event subtype? | C-03 vs C-01 |

---

## Validating These Examples

To validate against `context-event-shapes.ttl` using pyshacl:

```bash
pip install pyshacl

# Validate conformant examples only (expect: conforms = True)
pyshacl \
  --shacl context-event-shapes.ttl \
  --format turtle \
  --advanced \
  context-event-examples.ttl
```

> **Note:** pyshacl does not currently support `sh:reifierShape` (NC-07 and the reifier validation in C-01 through C-06). Those cases require a SHACL 1.2-conformant engine such as Apache Jena SHACL or TopBraid Composer. The group should maintain a reference engine matrix alongside these examples as engine support for SHACL 1.2 features matures.

---

*Last updated: 2026-03-08. Maintained by the W3C Context Graph Community Group.*
