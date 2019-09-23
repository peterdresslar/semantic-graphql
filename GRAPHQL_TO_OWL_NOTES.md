# Translating From GraphQL to OWL (and possibly other semantic graphs)

These are some WIP notes and/or documentation for migrating from GraphQL to graph languages like [OWL](https://en.wikipedia.org/wiki/Web_Ontology_Language).

The connection from GraphQL *to* OWL might be fairly obvious; in many cases, designers would typically select to work with OWL since it is more advanced and feature-rich than its predecessors. However, there may be specific use cases where [RDF-S](https://www.w3.org/TR/rdf-schema/) or even RDF is required. Interestingly<sup>*</sup>, the match from GraphQL to RDF-S is somewhat more congruent than from GraphQL to OWL. RDF on the other hand lacks the important `rdfs:subClassOf` Property, and so will not handle Object sub-typing terribly well.

Please note that the _syntax_ used herein is [Turtle](https://www.w3.org/TR/turtle/) (.ttl)

The GraphQL we are working specifically with comes from the [2018 GraphQL Specification](https://graphql.github.io/graphql-spec/June2018/). Extensions to that core specification may be considered at some point. 

**Table of Contents**

- [Getting Started]()
- [Examples]
- [Table of Conversions]()
- [To Do]()
- [More Notes]()

## Getting Started

We assume that you are working with a valid GraphQL Schema in some format.

## Examples

GraphQL SDL: For the following GraphQL Schema:

```js
"This is a Company"
type Company implements Organization {
  id: ID!

  "Legal name of the Organization"
  name: String!
  
  "Number of employees at this Company"
  employees: Int

  "Calendar year the Company was founded."
  yearFounded: Year

}

"Organization is a group of people in a unified social context"
type Organization {
  id: ID!

  "Legal name of the Organization"
  name: String!
}

scalar Year
```

The OWL we produce looks like (assuming we use defaults, and omitting the imports):

```json
:Organization
  a owl:Class ;
  rdfs:comment "Organization is a group of people in a unified social context." ;
  rdfs:label "Organization" ;
  rdfs:subClassOf :Thing .

:Company
  a owl:Class ;
  rdfs:comment "This is a Company." ;
  rdfs:label "Company" ;
  rdfs:subClassOf :http://foo.com#Organization .

# TODO Choose what to map stringScalar to. There are a lot of options--tempting to just call these fields Literals as I think that is all we can infer from a scalar's exsitence within a type. There would be a difference between the OWL and the RDFS implementation
:name
  a owl:DatatypeProperty ;
  rdfs:comment "Legal name of the Organization." ;
  rdfs:domain :Thing ;
  rdfs:label "name" ;
  rdfs:range xsd:string .

:employees
  a owl:DatatypeProperty ;
  rdfs:comment "Number of employees in this Company." ;
  rdfs:domain :Thing ;
  rdfs:label "employees" ;
  rdfs:range xsd:string .


WIP EXAMPLE NOT DONE

```


## Table of Conversions
From [the specification](https://graphql.github.io/graphql-spec/June2018/#sec-Names):
> GraphQL Documents are full of named things: operations, fields, arguments, types, directives, fragments, and variables. All names must follow the same grammatical form.

The following table lists GraphQL "things" and their default conversions to Semantic Graph elements. Since GraphQL is semantically *less* rich than the target Semantic languages, the conversion process needs to make a number of assumptions by default. These defaults will tend to choose the simplest options or representations in the target structures. This list can be extended to other formats.

| GraphQL Named thing | Example | OWL | RDFS |
| --------- | ------- | -------- | ------- |

_WORK IN PROGRESS_

Note that for OWL we are assuming the [OWL 2 ontology].

## To Do

Inputs
- [ ] Parse SDL schema into Object
- [ ] Parse Javascript schemas
- [ ] Get Schema by URL

Conversion: Input GraphQL Specification Coverage
- [ ] Basic Schema stuff (Schema itself, Types, Scalars, Objects)
- [ ] Root Operation (Query + Params)
- [ ] Descriptions (still pretty basic!)
- [ ] Lists
- [ ] Enums
- [ ] Interfaces
- [ ] Unions
- [ ] Non-Null and Non-Null+List
- [ ] Directives
- [ ] Inputs (Mutations)
- [ ] Type System Extensions
- [ ] Schema Extensions

GraphQL Document Stuff
- [ ] Document, OperationDefinition, Query?
- [ ] Fragments

Outputs
- [ ] OWL (.ttl, other?)
- [ ] RDFS? (No OWL)?
- [ ] RDF?

GraphQL Extensions / Ecosystem
- [ ] Relay
- [ ] Nexus?

Other
- [ ] This To Do List
- [ ] Expand Tests
- [ ] Schema validation issues and other edge cases
- [ ] Documentation
- [ ] Examples (Hello World. SWAPI? Kitchen Sink?)

## More Notes

- Should GraphQLString become a `rdfs:Literal` or a `rdf:string`? Possibly this would be an option. This same question can extend to OWL.
- ID is a special Type -- handle as a one-off? Options?
- Should be possible to match up Types to classes in a custom Ontology--that seems like a pretty neat fature.

.

.

.

---
\* For certain very fine values of "interesting"