### Database Utilities API

This describes the API of the top level database interface. 

You may be interested in the large schema diagram: [Large schema png file](Specifications/Originals/Schema Diagram.png)

There is an interactive schema web site: [Schema web site](http://shannonvm.village.virginia.edu/~twl8n/schema_spy_output/)

### Table of contents

[New DBUtil and initialization](#new-dbutil-and-initialization)


### Notes on undelete

Undelete is not currently implemented. There is a function clearDeleted(), but it is almost certainly is not
quite right, and we need agreement on how to call it.

As of Mar 10 2016 we have functioning object operation via setOperation() and getOperation() with valid
operations insert, update, delete. In this new world, undelete should be yet another operation, and should be
integrated into the existing operation code.

There are two problems:

1) delete and undelete are different for constellation vs component

2) it would be wrong for undelete to incorrectly undelete a component that was deleted before a constellation
was deleted

Issue (1) is typical for delete, and we need undelete code that is symmetrical with the delete operations.

Issue (2) is interesting. If a constellation is being undeleted, we should not also apply the undelete
operation to each component. Delete of a component is granular and undelete should be granular in the same
sense. That is: delete of a component happens independently of all other operations.

Likewise, constellation level delete should not allow component operations, thus it is probably best that
constellation delete not traverse the components. Likewise, constellation level undelete should only undelete
the constellation, and not traverse the components doing any operation, especially not component undelete.

### New DBUtil and initialization

When creating a DBUtil object, DBUtil currently calls $this->getAppUserInfo('system') to determine the user id
and role id. This function simply reads the database for user 'system'. When we have an authentiation and
authorization system, application code will supply the user information to DBUtil.

DBUtil will automatically, internally mint new version and constellation main_id as necessary. For constellation
inserts you should create a single DBUtil object and use it for all inserts of every record in the
batch. Passing in a constellation with no ID or version and asking for an "insert" operation will result in a
new ID being minted. In other words, for a new constellation written to the database for the first time, a new
version will be minted if none exists. 

A new version is minted for each constellation written. While we could use the same version during the life of
an instance of DBUtil, it was decided to mint fresh versions. Programmer reading the database records need to
be aware of this, and always check version_history.main_id and version_history.id (aka version).


### Constellation ID discovery

Methods to discover the ID or ARK of existing constellations need work, and are evolving as use cases
emerge. DBUtil must have a constellation ID in order to read a constellation from the database. Presumably,
some search or browse function(s) exist that return a list of constellation ID. With some constraints a
manageable length list will be return, and not all 4.5 million records.

W support reading old versions of a constellation via allVersion() which returns a list of prior version
numbers for a given constellation ID. readConstellation() takes a version number argument.



### Reading and writing constellations

Constellation insert, update, or delete is controlled by the getOperation() of the constellation and each
component object. To save a constellation, call writeConstellation() with the constellation object, and a
string for the history note. The version history status is detemined via heuristic in the code, and the status
can be set via writeConstellationStatus(). To retrieve a constellation, call readConstellation() with
constellation ID (aka main_id, $mainID) and version number.

We can use readConstellation() for any version numbe. Requirements for reading old versions are
incomplete. Companion functions will retrieve ID and version, but for the most part, they version number is
only the most recent. Only allVersion() returns a full list of versions for a given ID.

See function signatures and example calls below. 

$cObj constellation object

$note string

$mainID constellation ID, an integer

$version version number, an integer

$arkID ARK, a string.

$appUserID numeric user identifier

$status constellation status from a controlled vocabulary (more or less?). Examples include: 'published', 'needs review', 'rejected',
'locked editing', 'bulk ingest'.


```
// public function, write a constellation to the db, where "write" is taken broadly
$cObj = writeConstellation($cObj, $note)

// public function, return a constellation read from the db
$cObj = readConstellation($mainID, $version)
<
// public function, return the published constellation by ARK
$cObj = readPublishedConstellationByARK($arkID);

// public function, return the published constellation by ID
$cObj = readPublishedConstellationByID($mainID);

// public function, return the list of constellations I'm editing
// uses $this->appUserID in the DBUtil object
$cObjList = listConstellationsLockedToUser();

$status = 'locked editing';
$newVersion = writeConstellationStatus($mainID, $status, 'optional arg, version note');

$status = readConstellationStatus($mainID);
$status = readConstellationStatus($mainID, $version);


// private function, return id and version for each constellation I'm editing, private?
// Relies on the $appUserID property in DBUtil, and thus does not take an argument.
$idVersionList = editList();

$idVersionList = array(
               array('main_id' => 1, 'version' => 2),
               array('main_id' => 3, 'version' => 2),
               array('main_id' => 4, 'version' => 5));

// This is the actual function to return all constellations with status 'locked editing'. 
// Eventually this will support other status args.
function listConstellationsLockedToUser($status=null)
{
    $infoList = $this->editList();
    if ($infoList)
    {
        $constellationList = array();
        foreach ($infoList as $idVer)
        {
            $cObj = $this->readConstellation($idVer['main_id'], $idVer['version']);
            array_push($constellationList, $cObj);              
        }
        return $constellationList;
    }
    return false;
}

$versionList = allVersion($mainID);


```

`public function readConstellation($mainID, $version)`

Returns a full constellation, or false upon failure. The UI can be built from the full constellation as
necessary. This is better than having functions specifically only returning partial data for the UI, but full
data for other uses. The functions that return ID and version work alongside readConstellation() as part of a
small tool kit.

`public function writeConstellation($argObj, $note)`

Writes a constellation to the database, returning the same constellation with ID and versions as
appropriate. It will probably crash if the first argument is not a constellation. The constellation must pass
validation tests which writeConstellation() calls before doing any other work.

`public function readPublishedConstellationByARK($arkID)`
`public function readPublishedConstellationByID($mainID)`

Given an ARK/ID, return the current publicly available constellation ID and version. This will only return the
current published version. Returns a single constellation or false upon failure.

Functions return the most recent version so that a user can continue editing where they left off. By
definition, a locked-for-edit constellation is not public. 

`public function listConstellationsLockedToUser($status=null)`

The SNAC dashboard will call listConstellationsLockedToUser() or the logical equivalent, which returns a list
of constellations. The dashboard web page is populated from this list of constellations. The user who has the
lock, or admins can change the lock to another user, but we will not allow two users edit the same
constellation.

We may want a function that returns the X most recently updated constellations, where X is a parameter.  That
would also be useful on the dashboard. We are already planning for listConstellationsLockedToUser() to take a
status parameter, which will be usefor for the dashboard.

`public function readConstellationStatus($mainID, $version=null)`

Read the status for constellation ID $mainID, with optional $version arg. If no version then it defaults to
the most recent version, deleted or not (logically) since 'deleted' is one of the status values. Returns false
on failure.

`public function writeConstellationStatus($mainID, $status, $note="")`

Modify the status of a constellation. Returns the new version number for the status update, or false on
failure. Writing a constellation status of deleted will "functionally delete" that constellation.


```
    private $statusList = array('published' => 1,
                                'needs review' => 1,
                                'rejected' => 1,
                                'locked editing' => 1,
                                'bulk ingest' => 1,
                                'deleted' =>1,
                                'currently editing' => 1);
```

Valid status values are stored in a private var in DBUtil.php.

`public function allVersion($mainID)`

Returns all versions (in order, from lowest to highest) in a list for a given ID. This is a utility function
available to build user interfaces for diff of two versions, or reversion to a previous version.

There are several functions that only return mainID and version (but not a full constellation). After getting
mainID and version, the application programmer calls readConstellation() to read the full constellation. This
mix and match approach is more flexible than a hoarde of functions which variously return: an id, lists of id,
a version, lists of version, a single constellation, or lists of constellation.

### Private functions

Currently, functions which return lists of mainID and version are private to DBUtil. These granular, low-ish
level functions may be useful for application programmers, but since the use case is currently unclear, we
have made these functions private.

Many other functions in DBUtil have no use case outside the class and are private.

`private function editList()`

Return a list of the mainID and version that are currently "locked editing" for the user.  DBUtil determines
who is the current user. Long term, the code calling DBUtil will probably feed the user id to DBUtil.



### Utility functions

A number of functions exist in DBUtil for testing.
