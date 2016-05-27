
### Introduction

This document is the requirements for the database user API, DBUser API. Eventually, there will be a
corresponding document in Specifications. This document covers user data requirements as related to
authentication and authorization (Au/Az).

For background about users, please see:

[User Management Requirements](Requirements/User Management.md)

### What data and features must be provided

- user must have a unique id, which we mint when creating the user database record in the database. The id is
  supplied by the database as a record id, which is a unique integer. User must have a unique username, which
  is some string; we currently use an email address for username

- support for data necessary to authenticate (log in), which is currently handled outside DBUser by
  OpenID. This data includes an access token and expires time

- support for user authorization for specific system features. This requirement relies on privilege(s).

- create user, requires a username which we default to the email address string. We also expect that the email
  field will contain an email address. There was some discussion of multiple email addresses per user. That
  has not been implemented.

- update user, all fields except the ID and username; ID must never be modified. Changing username will have
  unpredictable consequences.

- delete or inactivate user

- create privilege

- add privilege to user

- remove privilege from user

- delete privilege (probably inadvisable, but we make it possible)

- allow multiple privileges per user

- create session

- extend session expiration time

- add session to user

- remove session from user

- remove all sessions from user

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
- active (boolean)
- first name
- last name
- full name
- email, not unique
- avatar
- avatar small
- avatar large
- password, stored hashed

When logged in, a user has at least one "session", and the session has some associated data. We anticipate
that there could be multiple simultaneous sessions per user, and each session needs its own unique data.

Current session data includes:

- access token from the authentication (OAuth?)
- expiration time stamp when the session will expire

The REST API requires the same session data. REST API users will have the same Au/Az constraints

The system has privileges conceptually, extensively integrated throughout the DBUser API. Each user is granted one
or more privileges.  Each user has one or more privileges, as many as necessary.


### Privileges

The SNAC permission system is based entirely on privileges (groups). SNAC is authorizing users to run various
functions. Ownership is a non-concept, but essentially the system owns all functions. In a situation like SNAC
where there is a single owner, the concept of "owner" has no utility in authorization. The Linux concept of
"other" doesn't apply to SNAC as "other" is only allowed to perform read-only actions such as search. That
leaves SNAC needing only a privilege system.

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

- List all users affiliated with California Digital Library (CDL).

- List all users affiliated with UC Irvine.

- List all users who can enroll new users for any affiliation

- List all users who can only enroll new users for NYPL

- List all users who can publish constellations.

- List all users who may run reports for their own affiliation.

Below is an example. Anyone who can log in can get a dashboard, so there are no specific dashboard permissions
(yet). The user id "public" and the privilege "research" may not make any sense. Conceptually, everyone has privilege
"Public HRT", so there's no reason to restrict or authorize it. In actual implementation the "what is public"
divide is simply any published record or read-only action on published constellations. The table below
initially had a user id "public" with privilege "Public HRT", and every user also had the privilege "Public HRT", but
that was simply redundant.

The privileges below are real, but do not imply any actual policy or any relation to persons living or dead.

Pulling a couple of random details from the table below, and wording them in English:

- Casey at CDL can approve NACO submissions (NACO approve)
- Jessie at NYPL is limited to assigning privileges only for NYPL (affiliation-nypl and privilege-assign-own)


| user             | description    | privileges                                                                                           |
|------------------|----------------|-------------------------------------------------------------------------------------------------|
| Marley Turner    | content expert | contribute, affiliation-none                                                                    |
| Remy Roberts     | power editor   | contribute, change-status-any, change owner, publish, affiliation-any                           |
| Casey Miyazaki   | editor CDL     | contribute, change-status-own, publish, NACO approve, affiliation-cdl, report-own-affiliation   |
| Jessie Coughlin  | admin NYPL     | affiliation-nypl, enroll-own-affiliation report-own-affiliation, privilege-assign-own                |
| Avery Valenzuela | super admin    | enroll-any, report-any, privilege-assign-any                                                         |


Privileges have a number of traits.

- Privileges are additive, and all privileges of a user define that user's authorized functions
- Privileges are created and maintained by admins and developers.
- Some instance privileges (such as affiliation) can be created by admins as needed.
- Privileges (ideally) have a single privilege per privilege. Privileges are very granular.
- Privileges and privileges must be coordinated with work flows and application features.
- One privilege exists per institutional affiliation.
- Every user has the conceptually privilege Public HRT, although no actual privilege exists for read-only access to published constellations
- Potentially, we can have privileges for ad hoc groups (sub-institution, department, professional orgs, etc.)
- Privileges can be deprecated, but re-purposing privileges is inadvisable from a security standpoint.
- Privileges need explicit, on-going policy guidance.
   

The minimal privilege "Research" grants privileges for search history, and certain research reports.  Members of
the public are mostly identical to Researchers. The primary feature gained by having an account is a
persistent dashboard. As already noted, "Public HRT" not an actual privilege, but instead is conceptual.


(Comment: I just noticed that privileges are mostly verbs: research, contribute, delete, propose, approve, block,
embargo, enroll, assign. It seems like a nice idea to enshrine this as a convention. We would have to change
"administrator" to "administer", and we need to come up with a verbs for the remaining privileges, especially
"affiliation".)


| Privilege                        | Privilege Description                                                      |
|----------------------------------+----------------------------------------------------------------------------|
| Research                         | Use discovery interface and dashboard, has an account                      |
| Contribute                       | Create and edit constellations but cannot publish (contributor)            |
| Publish                          | Approve constellation publication, change status to "published" (editor)   |
| Change status any                | Change any status, removing locks, and so on, any affiliation              |
| Change owner any                 | Change constellation user, changing which user has a lock, any affiliation |
| Change status own                | Change any status, removing locks, and so on, own affiliation only         |
| Change owner own                 | Change constellation user, changing user lock, own affiliation only        |
| Delete/embargo                   | Delete or embargo constellations  (editor)                                 |
| Propose delete/embargo           | Propose delete or embargo                                                  |
| Ontology propose                 | Propose headings in ontologies, but cannot approve headings                |
| Ontology approve                 | Approve ontology headings (editor)                                         |
| Propose NACO                     | Create(?) NACO contributions, but not push(?) to NACO                      |
| Approve NACO                     | Approve, finalize, submit NACO contributions (editor)                      |
| Enroll                           | Enroll new SNAC participants, create new users                             |
| Privilege assign own institution | Assign new privileges for own-institution users                            |
| Privilege assign any institution | Assign new privileges for any institution users                            |
| System administrator             | Maintains server hardware and operating systems                            |
| Developer                        | Writes the SNAC application, a programmer                                  |
| Web administrator                | (duplicate? historical?) May perform admin tasks via the web interface     |
| Database administrator (DBA)     | Create and maintain the SQL database                                       |
| Block upload                     | Do bulk uploads of EAC-CPF, finding aids, etc.                             |
| Report own                       | Run own-institutional reports                                              |
| Report any                       | Run any report, super reporter                                             |
| Report admin                     | Run special administrative reports                                         |
| Affiliation X                    | Affiliated with institution X.                                             |
| Affiliation any                  | Affiliated with all institutions, a super privilege (discuss?)             |


Example role, with their privilege(s) and description.


| Role                       | Privilege(s)                                        | User Description                                       |
|----------------------------+-----------------------------------------------------+--------------------------------------------------------|
| Sysadmin                   | System administrator                                | Maintain server, backups, etc.                         |
| Database Administrator     | DBA                                                 | Schema maintenance, data dumps, etc.                   |
| Software engineer          | Developer + DBA                                     | Coding, testing, QA, release, data loading, etc.       |
| Manager                    | Enroll + Privilege assign own + Report own          | Create, manage, assign privileges, run reports         |
| Peer vetting               | Enroll                                              | Approve moderators, reviewers, content experts         |
| Moderator                  | Publish                                             | Approve maintenance changes, posting those changes     |
| Reviewer/editor            | Contribute + publish                                | Maintainer privileges, interacts with moderators       |
| Content expert             | Contribute                                          | Domain expert, may have zero institutional privileges  |
| Documentary editor         | Contribute                                          | (Any distinguishing privileges?)                       |
| Maintenance                | Contribute                                          | Constellation edit, maintain constellations.           |
| Research                   | Research                                            | Use the discovery interface and history dashboard      |
| Archival description donor | Block upload                                        | May do bulk uploads of EAC-CPF, finding aids, etc.     |
| Name authority manager     | Name authority                                      | (superseded by Editor-NACO?)                           |
| Institutional admins       | Report own                                          | May run own-institutional reports                      |
| Public                     | n/a                                                 | No SNAC account, single session dashboard              |
| Contributor                | Contribute + Ontology propose                       | Creates/edit constellations, propose ontology headings |
| Author                     | Contribute + Publish + Propose Del/Emb+Propose NACO | Contribute, with additional privileges                 |
| Editor                     | Contribute + Publish + Delete/embargo + NACO        | Review constellations, approve and publish             |
| Author-NACO                | Propose NACO                                        | Creates NACO entries, sends to editor for submission   |
| Administrator              | Contributor + Editor privileges + enroll + assign   | Everything, only own institution                       |
| Administrator-super        | Administrator + any institution                     | Admin+ assign privileges, any user, any institution    |


### API

See the specifications for the API functions.


[User Management API Specifications](Specifications/DBUser API.md)

