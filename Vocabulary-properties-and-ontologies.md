
### Introduction


There are two related yet distinct issues: controlled vocabularies and ontologies. The vocabularies are a list
of properties and can easily be accommodated in a single SQL table. Policies need to be developed for create,
update, and delete of property entries. It might also be sensible to design the properties with multilingual
labels, that is multiple labels where each label is specific to a specified language. All properties for all
vocabularies are (probably) equivalent, thus there is only a single extensive list of properties. Each
property has a unique id, and needs a definition. By and large, the property table is simply a dictionary-like
entity with labels and definitions.

### Property domain

By design properties have a domain which is made explicit by the definition. The Wikipedia clarifies this
issue since some terms only result in a disambiguation page. My "automotive" example may not be good, but the
"belt" example holds up quite well.


"Automotive" defined as "related to automobiles" includes "automobile" and therefore has a broad domain. There
might be no property "automobile". We need a policy for properties, but I'm fairly sure that a property should
be a broad as possible without being ambiguous, and narrowing is handled by ontology and multiple
properties. "Seat belts" are "webbing designed to retain an occupant or object in a seat". There is no
single property "Automotive--Seat belts", but the separate ability to create complex lists and ontological
entries allows for "Automotive" + "Seat belt", as well as "Roller coaster" + "Seat belt" and "Aircraft" +
"Seat belt".

The curatorial question: Is a seat belt simply a complex list of "belt" and "seat". I think not. The word
"belt" is (in English) applied to clothing, machinery belts, and webbing. Arguably, "seat belt" is simply
Enlish-centric, where the word "belt" has multiple different meanings some of which could just as well be rope
or chain. Perhaps the guiding curatorial policy is "avoid confusion" while attempting to be broad. Properties
which might naturally have multiple meanings should be avoided. Thus, we avoid the property "belt", which
isn't so much broadly inclusive as it is describing mutually exclusive things. The word "automotive" is broad
and inclusive. Thus properties for "belt (mechanical)", "seat belt", "belt (clothing)" make sense. The
definitions would be explicit, and adding "(clothing)" merely disambiguates the label.

In Spanish, a "fan belt" is "correa del ventilador", not "cinturon del ventilador". The Spanish word
"cinturon" is a clothing belt. This difference between languages clarifies what are properties, and what words
are simply conflations specific to English (or any language). My translations may be a bit clumsy, so I hope
the conclusion is clear:

```
cinturon == belt (clothing)
correa de transmisión == belt (mechanical)
```

Even Spanish has issues with belt. The word "correa" by itself is "strap". 


It is reasonable to have a property "airplane" as well as "aircraft"? Perhaps, and there is some burden on
archivists to choose "jet (aircraft)" + "seat belt" when speaking of a Boeing 747, and "Aircraft" + "Glider" +
"Seat belt" when refering to a glider, although I'm not convinced that glider seat belts adds useful facets of
information. 

The ontology needs to clarify sub-classes where "jet (aircraft)" and "glider" are more specific
examples of "Aircraft". In fact, using a broad category is superfluous when the narrow property is supplied.

It would be just as useful to search/analaysis to have a separate topical subject "gliders". In fact, from a
computational point of view, there is probably no difference between complex lists of topical subjects and
lists of singleton topical subjects. These are equivalent:

```
subject: Aircraft
subject: Seat belts
subject: Gliders (aircraft)
```
```
subject: Aircraft + Seat belts + Gliders (aircraft)
```

In fact, the first example is more clear and easier to analyze. If this is true, then we could apply a
universal rule: There are no complex properties, although multiple properties are allowed (and encouraged)


Properties are not themselves complex. "Automotive" is broad, and if the added specificity of "parts" is
desired then a second topic "Parts" (Parts: components of a large entity) needs to be used. Component lists
are in the domain of ontologies, not properties. There is no single property "Automotive--Parts", or
"Automotive--Paintings", and this needs to be enforced by database design, user interface and policy.

### Proper entities are not properties. 


The ontology linkage handles issues such as "Automobiles" +
"Detroit". There is no property "Detroit", although there is a CPF entity for "Detroit, MI USA". It is
possible to conflate CPF entities in the user interface to enable the construction of a topical subject
"Automobiles" + "Detroit". 

Consider what happens if (and I'm opposed to this) all CPF entities were imported into the property
table. That would be denormalization and data duplication, and will only end in tears. 

### Ontologies use the properties, but are a separate problem. 

The ontology should have publishers of Automotive + Paintings. Graphic materials ontology should include
Automotive + Paintings. The underlying properties are the same in both branches of the ontology, but the
ontological relationship is quite different because one is sub-category of "Publishers" and the other of
"Objects".

The ontology system deals with sub-classes as the example where "Jet (aircraft)" and "Glider" are more specific
examples of "Aircraft". 

### Ontology and property interact to create search facets

A search for "Aircraft" should turn up "Glider (aircraft)" even if the record in question lacks "Aircraft" as
a specific topical subject. In general, a search for a parent property should include all child properties as
specified by the ontology. Searching for the Spanish term "Ropa" will include "Cinturon" which has the English
label "Belt (clothing)". 
