
### Introduction

This document is the requirements for the database user API, DBUser API. Eventually, there will be a
corresponding document in Specifications. This document covers user data requirements as related to
authentication and authorization (Au/Az).

For background about users, please see:

[User Management Requirements](Requirements/User Management.md)

### What data and features must be provided

- user must have a unique id, which we mint when creating the database record
- user must be able to authenticate (log in), which is currently handled outside DBUser by OpenID.
- user must be authorized for specific system features, which relies on role(s).

User id must be unique for all time, and thus, user ids may not be reused. We have a unique user id internal
to the system, which is the row id from table appuser. We do not currently have (or need) a textual user id
such as "mst3k", although email address must be unique to our system and serves an identifier. For example:
"mst3k@example.com" should be unique for all time, but (in theory) could be reused with a new, unique system
use rid.

Accounts need an active flag. Deleted accounts might be kept inactive, as an easy way to prevent accidentally
creating an account with the same name at some future time. 

The list of features are described in more detail in other requirements docs although we list them below with
roles. Requirements here will cover implementation of authorization, and of management of authorization.

Each user has account info: 

- id (not editable, database assigned record id)
- active (boolean)
- first name
- last name
- full name
- email which serves as a unique identifier in some special contexts
- user id which may be email, should probably be unique
- avatar
- avatar small
- avatar large
- password, stored encrypted

When logged in, a user has at least one "session", and the session has some associated data. We anticipate
that there could be multiple simultaneous sessions per user, and each session needs its own unique data.

Current session data includes:

- access token from the authentication (OAuth?)
- expiration time stamp when the session will expire

The REST API requires the same session data. REST API users will have the same Au/Az constraints

The system has roles conceptually, extensively integrated throughout. Each user is granted one or more roles.
Each user has one or more roles, as many as necessary.


### Roles

The SNAC permission system is based entirely on roles (groups). SNAC is authorizing uses to run various
functions. Ownership is a non-concept, but essentially the system ("root") owns all functions. In a situation
like SNAC where there is a single owner, the concept of "owner" has no use in authorization. That leaves SNAC
with a role-only system.

(Optional paragraph) Not having "files", there is no concept of "file has no privileges" for user, group, or
other. An example of what SNAC does not have is the shared web server situation where user grants privileges
and group removes privileges. In a shared web server, users are all assigned to a group "users". Web
accessible directories have no group permissions and are owned by group user. Lacking permissions for group
user, no one in group user has any access. Actual access then is granted only to the owner who has
read-write-execute (rwx) for that directory. Apache runs as suexec for each user, and thus each user's CGI
scripts gain access to only that user's directory via the user privs.

SNAC lacks this "subtractive" permission, and instead has only additive permissions.

In a global sense, the cumulative collection of users and roles is complicated. However, that complexity is
essential (not accidental) and on a per-user or per-role basis, things are very manageable. When managing the
system, imagine asking these typical, and manageable questions:

- List all users affiliated with California Digital Library (CDL).

- List all user affiliated with UC Irvine.

- List all users who can enroll new users.

- List all users who can publish constellations.

- List all users who may run reports for their own affiliation.

Below is an example. Anyone who can log in can get a dashboard, so there are no specific dashboard permissions
(yet). The user id "public" and the role "researcher" may not make any sense. Everyone has role "Public HRT",
so there's no reason to restrict or authorize it. This table initially had a user id "public" with role
"Public HRT", and every user also had the role "Public HRT", but that is simply redundant. When it comes to
implementation, the authorization system will use some convention for testing role. The feature "Public HRT"
can call a role test that always returns true.

The roles below are real, but do not imply any actual policy or any relation to persons living or dead.

Pulling a couple of random details from the table below, and wording them in English:

- Casey at CDL can approve NACO submissions (NACO approve)
- Jessie at NYPL is limited to assigning roles only for NYPL (affiliation-nypl and role-assign-own)


| user             | description    | roles                                                                                           |
|------------------+----------------+-------------------------------------------------------------------------------------------------|
| Marley Turner    | content expert | contribute, affiliation-none                                                                    |
| Remy Roberts     | power editor   | contribute, change-status-any, change owner, publish, affiliation-any                           |
| Casey Miyazaki   | editor CDL     | contribute, change-status-own, publish, NACO approve, affiliation-cdl, reporter-own-affiliation |
| Jessie Coughlin  | admin NYPL     | affiliation-nypl, enroll-own-affiliation reporter-own-affiliation, role-assign-own              |
| Avery Valenzuela | super admin    | enroll-any, reporter-any, role-assign-any                                                       |


Roles have a number of traits:

- Created by developers, and maintained by admins with role-granting privileges
- There is (ideally) a single privilege per role
- Roles and privileges must be coordinated with work flows and application features
- One role exists per institutional affiliation
- Every user has the implied role Public HRT
- Potentially, we can have roles for ad hoc groups (sub-institution, department, professional orgs, etc.)
- Roles can be deprecated, but re-purposing roles is inadvisable from a security standpoint
- Need explicit, on-going policy guidance
   

Every account will have the "Public HRT" role which has the same privileges as the general public, but with a
TBD set of basic privileges including: search history, certain researcher reports. An account is not necessary
to use SNAC. Members of the public are mostly identical to Researchers. The primary feature gained by having
an account is a persistent dashboard.



| Role                         | Role Description                                                            |
|------------------------------+-----------------------------------------------------------------------------|
| Public HRT                   | No account, but may use HRT public interfaces to SNAC                       |
| Researcher                   | (Same as Public HRT?) Use discovery interface and dashboard, has an account |
| Contribute                   | Create and edit constellations but cannot publish (contributor)             |
| Publish                      | Approve constellation publication (editor)                                  |
| Change status any            | Change status, removing locks, and so on, any affiliation                   |
| Change owner any             | Change constellation user, changing which user has a lock, any affiliation  |
| Change status own            | Change status, removing locks, and so on, own affiliation only              |
| Change owner own             | Change constellation user, changing user lock, own affiliation only         |
| Delete/embargo               | Delete or embargo constellations  (editor)                                  |
| Propose delete/embargo       | Propose delete or embargo                                                   |
| Ontology propose             | Propose headings in ontologies, but cannot approve headings                 |
| Ontology approve             | Approve ontology headings (editor)                                          |
| Propose NACO                 | Create(?) NACO contributions, but not push(?) to NACO                       |
| NACO approve/finalize/submit | Approve NACO contributions (editor)                                         |
| Enroll                       | Enroll new SNAC participants, create new users                              |
| Role assign own institution  | Assign new roles for own-institution users                                  |
| Role assign any institution  | Assign new roles for any institution users                                  |
| System administrator         | Maintains server hardware and operating systems                             |
| Developer                    | Writes the SNAC application, a programmer                                   |
| Web administrator            | (duplicate? historical?) May perform admin tasks via the web interface      |
| Database administrator       | Create and maintain the SQL database                                        |
| Block upload                 | Do bulk uploads of EAC-CPF, finding aids, etc.                              |
| Reporter own                 | Run own-institutional reports                                               |
| Reporter any                 | Run any report, super reporter                                              |
| Affiliation X                | Affiliated with institution X.                                              |
| Affiliation any              | Affiliated with all institutions, a super role (discuss?)                   |


More example user types, with their role(s) and description.


| User type                  | Role(s)                                             | User Description                                            |
|----------------------------+-----------------------------------------------------+-------------------------------------------------------------|
| Sysadmin                   | System administrator                                | Maintain server, backups, etc.                              |
| Database Administrator     | DBA                                                 | Schema maintenance, data dumps, etc.                        |
| Software engineer          | Developer + DBA                                     | Coding, testing, QA, release management, data loading, etc. |
| Manager                    | Enroll + Role assign + Inst. Reporter               | SNAC accounts: create, manage, assign roles, run reports    |
| Peer vetting               | Enroll                                              | Approve moderators, reviewers, content experts              |
| Moderator                  | Editor-publish                                      | Approve maintenance changes, posting those changes          |
| Reviewer/editor            | Contributor + Editor-publish                        | Maintainer privileges, interacts with moderators            |
| Content expert             | Contributor                                         | Domain expert, may have zero institutional roles            |
| Documentary editor         | Contributor                                         | (Any distinguishing roles?)                                 |
| Maintenance                | Contributor, constellation                          | May be older terminology for "contributor"                  |
| Researcher                 | Researcher                                          | Use the discovery interface and history dashboard           |
| Archival description donor | Block upload                                        | May do bulk uploads of EAC-CPF, finding aids, etc.          |
| Name authority manager     | Name authority                                      | (superseded by Editor-NACO?)                                |
| Institutional admins       | Institutional reporter                              | May run own-institutional reports                           |
| Public                     | Public                                              | No SNAC account, single session dashboard                   |
| Contributor                | Contribute + Ontology propose                       | Creates/edit constellations, propose ontology headings      |
| Author                     | Contribute + Publish + Propose Del/Emb+Propose NACO | A contributor, with additional privileges                   |
| Editor                     | Contribute + Publish + Delete/embargo + NACO        | Review constellations, approve and publish                  |
| Author-NACO                | Create provisional NACO                             | Creates NACO entries, sends to editor for submission        |
| Administrator              | Author + editor + enroll + assign                   | Everything, only own institution                            |
| Administrator-super        | Administrator + any institution                     | Admin plus assign roles for any user of any institution     |


### API 

Most of the API depends on a User object. That object must at least contain an email address. When creating,
saving, or updating a user, all available fields are written to the database except id (aka appUserID). The ID
is generated from the database.

createUser() creates a new id, overwriting any existing id.

saveUser() requires a valid id in order to update. The ID is not changed.

readUser() requires a valid id. The ID is not changed.

A user object has only a single session. There may be several active sessions per account. We assume that each
HTTP request will include unique session, keeping all concurrent sessions separate. 

Role management works.

---

`checkSessionActive(snac\data\User $user)` 

Check that a session is active (not expired) for $user. Time is assumed to be "now". Return a $user with valid
appUserID upon success, or false on failure. Adds the user to the db if the user does not already
exist. Creates a session if a session does not already exist.

If a session exists, that session expires is checked against now() as UTC. If the session has expired, then
the session is deleted, and the $user token is cleared by setting 'access_token' to '' and expires to zero.

The user is created based on email being unique. Any existing userID inside $user is ignored, then
overwritten.

The new user is not assigned a default role, but there is a function to add a default role if we need that.

---

`createUser(snac\data\User $user)`

Add a user record. Minimal requirements is a unique email. Return the new User object. The current
implementation has no concept of failure. Adds a default role if we have one. We do not currently have a
default role.


---

`saveUser(snac\data\User $user)`

Update a user record. Overwrite everthing else with values from the User object. Please contact the developers
before using this function.

---

`findUserID($email)`

Get the user id if you only know the email address. We assume the email addresses are unique. Returns integer appUserID or false.

---


`readUser($userID)`

Return a User object for the user id or email. Return false on failure. Fills in the role list in the User object.

---

`readUserByEmail($email)`

Return a User object based on email address. Return false on failure.

---

`disableUser(snac\data\User $user)`

Disable log in to this account. Update table appuser.active to false. Return true on success.

Proably has not been tested. The concept of inactive accounts is not well defined.

---

`roleList()`

List all system roles as a list of Role objects.

---

`addUserRole(snac\data\User $user, $newRole)` 

Add a role to the User via table appuser_role_link. Return true on success. $newRole is a Role object.

---

`addDefaultRole($user)`

Add default roles to $user, if any default roles exist. Currently does nothing. If we had default roles, it
would change $user in place. Would return true on success, false otherwise.

---

`listUserRole($user)`

List all the roles a user currently has as an array of Role objects.

----

`checkRoleByLabel($user, $label)`

Check for role by label. Returns the role if user has it, else false;

----

`removeUserRole($user, $role)`

Remove a role from the User by deleting the role link from table appuser_role_link.

----

`createRole($label, $description)` 

Create a new role with $label and $description. Return the role on success. Currently, it has no concept of
failure.

---

`checkPassword(snac\data\User $user, $passwd)` 

Check $passwd matches the password stored for snac\data\User. Return true on success.

---

`addSession(snac\data\User $user)` 

Add a new session token $user->getToken()['access_token'] with expiration time $user->getToken()['expires']
for $user. Update the session if $accessToken exists.

---

`sessionExists($user)`

Find out if a session exists and if that session is associated with $user, and has not expired.

---

`removeSession($user)`

 Remove a session. Assume that tokens are unique, which is important. Delete all sessions with the token
 $user->getToken()['access_token'] matter what user has that token.

----

`clearAllSessions(snac\data\User $user)`

Delete all session records for $user.

----
