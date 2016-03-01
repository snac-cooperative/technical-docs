### Database Utilities API

This describes the API of the top level database interface. 

You may be interested in the large schema diagram: [Large schema png file](Specifications/Originals/Schema Diagram.png)

There is an interactive schema web site: [Schema web site](http://shannonvm.village.virginia.edu/~twl8n/schema_spy_output/)

### Table of contents

[Multiple names, alternate names, components](#multiple-names-alternate-names-components)

[How dates are stored](#how-dates-are-stored)

[How versioning works](#how-versioning-works)

[Foreign key related records](#foreign-key-related-records)

[Vocabulary table history and rationale](#vocabulary-table-history-and-rationale)

[Vocabulary separate SQL file](#vocabulary-separate-sql-file)

[Common fields and their meaning](#common-fields-and-their-meaning)

[Identical text tables](#identical-text-tables)

[Geographic authority](#geographic-authority)


### New DBUtil and initialization

When creating a DBUtil object, you must supply the application user credentials in the form of the numberic
user id and numeric role.

DBUtil will automatically internally mint new version and constellation main_id as necessary. For batch
inserts you should create a single DBUtil object and use it for all inserts of every record in the
batch. Passing in a constellation with no ID or version and asking for an "insert" operation will result in a
new ID being minted. A new version will be minted if none exists. If a version exists, it will be used for all
operations during the life of the DBUtil object. This is intentional. While the database is not appreciably
effected by minting large numbers of versions, it does make the data harder for humans to read and debug. 

### Constellation ID discovery

This mechanism needs work. DBUtil needs a constellation ID in order to read a constellation from the
database. Presumably, some function will exist in DBUtil that takes some constraints and returns a list of
constellation ID.

In the future we will support reading old versions of a constellation. In that case, discovery must return the
constellation ID as well as version number. At that time, readConstellation() will need an update to take a
version number argument. Low level SQL code already takes version number.


### Reading and writing constellations

Constellation insert, update, or delete is controlled by the getOperation() of the constellation and each
component object. To save a constellation, call writeConstellation() with the constellation object, a string
for status, and a string for the history note. To retrieve a constellation, call readConstellation() with
constellation ID (aka main_id, $mainID).

Current implementation of readConstellation() only supports reading the most recent version. Requirements for
reading old versions are incomplete, but could be a simple as supplying readConstellation() with a version
number. 

Status is a (more or less?) controlled vocabulary. Examples include: 'published', 'needs review', 'rejected',
'being edited', 'bulk ingest'.


```
    public function writeConstellation($cObj, $status, $note)

    public function readConstellation($mainID)
```

### Utility functions

A number of functions exist in DBUtil for testing.
