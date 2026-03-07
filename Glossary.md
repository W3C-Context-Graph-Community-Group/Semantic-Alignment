# Context Graph Specification Glossary

These definitions are from the Context Graph specification maintained by the W3C Community Group.

## Assertion
An assertion is a statement of belief. It is the foundation of the Semantic Web, and the basis for RDF (and many graph representations of knowledge). An assertion is epistomological in that it is not necessarily truth (in the sense that it is an accurate reflection of the world) but rather is a statement that reflects the truth as given by a specific authority. A set of assertions is called a graph, though for purposes of discussion, such a graph should be considered a [hypergraph](#hypergraph). 


## Signal
~~Measurable incomplete and/or incoherent information between local events and global systems. Context is not noise, not metadata, and not a linguistic concept. The goal of the group is to reduce it to a formal, information-theoretic representation of the state of missing and misaligned information in communication between systems — in the Shannon sense: sender, receiver, channel, signal, and uncertainty — structured uncertainty that can be sampled, bounded, scored, and reduced through observation.~~

A signal is an encoded event produced by an external environment that will be added to the context graph once the signal (as an event message) has been authenticated (verified), checked for well-formedness, and validated. A signal generates either a validated context event, if it passes validation, or a report if it does not.

## Context
Within a graph, a context is a specific graph configuration that forms a testable pattern for validation and rule initiation. 

## Reifications
A reification is a set of assertions that are made about a given statement or ground assertion. A reification is notable in that rather than describing a specific entity, the reification describes either the relationship between two specific entities, or between an entity and its value. Reifications are significant because they are typically used to describe the event that triggered the ground assertion to be added to the context graph, in effect creating a hypergraph event log that can then be analysed as a graph post hoc. Please see [Reifications and Context Events](#reifications-and-context-events).

## Hypergraph
A hypergraph is a labeled directed cyclic graph (LDCG) that permits reifications, meaning that an assertion can be treated as a first-class resource separate from its components. Both knowledge graphs and context graphs are hypergraphs. The assumption being made here is that all references to graphs made within this working document are to hypergraphs, and refer both to reified RDF Star (RDF 1.2) based graphs and (with certain caveats) to LPG-based graphs such as those that support the OpenCypher standard, as well as any other products or open source projects that support IRI addressability, triples manipulation and reification. Knowledge Graphs prior to 2016 without reification support are grandfathered in under the term but may not have all the functionality described as part of this effort.  

## Holon
A holon is an entity that can be represented as a system. For example, a country can be represented as a collection of states, cities, biomes and so forth. Holons represent computational boundaries between systems and, as such, typically manifest as "internal" state vs an external representation of that state. Holons may be represented as named graphs, and interact via reifications on the representation of that named graph as an entity.

## Context Event
A Context Event is a process or activity that spawns a record (an assertion) and associated reification of that assertion that is published into the context graph. The event has a system-generated unique name or identifier that can be referenced by subsequent events, creating an event trail, and to the extent possible maintains temporal and spatial information about that event along with related content (who or what initiated the event, what was changed during the event, classification of the event and so forth). The event itself is ephemeral and will typically be a change of the state of the world as modelled by the graph; however, the context event follows a specific standard (the coherence protocol) and the Context Event entity within the Context Graph is the record of the event according to that protocol.

## Context Graph
An auditable log of Context events as a local representation of local and global systems boundaries, as a hypergraph. The graph itself is an append-only data structure that represents a data fabric. Context graphs may also have a processing layer that generates validation reports and a knowledge graph-type view of the processed information for easier access. Context graphs typically provide a layer of authenticated certificates to protect the underlying structure of the graph itself, and are usually not directly queryable except through internal processes via the [Context Graph Control Plane](#context-graph-control-plane).

~~A context graph assigns identity to atomic units of information related to context events using a canonical "Four Facets Model": Data, Meaning, Structure, and Context. A Context Graph is intentionally the most simple unit of information to be general to any domain and agnostic to any technology. A Context Graph instance is ephemeral — instantiated on demand at a boundary, populated during an interaction, and producing a persistent resolution trace that survives after the instance is destroyed.~~

## Knowledge Graph
A knowledge graph is a hypergraph structure focused on direct assertions and indexed analytic computations, and is typically more topically oriented than a context graph. In the CG architecture, the knowledge graph is typically computed from the context graph and is usually more publicly accessible. 

## Shapes and SHACL
A shape is a structural, rather than semantical or Classical, representation of the constraints and rules acting upon specific nodes and their contexts within a graph. The Shape Constraint Language (SHACL) is a language for the construction of and interaction with shapes within one or more graphs. it is an abstraction language that utilises the abstractions inherent in RDF 1.2, but most of the design principles involved with SHACL can be carried over with minor modification to the LPG-based languages as well in an isomorphic fashion. This proposal is written with SHACL in mind as the graph control plane.

## Constraint
A constraint is a restriction that exists upon an entity that defines what states it can and cannot be in, as represented within a graph. When an change request occurs, the change is validated, and if it passes validation is it logged (and any rules defined on it are initiated), when it fails, a report is generated, which also may initiate additional action.

## Rule
A rule generates new content upon validation of a given change in the graph, and is initiated when a state change is made relevant to a particular context within the graph. This rule will generally create additional facts, but may also initiate external services through a defined protocol.  

## Report
A report is an artefact (as a named graph) generated when a particular validation fails, indicating the reason for the failure. Reports are treated as part of the context graph. Depending upon the (configurable) severity of the invalidation, the report may be generated even when a warning or information node is validated. Reports can be queried as part of the event system.

## State Machine
A state machine within the current context is a system that can accept events (insertions from external systems, queries, modifications, and so forth) that, if validated, produce new information via rules, or generate reports that can initiate other actions if validation fails. The state machine is deliberately deterministic and mechanistic, though it can rely on external agentic systems to initiate external actions. The context system is a state machine.

## Coherence
The degree to which required meaning, structure, data, and context are sufficiently aligned for the current intent and risk tolerance at a boundary. When coherence is high, systems can act. When coherence is low, the gap between local and global state is consequential — the same term, the same data, the same structure may produce different outcomes depending on which system interprets it.

## Coherence Protocol
The specification for composing a coherence representation that may use multiple Context Graphs across system boundaries: measuring gaps and how they propagate, how resolution traces link. The Coherence Protocol is distinct from the Context Graph data model — the Context Graph describes a single boundary; the Coherence Protocol describes how boundaries compose.

## Context Graph Control Plane
A control plane is an interface layer that connects the system's state machine to external events. This manifests at several control points: at the event ingestion layer, which takes external data and formulates them as events (this may also perform routing to other control graph state machines depending upon categoriation), at the validation stage, which incorporates verifiable certificates in order to ascertain access control levels, at the rule level when new content is created, and at the report level when an invalidation error occurs. This approach is designed to scale readily, as each state machine can handle specific types of context.

## Verifiable Credentials

The context graph is not an open or public triple store. Because it can act in an agentic sense through the control plane, it needs to be governed. Among other things, this means that validation _includes_ the ability to read the intent expressed within each event, even if the message is otherwise valid. Such intent can be determined through certificates and authentication events. For instance, a bank transaction requires that a trusted session be enabled cryptographically and that it remain in force until the session expires according to the dictates of the certificates. Certificates are submitted as part of an authentication event, and either natural expire or terminate when validation fails for specific reasons. 

## ~~Intent (Previous)~~

~~The actor's pragmatic purpose at a boundary. Unlike semantics, which defines terms with the purpose of shared consistency as an authority, intent is pragmatic — it reflects an individual actor's language, understanding, and purpose. Intent may be explicit (stated by the user), inferred (derived by the system from available evidence), or composed (assembled from multiple sources, including system designer intent and subsystem composition). When consequential and unresolved, intent should be sampled across the boundary and recorded in the resolution trace. Intent is the information that determines which resolution of ambiguity is correct for this actor, at this moment.~~

## Intentional Graph

Intent is the anticipation of actions a given actor will take, given certain events (priors). It is, in effect, a simulation of a context graph based upon a hypothetical context. Such a simulation can  be called an Intentional Graph. The analysis of such Bayesian priors can be added as annotational overlays, and from these analyses, the Intention Graph, partitioned into a separate forward-facing named graph, can be constructed. The intentional graph then participates in a conversation (a narrative interaction) with an external agent that provides the relevant events based upon surfaced content within the simulation. Once the simulation has terminated, the external agent may then initiate events based upon the results of the intentional graph as a merge.

## Agency

One key point to understand here - the context system does not (and arguably cannot) have a concept of external agency. It is a state machine, and beyond what the system perceives through external events (epistomological or epistolic) the state machine cannot "know" in any meaningful sense, agentic intent. What it can do, however, is append to the schema constraints and rules those specific conditions and actions that cause a change in behaviour based upon specific context - when Jane is performing the actions, this is how the rules and behaviours for these relationships behave as compared to how they do so when John as agent is performing those actions. This system is reactive but evolutionary. 


## ~~Decision Interface~~
~~A neutral contract between the Context Graph's coherence measurements and external decision models. Defines what inputs a decision model receives (the uncertainty vector, policy parameters, boundary metadata), what outputs it must return (a protocol action and optional flags), and what must be recorded in the resolution trace for auditable replay. The interface is neutral to the choice of decision framework.~~

---

## Links

- [W3C Context Graphs Community Group](https://www.w3.org/community/context-graph/)

## License

- **Specifications and registry content** — [W3C Software and Document License (2023)](https://www.w3.org/copyright/software-license-2023/)
- **Source code** — [MIT License](https://opensource.org/licenses/MIT)
- **Contributions** — All contributions are made under the [W3C Community Contributor License Agreement (CLA)](https://www.w3.org/community/about/agreements/cla/). You must [join the Community Group](https://www.w3.org/community/context-graph/) before contributing.

See [LICENSE.md](LICENSE.md) and [CONTRIBUTING.md](CONTRIBUTING.md) for details.
