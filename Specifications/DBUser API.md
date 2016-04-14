
### Introduction

Most of the API depends on a User object which contains a list of Role object. Before being written to the
database, a User must at least contain an username which we are defaulting to the email address string. After
being written to the database, a user must contain the userid (aka appUserID, aka id) and username. When
creating, saving, or updating a user, all available fields are written to the database except id (aka
appUserID) and username. The ID is generated from the database, and the username was supplied when the user
was first created. We assume that neither will ever change.

If there is an ID present in a User prior to the user being created, that ID will be overwritten. This might
occur where fields from a User come from an outside system with its own concept of user id.

createUser() creates a new id, overwriting any existing id in the User object

saveUser() requires a valid id in order to update. The ID is not changed.

readUser() requires a valid id. The ID is not changed.

A User object has only a single session. There may be several active sessions per account. We assume that each
HTTP request will include unique session, thus keeping all concurrent sessions separate. 


### API 

---

`checkSessionActive($user)` 

Don't use this. Instead use granualar functions such as: sessionExists(), sessionActive(), addSession(),
sessionExtend(), readUser(), and createUser().

This does multiple actions including check that a session is active (not expired) for $user. Time is assumed
to be "now" UTC in seconds since the epoch. Return a $user with valid appUserID upon success, or false on
failure. Adds the user to the db if the user does not already exist. Creates a session with included token and
expires if a session does not already exist.

If a session exists, that session expiration time is checked against now() as UTC. If the session has expired,
then the session is deleted, and the User token is cleared by setting 'access_token' to '' and expires to
zero.

The User is created based on email being unique. Any existing userID inside $user is ignored, then
overwritten with the database supplied value which is an integer.

The new user is not assigned a default role, but there is a function to add a default role if we need that.

---

`createUser($user)`

Add a user record. Minimal requirements is a unique email. Return the new User object. The current
implementation has no concept of failure. Adds a default role if we have one. We do not currently have a
default role.


---

`saveUser($user)`

Update a user record. Overwrite everthing except user id with values from the User object. Please contact the developers
before using this function because there is no sanity checking in the function. This function is currently
unused and while it seems reasonable to have this function, it may be removed.

---

`findUserID($email)`

Get the user id if you only know the email address. We assume the email addresses are unique. Returns integer appUserID or false.

This is a nice low level function, but you probably want the high level readUserByEmail() which returns a User object.

---


`readUser($userID)`

Return a User object for the user id or email. Return false on failure. Fills in the role list in the User
object. This is a nice low level function, but you probably want readUserByEmail() which returns a User object.

---

`readUserByEmail($email)`

Return a User object based on email address. Return false on failure.

---

`disableUser($user)`

Disable this account. Definition of "disable" is evolving.  Update table appuser.active to false. Return true on success.

Proably has not been tested. The concept of inactive accounts is not well defined.

---

`roleList()`

List all system roles as a list of Role objects.

---

`addUserRole($user, $newRole)` 

Add a role to the User via table appuser_role_link. Return true on success. $newRole is a Role object, and you
probably want to call roleList() to get a list of available roles.

---

`addDefaultRole($user)`

Add default roles to $user, if any default roles exist. Currently does nothing. If we had default roles, it
would change $user in place. Would return true on success, false otherwise.

---

`listUserRole($user)`

List all the roles a user currently has as an array of Role objects.

----

`checkRoleByLabel($user, $label)`

Check a User object for role by label. Returns the role if user has it, else false;

----

`removeUserRole($user, $role)`

Remove a role from the User by deleting the role link from table appuser_role_link. Returns true.

The User object is modified in place, getting the updated list of roles.

----

`createRole($label, $description)` 

Create a new role with $label and $description. Return the role on success.

---

`checkPassword($user, $passwd)` 

Check $passwd matches the password stored for User. Return true on success.

Not used with OpenID, and not tested, but it is very simple. 

---

`addSession($user)` 

Add a new session to the database for the token $user->getToken()['access_token'] with expiration time
$user->getToken()['expires'] for $user. Update the session if $accessToken exists.

---

`sessionExists($user)`

Find out if a session exists for this User. Expiration time is ignored, so this is only answers "Does the session exist?"

---

`removeSession($user)`

 Remove a session. Assume that tokens are unique, which is important. Delete all sessions with the token
 $user->getToken()['access_token'] matter what user has that token.

----

`clearAllSessions($user)`

Delete all session records for $user.

---

`writePassword($user, $passwd)`

Write a hashed password to the database for User. The password itself it not part of the User object, so we
use some additional functions to manage passwords. This is not used during OpenID authentication.

----

`getSQL()`

Used only for testing. Returns the SQL object so that sql functions can be called directly.

---
