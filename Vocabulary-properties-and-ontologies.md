
### Introduction


For our purposes the simplest controlled vocabulary is a flat (non-hierarchal) list of terms.

An ontology is a heirarchy of controlled vocabulary terms that explicitly encodes both relatedness and category.

Using the example of subject (aka "topical subject"), either technology allows us to make assertions about the
data and relations between identities. 

- Ontolgies allow explict assertions, and stronger assertions

- Both technologies are weak if a subject is missing, that is, the identity was not marked up (to use XML-speak)

- More extensive is better, but the extent of ontology or vocabulary is limited by resources

- Ontologies are difficult to create and maintain

- Flat vocabularies are less difficult to create and maintain (perhaps much less difficult)

- Both vocabularies have an implicit definition for each term; however, two different editors may understand
  somewhat different implied defintions

- A single explicit definition can be added to each term (although I haven't seen this; it may only exist in
  fields outside the archival world)

- Explicit definition (see below) vocabularies are difficult to create and maintain

- Flat vocabulary can be done with a single database table

- An ontology requires at least 2 database tables, perhaps 3

- Policies need to be developed for create, update, and delete

- Policy complexity is greater for an ontology

- Using computers, an identity may have multiple subjects of either ontology, or flat vocabulary

It might also be sensible to design the properties with multilingual vocabulary terms. By multilingual I mean:
multiple terms for each unique ID where each term is specific to a specific language, and all terms with the
same ID share a definition. 

Each term has a unique id, and a definition (implied or explicit). This is a simple dictionary. Explicit
definition would improve the vocabulary, but takes more work.

The flat vocabulary has a single "type" for each term, where type examples are: topical subject, gender,
function, etc. In an ontology, the "type" is handled by the ontology structure, which is explicit, but
discovering the type requires tree-traversal.

### Property domain


Intellectually each property has a definition. Technically, dding an explicit definition only required an
additional field. Explicit definitions are (almost?) trivial technically, but are challenging
intellectually. The Wikipedia clarifies this issue since ambiguous terms lead to a disambiguation
page. Wikipedia "definition" is the article.


### Proper entities are not properties

There is no property "detroit", although there is a CPF entity for "Detroit, MI USA", complete with a field
for the corresponding geonames ID. It is technically possible to conflate CPF entities in the user interface
to enable the construction of a topical subject "detroit", although that intellectually sub-optimal. The data
should be as well-constructed as possible. A search for subject + place is not a search for subject +
subject(placename).

Consider what happens if (and I'm opposed to this) all CPF entities were imported into the property
table. That would be denormalization, data duplication, and would only end in tears.


### Use Markov models instead of an ontology

Ontologies are difficult to create, and there is disagreement about them, both in structure and content. There
are several to choose from, the the properties they use are somewhat incomplete as confusing. Linking (aka
markup of) each aspect of an identity record's properties to the ontology is an onerous task, and fraught with
several types of errors. Linking is often a judgement call.

A technology exists that is easy to implement, powerful, and tractable in real life and works well with flat
vocabularies.

We can create a Markov matrices of the terms. Multiplying Markov matrices causes them to converge which
reveals property relatedness as exists in the data. The effect is quite powerful and (almost?) obviates the
need for a hand-created ontology. Missing relations (known to exist, but not discovered by the Markov
convergence because no records actually contain the desired relation) are easily rectified by either of two
methods. The first would be to add the correct relations to existing records. The second works by creating
non-public special records containing related terms and making the special records available to the Markov
modeling process. The whole Markov solution is only 2 or 3 pages of code, so we can write it and evaluate the
effectiveness.

See: Everything is miscellaneous.

https://en.wikipedia.org/wiki/Everything_Is_Miscellaneous

http://www.youtube.com/watch?v=WHeta_YZ0oE

http://www.youtube.com/watch?v=x3wOhXsjPYM


### Ontology uses property, but is a separate problem

The alternative to the Markov relation discovery is an ontology that relates terms both in relatedness,
and as a hierarchy from broad to narrow. There are existing ontologies with varying levels of detail.

When describing two very different things, some flat terms would be the same. For example the topical
subject of a publisher, and of a painting (a work of art). The publisher creates books of automobiles,
especially cars which have been artistically painted. The work of art is a painting of an automobile. In this
example, both publisher and painting have two subjects, and both are identical.

```
Publisher subject:Automobiles subject:Painting (fine art)
Painting subject:Automobiles subject:Painting (fine art)
```

The underlying terms are the same in both. However, the ontological relationship is quite different
because one is a corporate body, and the other is an art object. It is not the domain of a flat property to
know how it is applied to a database record. Also, the larger context of what is being described changes how
the description is perceived. In any case, the use of a flat vocabulary is sufficient for search and discovery, and
Markov matrices can discover relatednesss between records. Hierarchy becomes another type of relatedness. In
the Markov world, both the publisher and the painting have a linkage to "Engineering" because there are
identities in the database with both "Automobiles" and "Engineering" as subjects.

The example above is limited to terms as topical subject. It seems reasonable to add fields in order to apply
additional terms (beyond "topical subject") "typeOf" or "isA", while still using the same (original, large)
list of terms. Types of "publisher (corporateBody)" and "painting (object)" seem obvious. Applying both
property and type pairs will explicitly categorize any database record, even without using an
ontology. However, it is unclear how this somewhat loosely coupled description will impact being able to
reason about database records. This also requires adding fields to the CPF database schema, which carries
serious baggage.


### Ontology and property interact to create search facets

In general, a search for a parent property should include all child properties as specified by the
ontology. Searching for the Spanish term "ropa" (clothes) will include "cinturon" (belt) which has the English
term "belt (clothing)". This works well as long as the ontology is complete. 

Interestingly, we might be able to apply Markov matrices to identities marked up via ontology, with the same
sort of relatedness building that occurs with a flat vocabulary list.
