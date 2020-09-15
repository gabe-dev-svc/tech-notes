# [2] Data Models and Query Languages

<!-- toc -->

- [Relational Model vs Document Model](#Relational-Model-vs-Document-Model)
  * [Birth of NoSQL](#Birth-of-NoSQL)
  * [Object-Relational Mismatch](#Object-Relational-Mismatch)
  * [Are Document Databases Repeating History?](#Are-Document-Databases-Repeating-History)
  * [Relational vs Document Databases Today](#Relational-vs-Document-Databases-Today)
    + [Which data model leads to simpler application code?](#Which-data-model-leads-to-simpler-application-code)
    + [Schema flexibility in the document model](#Schema-flexibility-in-the-document-model)
    + [Data locality for queries](#Data-locality-for-queries)
    + [Convergence of document and relational databases](#Convergence-of-document-and-relational-databases)
  * [Query Languages for Data](#Query-Languages-for-Data)
    + [MapReduce Querying](#MapReduce-Querying)
  * [Graph-Like Data Models](#Graph-Like-Data-Models)
    + [Property Graphs](#Property-Graphs)
    + [Triple-Stores and SPARQL](#Triple-Stores-and-SPARQL)

<!-- tocstop -->

## Relational Model vs Document Model

### Birth of NoSQL
Driving forces behind NoSQL:
- Need for greated scalablity than relational databases can easily achieve, including very large datasets or very high write throughput
- Preference for free and open source software over commercial database products.
- Specialized query operations not otherwise supported very well by relational models
- Frustration with restrictiveness of relational schemas and desire for a more dynamic and expressive data model.
_Polyglot persistence:_ Use of relational databases alongside broad variety of nonrelational datastores.

### Object-Relational Mismatch
- OO Programming leads to criticism of SQL data model due to the translation layer sometimes required.
- Normalization was very highly-adopted in traditional SQL models, however, now SQL standard added support for structured data types and XML data. JSON data types are supported by Postgres, DB2, and MySQL
- Resumes and objects with tree structures are explicitly shown in JSON representations. Document-oriented databases support JSON. 
- The idea behind normalization is to remove duplication in databases. Storing values as references instead of the actual text is a form of normalization often seen in databases. 
- Normalizing data requires many-to-one relationships--which don't play nice with the document model due to the often poorly supported ability to join. This is overcome by making multiple requests to the database but those joins are now application code instead of just database processing.
- Document models are usually pretty good for one-to-many relationships, but as previously stated, have shortcomings to many-to-many relationships 


### Are Document Databases Repeating History?
- CODASYL was a network model which fizzled out
- Relational and document databases are not fundamentally different when dealing with many-to-one and one-to-many relationships. Relational databases use foreign keys while document databases use document references. 

### Relational vs Document Databases Today
Main arguments in document model:
- Schema flexbility
- Better performance due to locality
- Closer to the data structures used by the application.
Counter from relational model:
- Better support for joins and M:M M:1 relationships

#### Which data model leads to simpler application code?

> For highly interconnected data, the document model is awkward the relatnional model is acceptable, and graph models are the most natural.


If data has document-like structure (tree of one-to-many relationships where typically the entire tree is loaded at once) then it's probably a good idea to use a document mode. Splitting the document into multiple tables can lead to cumbersome schemas and unnecessarily complicated application code. 

- If application does use M:M relationships, document model becomes less appealing. 

#### Schema flexibility in the document model
- Document databases are sometimes called schemaless, but sometimes implicit schemas exist due to application model structures of things of the sort. More appropriate term is schema-on-read.
- Relational databases don't have the schema flexibilty and require migrations. 
- New columns can be populated come read-time. 
- Schemaless can prove useful when data needs to be flexible, be it because there are objects which don't have a set structure or the objects being recorded can change because they come from some external dependency.
- Schemas however can help support and enforce strucuture.

#### Data locality for queries
- Applications requiring access to entire documents can gain advantage from data locality due to the lack of joins
- Column-family concept in Bigtable data model (used in Cassandra and HBase) has a similar purpose of managing locality. 

#### Convergence of document and relational databases
- Certain RDBMS (e.g. Postgres) have support for document objects (i.e. JSON)
- Certain document databases (e.g. RethinkDB support relational-like joins ini its query language)
- Document and relational databases are converging.

### Query Languages for Data
- _Imperative language:_ Tells the computer how to perform certain operations in a certain order.
- _Declarative language:_ You must specify the conditions, pattern, and transformation of data being queried. Hides implementation details of database engine. 
  - Declarative languages lend themselves to parallel execution.
- Example of a declarative language for the web is CSS.

#### MapReduce Querying
- _MapReduce Querying:_ Programming model for processing large amount of data in bulk across multiple machines. Limited form supported by some NoSQL Datastores (MongoDB and CouchDB) as a mechanism for performing read-only queries across many documents. 
  - Neither delcarative or imperative, somewhere in between. 
  - Based on `map` (a.k.a `collect`) and `reduce` (a.k.a `fold`, `inject`) functions existing in functional programming languages. 
  - e.g.:
```JavaScript
db.observations.mapReduce(
  function map() {
    var year = //getFullYear();
    // Continue for day and month
    emit(year + "-" + month + "-" + day, this.numAnimals);
  }, 
  function reduce(key, values) {
    return Array.sum(values);
  },
  {
    query: { family: "Sharks" },
    out: "monthlySharkReport"
  }
  
);
```

- The code snippet above would first call map to create a `map` looking something like `{'2019-01-01', [9,1]}` and the reduce function would return `10`.
- MapReduce functions must be pure functions, meaning they only use the data passed into them. 
- MongoDB added support for a declarative query language called the _aggregation pipeline_

### Graph-Like Data Models
- Again, if an application has mostly 1:M or 1:1 relationships, or no relationships at all between records, then the document model is appropriate. 
- M:M can take great advantage of Graph-Like Data Models
- _Vertices:_ a.k.a `nodes` or `entities`
- _Edges:_ relationships between vertices. 

#### Property Graphs
- Verticies consist of a unique identifier, a set of outgoing edges, a set of incoming edges, and key-value properties
- Edges consist of a unique ID, vertex at which edge starts (tail vertex), vertex at which the edge ends (head vertex), label to describe the kind of relationship between the vertices, and a collection of properties. 
- Property graph data models make it easy to define and visualize relationships as you are able ot follow one vertex to the next via edges. 
- Great for evolvability, a graph can easily extend to accomodate changes in application's data structures. 
- _Cypher Query Language:_ Declarative query language created for property graphs, specifically Neo4j

```
CREATE
  ([vertex:Type] {property:value}),
  (([vertex]) -[:{association}]-> (vertex))
```
#### Triple-Stores and SPARQL
- Similar to the property graph model
- All information is stored in the form of three-part statements `(subject, predicate, object)` `(Jim, likes, bananas)`
- If the object is a primitive data type, then the predicate behaves like a property graph model's attributes for a vertex
- Otherwise, the predicate behaves like a graph model's edge, describing a relationship.
- _RDF Data Model:_ Resource description framework.
- _SPARQL:_ Query language for triple-stores using the RDF data model
