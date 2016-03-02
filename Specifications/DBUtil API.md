### Database Utilities API

This describes the API of the top level database interface. 

You may be interested in the large schema diagram: [Large schema png file](Specifications/Originals/Schema Diagram.png)

There is an interactive schema web site: [Schema web site](http://shannonvm.village.virginia.edu/~twl8n/schema_spy_output/)

### Table of contents

[New DBUtil and initialization](#new-dbutil-and-initialization)


### New DBUtil and initialization

When creating a DBUtil object, you must supply the application user credentials in the form of the numberic
user id and numeric role.

DBUtil will automatically, internally mint new version and constellation main_id as necessary. For batch
inserts you should create a single DBUtil object and use it for all inserts of every record in the
batch. Passing in a constellation with no ID or version and asking for an "insert" operation will result in a
new ID being minted. In other words, for a new constellation written to the database for the first time, a new
version will be minted if none exists. 

If a version exists in the current DBUtil object, it will be used for all operations during the life of the
DBUtil object. This is intentional. While the database is not appreciably effected by minting large numbers of
versions, it does make the data harder for humans to read and debug. For example, a programmer trying to
figure out a dozen operations that take place from a single session will have an easier time if all those
operations share a version number.

### Constellation ID discovery

Methods to discover the ID or ARK of existing constellations needs work. DBUtil must have a constellation ID
in order to read a constellation from the database. Presumably, some search or browse function(s) will exist
that returns a list of constellation ID. The function will have some constraints so that a manageable list
will be return, and not all 4.5 million records.

In the future we will support reading old versions of a constellation. In that case, some function must return
a list of prior version numbers for a given constellation ID. Starting now, readConstellation() takes a version
number argument, but the only available version number is the most current. (As of Mar 2 2016.)


### Reading and writing constellations

Constellation insert, update, or delete is controlled by the getOperation() of the constellation and each
component object. To save a constellation, call writeConstellation() with the constellation object, a string
for status, and a string for the history note. To retrieve a constellation, call readConstellation() with
constellation ID (aka main_id, $mainID) and version number.

We can use readConstellation() for any version numbe. Requirements for reading old versions are
incomplete. Companion functions will retrieve ID and version, but for the most part, they version number is
only the most recent. Only allVersions() returns a full list of versions for a given ID.

See function signatures and calls below. 

$cObj is a constellation object

$note is a string

$mainID is a constellation ID, an integer

$version is a version number, an integer

$arkID is an ARK, a string.

$appUserID is a numeric user identifier

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

function loadDash($appUserID)
{
    $idVersionList = editList($appUserID);

    $constellationList = array();
    foreach ($idVersionList as $iver)
    {
        $cObj = readConstellation($iver['main_id'], $iver['version']);
        array_push($constellationList, $cObj);              
    }
}

$versionList = allVersions($mainID);


```

The SNAC dashboard will do the logical equivalent of calling editList() followed by calling
readConstellation() for each entry in $idVersionList. A list of constellations results. The dashboard web page
is populated from the big list of constellations. See function loadDash() in the example above.


publishedConstellationByARK($arkID), publishedConstellationByID($mainID)

Given an ARK/ID, return the current publicly available constellation ID and version. This will only return the
current published version.  Have equivalent functions for constellations locked-for-edit aka editing. The
"editing" will only return a list that the user has permissions to edit, and that means constellations locked
by that user. Admins can change the lock to another user, but we will not allow two users access to edit the
same constellation.

editList($appUserID)

Given a user, get a list of the constellations that are currently "checked out" to that user. In other words,
constellations that user is editing. Return a list of constellation objects.

We may want a function that returns the X most recently updated constellations, where X is a parameter.  That
would also be useful on the dashboard. Will this only return constellations locked by the user, or does this
list any constellation? Are these only constellations being edited, or are they published? Or a mix of
locked-for-edit and published?

There are several functions that only return mainID and version (but not a full constellation) The application
programmer calls readConstellation() to read the full constellation. This mix and match approach is more
flexible than a hoarde of functions which variously return: an id, lists of id, a version, lists of version, a
single constellation, or lists of constellation.

readConstellation() returns a full constellation, and the UI can be built from that as necessary. This is
better than having functions specificly only returning partial data for the UI, but full data for other
uses. The functions that return ID and version work alongside readConstellation() as part of a small tool kit.

Functions return the most recent version so that a user can continue editing where they left off. By
definition, a locked-for-edit constellation is not public.  By having a number of functions that only return a
mainID and version (or a list of mainID and version) the UI and dashboard programmers can use those in
combination with functions that return full constellations as the programmers wish.

The special allVersions() returns all versions (in order from lowest to highest) in a list for a given
ID. This is a utility function available to build user interfaces for diff of two versions, and reversion to a
previous version.


### Utility functions

A number of functions exist in DBUtil for testing.
