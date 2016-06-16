
### Introduction

This document is the requirements for the database user API, DBUser API. Eventually, there will be a
corresponding document in Specifications. This document covers user data requirements as related to
authentication and authorization (Au/Az) as well as user account information.

For additional background about users, please see:

[User Management Requirements](Requirements/User Management.md)

### What data and features must be provided

- User must have a unique id, which we mint when creating the user database record in the database. The id is
  supplied by the database as a record id, which is a unique integer. User must have a unique username, which
  is some string; we currently use an email address for username.

- Each user belongs to one or more roles, and roles have privileges, therefore users have privileges.

- Support for data necessary to authenticate (log in), which is currently handled outside DBUser by
  OpenID. This data includes an access token and expires time.

- Support for user authorization for specific system features. This requirement relies on privilege(s).

- Create user requires a username which we default to the email address string. We also expect that the email
  field will contain an email address. There was some discussion of multiple email addresses per user. That
  has not been implemented.

- Update user, all fields except the ID and username; ID must never be modified. Changing username will have
  unpredictable consequences.

- Delete or inactivate user

- Add privilege to user via adding a role

- Remove privilege from user via removing a role

- Multiple privileges per user via multiple roles

- Create session

- Extend session expiration time

- Add session to user

- Remove session from user

- Remove all sessions from user

- List all users

Beyond user data, the "authorization system" requires features of the DBUser API:

- Create, modify, list and generally manage roles via web admin

- Create, modify, list and generally manage privileges in coordination with hard coded, symbolic references to
  privileges, almost certainly only managed by developers.

User id must be unique for all time, and thus, user ids may not be reused. We have a numerical unique user id
internal to the system, which is the row id from table appuser. We added a textual username which
traditionally would be something such as "mst3k" or an email address. We default to using an email address as
username. Currently, email address in the email address field is not necessarily across our system, and
therefore is not a reliable identifier. 

Accounts have an active flag. Deleted accounts might be kept in an inactive state in the database. It is
unclear what information in an inactive account is important historically. An inactive account would prevent
using that account's username due to username being unique in table appuser. User ID values cannot be
accidentally reused because the database creates them from a Postgres sequence.

Some features are described in more detail in other requirements docs although we list them below with
privileges. Requirements here will cover data necessary to support implementation of authorization, and support
management of authorization.

Each user has account info: 

- id (not editable, database assigned integer record id)
- username, unique string, we currently use an email address string
- affiliation, SNAC ID of the constellation of the affiliated institution
- active (boolean)
- first name
- last name
- full name
- email, not unique
- work email
- work phone
- avatar
- avatar small
- avatar large
- preferred descriptive rules
- password, stored hashed

When logged in, a user has at least one "session", and the session has some associated data. We anticipate
that there could be multiple simultaneous sessions per user, and each session needs its own unique data.

Current session data includes:

- access token from the authentication (OAuth?)
- expiration time stamp when the session will expire

The REST API requires the same session data. REST API users will have the same Au/Az constraints.

The system has privileges used to implement authorization in the code, extensively integrated throughout the
DBUser API. Each user belongs to one or more roles, and roles have privileges, therefore users have
privileges.



### Privileges

The SNAC permission system is based entirely on privileges (permissions). Groups of privileges are
roles. Privileges authorize users to run various functions. Ownership of a constellation is a non-concept, and
essentially the system owns all functions. In a situation like SNAC where there is a single owner, the Linux
concept of "owner" has no utility in authorization. The Linux concept of "other" doesn't apply to SNAC as
"other" is only allowed to perform read-only actions such as search. That leaves SNAC needing only a privilege
system analogous to Linux "group". Note that SNAC role would be analogous to a group of Linux groups.

Note that affiliation is a property of each user account, and there is a single affiliation per user.

(Optional paragraph) Not having "files", there is no concept of "file has no privileges" for user, group, or
other. An example of what SNAC does not have is the shared web server situation where user grants privileges
and group removes privileges. In a shared web server, users are all assigned to a group "users". Web
accessible directories have no group permissions and are owned by group user. Lacking permissions for group
user, no one in group user has any access. Actual access then is granted only to the owner who has
read-write-execute (rwx) for that directory. Apache runs as suexec for each user, and thus each user's CGI
scripts gain access to only that user's directory via the user privileges.

SNAC lacks the Linux "subtractive" permission which is sometimes used with groups permissions. Instead SNAC
has only additive permissions.

In a global sense, the cumulative collection of users and privileges is complicated. However, that complexity is
essential (not accidental) and on a per-user or per-privilege basis, things are very manageable. When managing the
system, imagine asking these typical, and manageable questions:

- List all users affiliated with my affilation

- List all users who can enroll new users for any affiliation

- List all users who can only enroll new users only for own-affiliation

- List all users who can publish constellations.

- List all users who may run reports for their own affiliation.

Below is an example. Anyone who can log in can get a dashboard, so there are no specific dashboard permissions
(yet). The user id "public" and the privilege "research" may not make any sense. Conceptually, everyone has privilege
"Public HRT", so there's no reason to restrict or authorize it. In actual implementation the "what is public"
divide is simply any published record or read-only action on published constellations. The table below
initially had a user id "public" with privilege "Public HRT", and every user also had the privilege "Public HRT", but
that was simply redundant.

The privileges below are real, but do not imply any actual policy or any relation to persons living or dead.

Note that affiliation is a property of each user account, and there is a single affiliation per user.

Pulling a couple of random details from the table below, and wording them in English:

- Casey at CDL can approve NACO submissions (NACO approve), modify constellations, change constellation
  status, change owner/lock of constellations, and publish constellations.
- Jessie at NYPL create new users affiliated with NYPL, run reports with NYPL constraints, and can assign
  privileges only for NYPL (privilege-assign-own).


| user             | description             | privileges                                                                   |
|------------------+-------------------------+------------------------------------------------------------------------------|
| Marley Turner    | content expert          | contribute,                                                                  |
| Remy Roberts     | power editor            | contribute, change-status-any, change owner, publish,                        |
| Casey Miyazaki   | editor, affiliation CDL | contribute, change-status-own, publish, NACO approve, report-own-affiliation |
| Jessie Coughlin  | admin, affiliation NYPL | enroll-own-affiliation report-own-affiliation, privilege-assign-own          |
| Avery Valenzuela | super admin             | enroll-any, report-any, privilege-assign-any                                 |


Privileges have a number of traits.

- Privileges are created and maintained by developers to authorize access to application features.
- Every privilege grants authorization to specific application features, therefore privileges are maintained
  only by developers.
- Privileges are additive, and all privileges of a user define that user's authorized functions. Everything not
  explicitly allowed is explicitly forbidden.
- Privileges (ideally) have a single feature permission per privilege. Privileges are very granular.
- Privileges must be coordinated with work flows and application features.
- Privilege is independent of institutional affiliation.
- Every user has the conceptually privilege Public HRT, although no actual privilege exists for read-only access to published constellations.
- Privileges can be deprecated, but re-purposing privileges is inadvisable from a security standpoint.
- Privileges need explicit, on-going policy guidance.
- Privilege label must be unique since the code will almost certainly refer to privileges symbolically, that
  is: by name, and the "name" of a privilege is the label field.
   

The minimal (implied?) privilege "Research" grants privileges for search history, and certain research
reports.  Members of the public are mostly identical to Researchers. The primary feature gained by having an
account is a persistent dashboard. As already noted, "Public HRT" not an actual privilege, but instead is
conceptual and implicit.


(Comment: I just noticed that privileges are mostly verbs: research, contribute, delete, propose, approve, block,
embargo, enroll, assign. It seems like a nice idea to enshrine this as a convention. We would have to change
"administrator" to "administer", and we need to come up with a verbs for the remaining privileges, especially
"own-affiliation".)

Note that privileges are granualar, and each privilege must exist only once. Having one privilege does not
imply having another. The Roles (a bit further below) deal with combinations of privileges.

#### Privilege table

| Privilege                        | Privilege Description                                                      |
|----------------------------------+----------------------------------------------------------------------------|
| Research                         | Use discovery interface and dashboard, has an account                      |
| Contribute (June 2016)           | Simplfied create                                                           |
| Edit (June 2016)                 | Edit constellations                                                        |
| Suggest edits (June 2016)        | Suggest edits, implies a mechanism to send suggestions to Edit             |
| Publish (June 2016)              | Review, approve, publish, change status to "published"                     |
| Change status any                | Change any status, removing locks, and so on, any affiliation              |
| Change owner (lock) any          | Change constellation user, changing which user has a lock, any affiliation |
| Change status own                | Change any status, removing locks, and so on, own affiliation only         |
| Change owner (lock) own          | Change constellation user, changing user lock, own affiliation only        |
| Delete/embargo                   | Delete or embargo constellations  (editor)                                 |
| Propose delete/embargo           | Propose delete or embargo                                                  |
| Ontology propose                 | Propose headings in ontologies, but cannot approve headings                |
| Ontology approve                 | Approve ontology headings (editor)                                         |
| Propose NACO                     | Create(?) NACO contributions, but not push(?) to NACO                      |
| Approve NACO                     | Approve, finalize, submit NACO contributions (editor)                      |
| Enroll                           | Enroll new SNAC participants, create new users                             |
| Privilege assign own affiliation | Assign new privileges for own-institution users                            |
| Privilege assign any affiliation | Assign new privileges for any institution users                            |
| System administrator             | Maintains server hardware and operating systems                            |
| Developer                        | Writes the SNAC application, a programmer                                  |
| Web administrator                | (duplicate? historical?) May perform admin tasks via the web interface     |
| Database administrator (DBA)     | Create and maintain the SQL database                                       |
| Block upload                     | Do bulk uploads of EAC-CPF, finding aids, etc.                             |
| Report own                       | Run own-institutional reports                                              |
| Report any                       | Run any report, super reporter                                             |
| Report admin                     | Run special administrative reports                                         |
| Role admin                       | Create and modify roles. May be limited to devs.                           |



### Example roles

A group of privileges is a "role". Users are assigned roles, and the user gains all privileges in those
roles. The authorization system only cares about privileges, so internal code will read the roles only to get
the permissions. Roles exists to make the system tractable for admins. Role name is unique. It would be
confusing for more than one role to have the same name.

Remember, the table below is only a guess or approximation of real world roles. The software team is very much
open to expanding the requirements to be as complete as possible. (Although not all requirements will
necessarily ever become features of SNAC.)

#### Role table

As of this writing, some roles have identical privileges. The duplicates should probably not exist.


| Role                           | Privilege(s)                                        | User Description                                       |
|--------------------------------+-----------------------------------------------------+--------------------------------------------------------|
| Sysadmin                       | System administrator                                | Maintain server, backups, etc.                         |
| Database Administrator         | DBA                                                 | Schema maintenance, data dumps, etc.                   |
| Software engineer              | Developer + DBA                                     | Coding, testing, QA, release, data loading, etc.       |
| Manager                        | Enroll + Privilege assign own + Report own          | Create, manage, assign privileges, run reports         |
| Peer vetting                   | Enroll                                              | Approve moderators, reviewers, content experts         |
| Editor (June 2016)             | Contribute + Publish                                | Same privilges as Reviewer                             |
| Moderator                      | Publish                                             | Approve maintenance changes, posting those changes     |
| Reviewer/editor                | Contribute + publish                                | Maintainer privileges, interacts with moderators       |
| Editor in Training (June 2016) | Contribute                                          | Editor, no publish, training to be Editor              |
| Contributor (June 2016)        | Simplified Create + Suggest Edits                   | Basic contributor, creator of constellations           |
| Content expert                 | Contribute                                          | Domain expert, may have no affiliation                 |
| Documentary editor             | Contribute                                          | (Any distinguishing privileges?)                       |
| Maintenance                    | Contribute                                          | Constellation edit, maintain constellations.           |
| Research                       | Research                                            | Use the discovery interface and history dashboard      |
| Archival description donor     | Block upload                                        | May do bulk uploads of EAC-CPF, finding aids, etc.     |
| Name authority manager         | Name authority                                      | (superseded by Editor-NACO?)                           |
| Institutional admins           | Report own                                          | May run own-affiliation reports                        |
| Public                         | n/a                                                 | No SNAC account, single session dashboard              |
| Contributor                    | Contribute + Ontology propose                       | Creates/edit constellations, propose ontology headings |
| Author                         | Contribute + Publish + Propose Del/Emb+Propose NACO | Contribute, with additional privileges                 |
| Editor                         | Contribute + Publish + Delete/embargo + NACO        | Review constellations, approve and publish             |
| Author-NACO                    | Propose NACO                                        | Creates NACO entries, sends to editor for submission   |
| Administrator                  | Contributor + Editor privileges + enroll + assign   | Every admin, only own affiliation                      |
| Administrator-super            | Administrator + any institution                     | Admin+ assign privileges, any user, any affiliation    |



### API

See the specifications for the API functions.


[User Management API Specifications](Specifications/DBUser API.md)

