### Database Utilities API

This describes the API of the top level database interface. 

You may be interested in the large schema diagram: [Large schema png file](Specifications/Originals/Schema Diagram.png)

There is an interactive schema web site: [Schema web site](http://shannonvm.village.virginia.edu/~twl8n/schema_spy_output/)

### Table of contents

[New DBUtil and initialization](#new-dbutil-and-initialization)


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

public function readConstellation($mainID, $version)

list($mainID, $version) = publishedConstellationByARK($arkID);

list($mainID, $version) = publishedConstellationByID($mainID);

list($mainID, $version) = editingConstellationByARK($arkID);

list($mainID, $version) = editingConstellationByID($mainID);

$idVersionList = editList($appUserID);

$idVersionList = array(
               array('main_id' => 1, 'version' => 2),
               array('main_id' => 3, 'version' => 2),
               array('main_id' => 4, 'version' => 5));

$constellationList = array();
foreach ($idVersionList as $iver)
{
    $cObj = readConstellation($iver['main_id'], $iver['version']);
    array_push($constellationList, $cObj);
}

$versionList = allVersions($mainID);

// I don't think we need this if we universally return id and version
public function readConstellation($mainID) 


```

Given an ARK/ID, return the current publicly available constellation ID and version. This will only return the
current published version.  Have equivalent functions for constellations locked-for-edit aka editing. The
"editing" will only return a list that the user has permissions to edit, and that means constellations locked
by that user. Admins can change the lock to another user, but we will not allow two users access to edit the
same constellation.

Given a user, get a list of the constellations that are currently "checked out" to that user. In other words,
constellations that user is editing. Return a list of constellation objects.

We may want a function that returns the X most recently updated constellations, where X is a parameter.  That
would also be useful on the dashboard. Will this only return constellations locked by the user, or does this
list any constellation? Are these only constellations being edited, or are they published? Or a mix of
locked-for-edit and published?

Most functions return mainID and version. The application programmer calls readConstellation() to read the
full constellation. This mix and match approach is more flexible than a hoarde of functions some of which
return an id, lists of id, a version, lists of version, a single constellation, or lists of constellation.

Should functions return just enough data for the UI, or should the return a list of full constellations?
That's a tricky question.  I would say let's just return full constellations.  They might not all get used by
us, but I can see that as a pretty cool REST API feature for others.  I'm thinking the WebUI will probably use
a name and the ID.  But remember, that function needs to get the current version for that user, which might
not be public yet.

Functions return the most recent version so that a user can continue editing where they left off. By
definition, a locked-for-edit constellation is not public.  By having a number of functions that only return a
mainID and version (or a list of mainID and version) the UI and dashboard programmers can use those in
combination with functions that return full constellations as the programmers wish.

The special allVersions() returns all versions (in order from lowest to highest) in a list for a given
ID. This is a utility function available to build diff and reversion user interfaces.


### Utility functions

A number of functions exist in DBUtil for testing.
