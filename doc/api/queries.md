---
layout: doc
title: Queries
---


**QMT** is an MMT-based query language.
It was first described in the [corresponding paper](http://kwarc.info/frabe/Research/rabe_querying_12.pdf).

### Queries

QMT uses three basic concepts: *types*, *queries*, and *propositions*.

* **Types** inherit from [`ontology.QueryType`](http://kwarc.github.io/MMT/api/index.html#info.kwarc.mmt.api.ontology.QueryType). Types are either products of [`ontology.QueryBaseType`](http://kwarc.github.io/MMT/api/index.html#info.kwarc.mmt.api.ontology.QueryBaseType)s or the power type of such a product.
* **Queries** inherit from [`ontology.Query`](http://kwarc.github.io/MMT/api/index.html#info.kwarc.mmt.api.ontology.Query). They are typed expressions.
* **Propositions** inherit from [`ontology.Prop`](http://kwarc.github.io/MMT/api/index.html#info.kwarc.mmt.api.ontology.Prop). They are expressions that are used as side conditions within queries.

The specific types, queries, and propositions can be best understood by browsing the [API documentation](http://kwarc.github.io/MMT/api/index.html) and the known subclasses for the respective class.

At the moment the only concrete syntax for queries is an ad hoc XML format.
The algorithms for parsing, type inference, and validity checking for queries are straightforward and defined in the respective companion objects [`ontology.Query`](http://kwarc.github.io/MMT/api/index.html#info.kwarc.mmt.api.ontology.Query$) and [`ontology.Prop`](http://kwarc.github.io/MMT/api/index.html#info.kwarc.mmt.api.ontology.Prop$).

### The MMT Ontology (T-Box)
The MMT ontology is a fragment of the query language.

* The QueryBaseType [`ontology.PathType`](http://kwarc.github.io/MMT/api/index.html#info.kwarc.mmt.api.ontology.PathType) is the type of [MMT URIs](uris.html).
* **Concepts**, i.e., unary predicates on this type, are the subclasses of [`ontology.Unary`](http://kwarc.github.io/MMT/api/index.html#info.kwarc.mmt.api.ontology.Unary).
* **Relations**, i.e., binary relations between MMT URIs, are the subclasses of [`ontology.Binary`](http://kwarc.github.io/MMT/api/index.html#info.kwarc.mmt.api.ontology.Binary).

### Maintenance of the Relational Knowledge (A-Box)

On disk, the dimension `relational` of an [MMT archive](../applications/archives.html) maintains the relational knowledge, i.e., the set of known URIs and the instances of the unary and binary predicates.
In memory, this is done by the class [`ontology.RelStore`](http://kwarc.github.io/MMT/api/index.html#info.kwarc.mmt.api.ontology.RelStore).

The relational dimension is written automatically when an import build target is used.
To load an archive's relational knowledge into memory, the [shell](../applications/shell.html) command `relational` can be used.

### Evaluation

The evaluation of queries is implemented in the [`ontology.Evaluator`](http://kwarc.github.io/MMT/api/index.html#info.kwarc.mmt.api.ontology.Evaluator).
According to its type, every query evaluates to a tuple of base values or to a set of such tuples.
For each [`ontology.QueryType`](http://kwarc.github.io/MMT/api/index.html#info.kwarc.mmt.api.ontology.QueryType), there is a corresponding subclass of [`ontology.BaseType`](http://kwarc.github.io/MMT/api/index.html#info.kwarc.mmt.api.ontology.BaseType), from which the return type of evaluation is built.

Evaluation results can be used in the API or exported in ad-hoc XML syntax.

### Extending the Query Language

There is an [extension](extensions/) for adding new atomic functions to the query language.
Several such functions expose basic algorithms (parsing, type checking, presentation, etc.) to the query language. See the documentation of [`ontology.QueryExtension`](http://kwarc.github.io/MMT/api/index.html#info.kwarc.mmt.api.ontology.QueryExtension).


### Querying via HTTP
The MMT [HTTP interface](../applications/server.html) opens a QMT query server that accepts a QMT query in XML encoding as the body of an HTTP POST request.
Some example queries are:

```
<uris concept="C"/>
```

retrieves all known MMT URIs of concept C.

```
<related>
  <literal uri="T"/>
  <toobject relation="Declares"/>
</related>
```

retrieves all declarations of theory T.

```
<related>
  <literal uri="T"/>
  <sequence>
    <transitive>
       <toobject relation="Includes"/>
    </transitive>
    <toobject relation="Declares"/>
  </sequence>
</related>
```

retrieves all declarations of any theory included into theory T.

### Querying from JavaScript
The qmt object of the MMT JavaScript code provides bindings for building queries and an Ajax style interface for server side query evaluation. 
