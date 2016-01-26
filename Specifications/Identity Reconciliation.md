# Identity Reconciliation Engine

![Engine Diagram](http://gitlab.iath.virginia.edu/snac/Documentation/raw/ir/Specifications/Originals/IR_Engine.svg)

The identity reconciliation engine will operate on SNAC Identity Constellations, as defined in [this specification](/Specifications/Server API/Constellation.md) and depicted in [this figure](/Specifications/Originals/IC_Overview.pdf).  The engine will take a partial constellation as input (called the _query constellation_) and find appropriate candidate matches from the SNAC database (called _candidate constellations_).

The engine is architected as an independent multi-stage process, with a coalescing weighting function to produce the final results.  Specifically, the engine will consist of multiple independent stages, which may run in parallel, to produce independent lists of resulting candidate constellations, together with a numeric score for the stage, from the query constellation.  The coalescer will then combine each set of resulting candidate constellations into one list of candidates by combining the scores for each constellation across the executed stages.

Within the sorted list of resulting candidate constellations, each will report its score among the stages.  The coalescer's weighting function may be modified, either by hand or programmatically, to enhance the resulting set of candidate constellations.  A human must verify and accept that the top candidate(s) is the same as the query constellation before any merging may take place. **Therefore, this engine does not and can not guarantee 100% accuracy in its matching.**

## Implementation Notes

* The identity reconciliation engine may be used as a search engine within SNAC by limiting the stages performed and providing a query constellation with only the parts interested in finding.  For example, to search for a name, a query constellation with that name may be passed to the reconciliation engine.  The process would be the same to search for an entity that is associated with a given place, occupation, or exist dates.
* Based on the highly parallelizable nature of the independent stages followed by the coalescer, the identity reconciliation engine maybe implemented (or enhanced) using the [Map-Reduce Framework](http://static.googleusercontent.com/media/research.google.com/en//archive/mapreduce-osdi04.pdf) on a large cluster.

# Stage Designs

The following stages are proposed for the Identity Reconciliation Engine:

* **Candidate Constellation producing stages** *These stages produce lists of candidate constellations.*
  * Elastic Search Name Entry (Heading) : *Search the entire name entry from the query constellation against all constructed name entry strings from SNAC identity constellations.  Return the top N constellations based on Elastic Search's search algorithm with Elastic Search native scores as the stage score for each candidate constellation.*
  * Elastic Search Name (Name-only) : *Search the name portion of the name entry from the query constellation against all constructed name-only strings from SNAC identity constellations.  Return the top N constellations based on Elastic Search's search algorithm with Elastic Search native scores as the stage score for each candidate constellation.*
  * Elastic Search Surname : *Search the surname component from the query constellation against all surnames from SNAC identity constellations.  Return the top N constellations based on Elastic Search's search algorithm with Elastic Search native scores as the stage score for each candidate constellation.*
  * Elastic Search Forenames : *Search the forenames component from the query constellation against all forenames from SNAC identity constellations.  Return the top N constellations based on Elastic Search's search algorithm with Elastic Search native scores as the stage score for each candidate constellation.*
  * Exist Dates : *Search the exist dates from the query constellation against all identity constellations in SNAC.  Return all constellations that contain the exact exist dates as the query constellation.*
  * Fuzzy Exist Dates : *Search the exist dates from the query constellation against all identity constellations in SNAC.  Return all constellations that contain exist dates within a range of X from those in the query.  Stage score is defined as the distance from the query's dates, lower is better.*
  * Occupation : *Search the list of occupations from the query constellation against all identity constellations in SNAC.  Return all constellations that match all occupations in the query constellation.  (The candidates must have the entire list of occupations from the query as a subset of their occupation list.)*
  * Place : *Search the list of places from the query constellation against all identity constellations in SNAC.  Return all constellations that match all places in the query constellation. (The candidates' list of places must be a superset of the query constellation's places.)*
* **Candidate Constellation list modifying stages** *These stages take lists of candidate constellations and modify or replace the scores for the input results.*
  * Name Entry Length : *Compute the difference in length between the candidate constellation's constructed name entry string and the query constellation's constructed name entry string.  Replace the original stage score with the log of the difference. Lower scores are better.*
  * SNAC Degree Sort : *Replace the original stage score with the number of constellation relations (constellation out-degree) in the candidate constellation.  Constellations that are more connected with other SNAC identity constellations will get better scores.*
  * SNAC Resource Count Sort : *Replace the original stage score with the number of resource relations (resource out-degree) in the candidate constellation.  Constellations with higher resource relations will get better scores.*
* **Multi-Stage**  *This stage allows the execution engine to run multiple stages in sequence, feeding the results of one stage as the input to the next.  It results in one final list of candidate constellations from all independent stages it ran.*
