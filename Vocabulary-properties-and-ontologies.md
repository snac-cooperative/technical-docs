
### Introduction


There are two related yet distinct issues: controlled vocabularies and ontologies. The vocabularies are a list
of properties and can easily be accommodated in a single SQL table. Policies need to be developed for create,
update, and delete of property entries. It might also be sensible to design the properties with multilingual
labels, that is multiple labels where each label is specific to a specified language. All properties for all
vocabularies are (probably) equivalent, thus there is only a single extensive list of properties. Each
property has a unique id, and needs a definition. By and large, the property table is simply a dictionary-like
entity with labels and definitions.

### Property domain


By design, each property has a domain which is made explicit by the definition. The Wikipedia clarifies this
issue since some terms only result in a disambiguation page. My "automotive" example may not be good, but the
"belt" example holds up quite well.


The properties "automotive" and "automobile" are closely related, and in common use it is reasonable to use
them interchangeably in some many situations. "automotive" + "seat belt" and "automobile" + "seat belt" differ
in subtle ways. The difference is subtle enough to guarantee that some database records will be code on way,
and some another way. If there are any errors where one or the other term was used, then due to amibiguity, any
reasoning about these closely related terms is likely be erroneous.

"seat belts" are "webbing designed to retain an occupant or object in a seat". There is no single property
"automotive--seat belts", but the separate ability to create subject lists allows for "automotive" + "seat
belt", as well as "roller coaster" + "seat belt" and "aircraft" + "seat belt".

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
archivists to choose "jet (aircraft)" + "seat belt" when speaking of a Boeing 747, and "aircraft" + "glider" +
"seat belt" when refering to a glider, although I'm not convinced that glider seat belts adds useful facets of
information. 

The ontology needs to clarify sub-classes where "jet (aircraft)" and "glider" are more specific
examples of "aircraft". In fact, using a broad category is superfluous when the narrow property is supplied.

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


Properties are not themselves complex. "automotive" is broad, and if the added specificity of "parts" is
desired then a second topic "parts" (Parts: components of a large entity) needs to be used. Component lists
are in the domain of ontologies, not properties. There is no single property "automotive--parts", or
"automotive--paintings", and this needs to be enforced by database design, user interface and policy.

### Proper entities are not properties

The ontology linkage handles issues such as "automobiles" + "detroit". There is no property "detroit",
although there is a CPF entity for "Detroit, MI USA". It is possible to conflate CPF entities in the user
interface to enable the construction of a topical subject "automobiles" + "detroit", although that is a bad
idea. The data should be as well-constructed as possible. When searching for subject and place, it is
reasonable to either parse the place name, or have the user explicitly choose a place name.

Consider what happens if (and I'm opposed to this) all CPF entities were imported into the property
table. That would be denormalization and data duplication, and would only end in tears. 


### Use Markov models instead of an ontology

Ontologies are difficult to create, and there is little agreement about them, both in structure and
content. There are several to choose from, the the properties they use are (speaking frankly) a huge
mess. Linking each aspect of an entity record's properties to the ontology is an onerous task, and fraught
with error largely because the linking is often a judgement call.

A technology exists that is easy to implement, powerful, and tractable in real life.

We can create a Markov matrices of the property terms. Multiplying Markov matrices causes them to converge
which reveals property relatedness as exists in the data. The effect is quite powerful and obviates the need
for a hand-created ontology. Missing relations (known to exist, but not discovered by the Markov convergence
because no records actually contain the desired relation) are easily rectified by either of two methods. The
first would be to add the correct relations to existing records. The second works by creating non-public
special records containing related terms and making the special records available to the Markov modeling
process. The whole Markov solution is only 2 or 3 pages of code, so we can write it and evaluate the
effectiveness.

See: Everything is miscellaneous.

https://en.wikipedia.org/wiki/Everything_Is_Miscellaneous

http://www.youtube.com/watch?v=WHeta_YZ0oE

http://www.youtube.com/watch?v=x3wOhXsjPYM


### Ontology uses property, but is a separate problem

The alternative to the Markov relation discovery is an ontology that relates properties both in relatedness,
and as a hierarchy from broad to narrow. As far as I can tell, such a network does not yet exist, and it would
be time consuming to create since it has to be done by hand, by humans. There are existing ontologies, but to
use them we would be forced to use their property lists, and (sadly) the property lists I've seen are both
incomplete and poorly constructed.

When describing two very different things, some properties would be the same. For example the topical subject
of a publisher, and of a work of art. The publisher creates books of automobiles, especially cars which have
been artistically painted. The work of art is a painting of an automobile. 

```
Publisher subject:Automobiles subject:Painting (fine art)
Painting subject:Automobiles subject:Painting (fine art)
```

The underlying properties are the same in both branches of the ontology, but the ontological relationship is
quite different because one is a corporate body, and the other is an object. It is not the domain of a
property to know how it is applied to a database record. Also, the larger context of what is being described
changes how the description is perceived. In any case, the use of properties is sufficient for search and
discovery. Data beyond property is necessary to make assertions about the records.

The example above is limited to properties as topical subject. It seems reasonable to apply additional
properties "typeOf", still using the same (original, large) list of properties. Types of "publisher (corporateBody)" and
"painting (object)" seem obvious. Applying both property and type pairs will explicitly categorize any
database record, even without using an ontology. However, it is unclear how this somewhat loosely coupled
description will impact being able to reason about database records.

It also seems resonable to constrain some properties to be used only as certain types. A gender property is
nonsense in the context of a topical subject. On the other hand, "painting (fine art)" could be both a subject
and a typeOf. The conservative approach is to limit each property to a single type.

### Ontology and property interact to create search facets

A search for "aircraft" should turn up "glider (aircraft)" even if the record in question lacks "aircraft" as
a specific topical subject. In general, a search for a parent property should include all child properties as
specified by the ontology. Searching for the Spanish term "ropa" will include "cinturon" which has the English
label "belt (clothing)". 
