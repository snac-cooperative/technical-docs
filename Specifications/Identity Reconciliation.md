# Identity Reconciliation Engine

![Engine Diagram](http://gitlab.iath.virginia.edu/snac/Documentation/raw/ir/Specifications/Originals/IR_Engine.svg)

The identity reconciliation engine will operate on SNAC Identity Constellations, as defined in [this specification](/Specifications/Server API/Constellation.md).  The engine will take a partial constellation as input (called the _query constellation_) and find appropriate candidate matches from the SNAC database (called _candidate constellations_).

The engine is architected as an independent multi-stage process, with a coalescing weighting function to produce the final results.  Specifically, the engine will consist of multiple independent stages, which may run in parallel, to produce independent lists of resulting candidate constellations, together with a numeric score for the stage, from the query constellation.  The coalescer will then combine each set of resulting candidate constellations into one list of candidates by combining the scores for each constellation across the executed stages.

Within the sorted list of resulting candidate constellations, each will report its score among the stages.  The coalescer's weighting function may be modified, either by hand or programmatically, to enhance the resulting set of candidate constellations.  A human must verify and accept that the top candidate(s) is the same as the query constellation before any merging may take place. **Therefore, this engine does not and can not guarantee 100% accuracy in its matching.**

## Implementation Notes

* The identity reconciliation engine may be used as a search engine within SNAC by limiting the stages performed and providing a query constellation with only the parts interested in finding.  For example, to search for a name, a query constellation with that name may be passed to the reconciliation engine.  The process would be the same to search for an entity that is associated with a given place, occupation, or exist dates.
* Based on the highly parallelizable nature of the independent stages followed by the coalescer, the identity reconciliation engine maybe implemented (or enhanced) using the [Map-Reduce Framework](http://static.googleusercontent.com/media/research.google.com/en//archive/mapreduce-osdi04.pdf) on a large cluster.