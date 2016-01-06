---
title: (redbook) What Goes Around Comes Around
layout: "post"
permalink: what-goes-around-comes-around
category: computers
tags: [papers, databases, redbook]
---

*This is part of a series of posts about papers from the latest Red Book (Readings in Database Systems). The post that follows is mostly an annotated (non-comprehensive) summary of the paper -- what I found interesting, and want to discuss further.*

- Michael Stonebraker,  Joseph M. Hellerstein. [What Goes Around Comes Around][wgaca]. (2005)

## summary

The first paper discussed is a summary of 35 years of database systems' data models, grouped loosely into 9 eras. It's fascinating as a historical record, written with a bunch of opinions and wry humor.

The paper describes each data model era by modeling the same data -- suppliers, parts, and the relationship between them (supplier, part, quantity available). The authors describe the historical context of that data model, and often select a representative implementation to discuss. Each section ends with a set of [lessons learned](#footnotes) during that historical period.

The eras discussed in the paper are:

1. Hierarchical (IMS)
  - represented data as a tree relationship, which is a fairly restrictive model.
2. Network (CODASYL)
  - represented data as a network of interlined entities, which is more flexible than hierarchies, but way more complex.
3. Relational
  - represented data as tables.
4. Entity-Relationship
  - represented data as entities & their relationships. Never took off as a database's data model, but it really took off as a model people used to design their database schemas.
5. Extended relational (R++)
  - relational databases + other small improvements (enum types, aggregation, etc.)
6. Semantic
  - focused on classes & inheritance, making it easier to map to OO languages. ~no adoption.
7. Object-oriented
  - OODBs, which essentially provide persistent representations of programming language objects. Seemed awfully similar/subsumed by serialization formats?
8. Object-relational (POSTGRES)
  -  relational databases plus user-defined datatypes, user-defined functions, and user-defined access methods (custom indexing, etc.)
9. Semi-structured
  - schema-last datasets, plus features from all previous eras.

The Red Book defines 3 other recent data models/trends:

<ol start="10">
  <li>The rise of JSON as
    <ul>
      <li>(1) a general purpose hierarchical data format, à la IMS, (described as a "disaster in the making")</li>
      <li>(2) as a representation of sparse data (c.f. JSON fields in Postgres/MySQL) (reasonable/effective)</li>
      <li>(3) as a mechanism for "schema-on-read," which functions essentially as a projection operation. (~fine)</li>
    </ul>
  </li>
  <li>Map-reduce
    <ul>
      <li>"not an architecture with any broad-scale applicability"</li>
    </ul>
  </li>
  <li>HDFS clusters as "data lakes"</li>
</ol>

The authors describe a few criteria by which they judge the technical merit of the models:

- physical data independence
- logical data independence
- complexity of the implementation
- flexibility of the model
- commercial viability (performance, need for specialization, etc.)

The concepts of physical and logical data independence are particularly important here, since those are the means by which improvements to the core database (physical) or application code (logical) are made. Physical data independence lets a database use different (or multiple!) storage engines. For example, MySQL lets you use InnoDB and MyISAM for different tables. I've heard people say that, similarly, MongoDB is starting to become slightly more legit now that it supports multiple storage engines.[^nelhage]

The paper also discusses the history of the "Great Debate" between supporters relational databases and CODASYL/IMS. This is fascinating -- both because we're having the same arguments again in the 2010s, and because the argument falls into a shape familiar from other Great Debates (assembly/compilers, SOAP/JSON apis, dynamic/typed languages etc.) This probably happens every time a new abstraction barrier is crossed.

## on schemaless databases

The schema-last section was especially interesting to read, after the recent surge in popularity of schemaless/flexible schema databases. Many of the points still hold, especially those about semantic hetergeneity.

Semantic heterogeneity happens when a field on a bunch of records of the same type (an employee, for example) does not conform to a common representation (an employee's address might be a string, foreign key, or (lat, long) tuples.) This field holds no inherent type, which leads to all sorts of hilarity. Most use cases of these databases still requires some sort of structured schema -- they've just pulled most of the structure of that schema into application code, instead. The state of the database becomes a representation of all of the different states of the code that produced it, which requires the application code to be able to deal with this combinatorial complexity.

> any schema-last application will have to confront semantic heterogeneity on a record-by-record basis, where it will be even more costly to solve.

This tradeoff in flexibility often leads to increased logical data dependence and complexity down the line.

The one case they present for schema-last applications is information retrieval on pure text. This seems like ElasticSearch's use case -- a loose(ish) schema that allows you to define searchable fields.

## on industry adoption

The paper's story arc describes the combination of historical quirks that created the current relational DBMS environment, and its widespread industry adoption.

Early relational database implementations were portable, which helped them win out the VAX market, as minicomputers rose in popularity. In contrast, the mainframe market was primarily dominated by IMS. Eventually, IBM chose to throw its weight behind DB/2 and SQL, effectively killing out IMS/CODASYL and alternative relational query languages like QUEL.

> Technical debates are usually settled by the elephants of the marketplace, and often for reasons that have very little to do with technology.

However, designing schemas for relational databases was still difficult. This led to the rise in popularity of the entity-relationship model (those familiar boxes and arrows and stars from UML), which enabled mere mortals to put together normalized schemas.

Later, in the 1980s, vendors were singularly focused on transaction performance, which meant that features proposed to make relational databases easier to use fizzled out. No one was particularly interested in features that could effectively be boiled down to pure SQL, even if they significantly simplified the programming logic. User-defined data types and access methods weren't added to relational databases until vanilla RDBMSs were useless for GIS, which eventually led to POSTGRES.

> Unless there is a big performance or functionality advantage, new constructs will go nowhere.

This echoes Bell Labs' approach to innovation -- significantly "better or cheaper or both".

## Related readings (where to go from here)

- Readings in Database Systems, of course
- The implementation of postgres
- A critique of the SQL database language

## footnotes

Collected and summarized, the lessons are:

  1. Physical and logical data independence are highly desirable.
  2. Tree-structured data models are very restrictive.
  3. It is challenging to provide sophisticated logical reorganizations of tree-structured data.
  4. A record-at-a-time user interface forces the programmer to do manual query optimization, and this is often hard.
  5. Networks are more flexible than hierarchies, but more complex.
  6. Loading and recovering networks is more complex than hierarchies/independently decomposable data.
  7. Set-at-a-time languages are good, regardless of the data model, since they offer much improved physical independence.
    - might be worth combining this and the record-at-a-time point, and the query optimizer point later on into a paragraph.
  8. Logical data independence is easier with a simple data model than a complex one.
  9. Technical debates are usually settled by the elephants of the marketplace, and often for reasons that have very little to do with technology.
  10. Query optimizers can beat all but the best record-at-atime DBMS application programmers.
  11. Functional dependencies are too difficult for mere mortals to understand. (KISS)
  12. Unless there is a big performance or functionality advantage, new constructs will go nowhere.
  13. Packages will not sell to users unless they are in major pain.
  14. Persistent languages will go nowhere without the support of the programming language community.
  15. The object-relational model has 2 main advantages: putting code in the database (and thereby blurring the distinction between code and data), and user-defined access methods.
    - this sounds v. similar to the repository pattern/storage-srv. Is this a common pattern? Is a repository just v. similar to the object-relational model?
  16. Widespread adoption of new technology requires either standards and/or an elephant pushing hard.
  17. Schema-last is probably a niche market.
  18. XQuery is pretty much OR SQL with a different syntax.
  19. XML will not solve the semantic heterogeneity either inside or outside the enterprise.

[^nelhage]: nelhage's [What MongoDB Got Right](https://blog.nelhage.com/2015/11/what-mongodb-got-right/) is an interesting read.

[wgaca]: https://mitpress.mit.edu/sites/default/files/titles/content/9780262693141_sch_0001.pdf
