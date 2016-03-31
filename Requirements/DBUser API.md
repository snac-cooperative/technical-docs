
### Introduction

This document is the requirements for the database user API, DBUser API. Eventually, there will be a
corresponding document in Specifications. This document covers user data requirements as related to
authentication and authorization (Au/Az).

For background about users, please see:

[User Management Requirements](Requirements/User Management.md)

### What data and features must be provided

- user must have a unique id 
- user must be able to authenticate (log in)
- user must be authorized for specific system features

User id must be unique for all time, and thus, user ids may not be reused. A unique user id internal to the
system may be a good idea. The textual user id such as "mst3k" or "mst3k@example.com" should probably also be
unique for all time, but (in theory) could be reused with a new, unique system use rid.

Accounts need an active flag. Deleted accounts might be kept inactive, as an easy way to prevent accidentally
creating an account with the same name at some future time. 
The list of features are described in more detail in other requirements docs although we list them below with
roles. Requirements here will cover implementation of authorization, and of management of authorization.

Each user has account info: 

- id (not editable, database assigned record id)
- first name
- last name
- full name
- email which may be user id
- user id which may be email, should probably be unique
- avatar
- avatar small
- avatar large
- password, stored encrypted

Each user has one or more roles, as many as necessary.

When logged in, a user as a "session", and the session has some associated data. We anticipate that there
could be multiple simultaneous sessions per user, and each session needs its own unique data. 

Current session data includes:

- access token from the authentication (OAuth?)
- expiration time stamp when the session will expire

The REST API requires the same session data. REST API users will have the same Au/Az constraints

The system has roles conceptually, extensively integrated throughout. Each user is granted one or more roles.

### Roles

The SNAC permission system is based entirely on roles (groups). SNAC is authorizing uses to run various
functions. Ownership is a non-concept, but essentially the system (sort of "root") owns all functions. When
there is only one owner, it has no use designing authorization partitions. That leaves SNAC with a
role-only system.

In a global sense, the cumulative users and roles is complex. On a per-user or per-role basis, things are very
manageable. When managing the system, imagine asking these fairly constrained (manageable) questions:

- List all users affiliated with CDL.

- List all user affiliated with UC Irvine.

- List all users who can enroll new users.

- List all users who can publish constellations.

- List all users who may run reports for their own affiliation.

Below is an example. Anyone who can log in can get a dashboard, so there are no specific dashboard permissions
(yet). The user id public and the role "researcher" may not any sense. Everyone has it, so there's no reason to
mention/restrict/authorize it. This table initially had a user id "public" with role "Public HRT", and every
user also had the role "Public HRT", but that is simply redundant. When it comes to implementation, the
authorization system will actually use some convention for testing role. The feature "Public HRT" can call a
role test that always returns true.

The roles below are real, but do not imply any actual policy or any relation to persons living or dead.

Pulling some random details, and wording them in English, the table below says that Casey at CDL can approve
NACO submissions (NACO approve). Jessie at NYPL is limited to assigning roles only for NYPL (affiliation-nypl
and role-assign-own).


| user             | description    | roles                                                                                           |
|------------------+----------------+-------------------------------------------------------------------------------------------------|
| Marley Turner    | content expert | contribute, affiliation-none                                                                    |
| Remy Roberts     | power editor   | contribute, change-status-any, change owner, publish, affiliation-any                           |
| Casey Miyazaki   | editor CDL     | contribute, change-status-own, publish, NACO approve, affiliation-cdl, reporter-own-affiliation |
| Jessie Coughlin  | admin NYPL     | affiliation-nypl, enroll-own-affiliation reporter-own-affiliation, role-assign-own              |
| Avery Valenzuela | super admin    | enroll-any, reporter-any, role-assign-any                                                       |



Not having "files", there is no concept of "file has no privs" for user, group, or other. An example of what
SNAC does not have is the shared web server situation. In a shared web server, users are all assigned to a
group "users". Web accessible directories have no group permissions and are owned by group user. Lacking
permissions for group user, no one in group user has any access. Actual access then is granted only to the
owner who has rwx for that directory. Apache runs as suexec for each user, and thus each users CGI scripts
gain access to only that user's directory via the user privs.

SNAC lacks this "subtractive" permission, and instead has only additive permissions.

Roles have a number of traits:

- Created and maintained by admins with role-granting privileges
- There is (ideally) a single privilege per role
- Roles and privileges must be coordinated with work flows and application features
- At least one role exists per institutional affiliation
- Every user has the implied role Public HRT
- Potentially we can have roles for ad hoc groups (sub-institution, department, professional orgs, etc.)
- Roles can be deprecated, but it re-purposing roles is inadvisable from a security standpoint
- Need explicit, on-going policy guidance
   

Every account will have the "Researcher" role which has the same privileges as the general public, but with a
TBD set of basic privileges including: search history, certain researcher reports. An account is not necessary
to use SNAC. Members of the public are mostly identical to Researchers. The primary feature gained by having
an account is a persistent dashboard.


| Role                          | Role Description                                                          |
|-------------------------------+---------------------------------------------------------------------------|
| Public HRT                    | No account, but may use HRT public interfaces to SNAC                     |
| Researcher                    | May use the discovery interface and history dashboard, has an account     |
| Contribute                    | Create and edit constellations but cannot publish (contributor)           |
| Publish                       | Approve constellation publication (editor)                                |
| Change status any             | Change status, removing locks, and so on, any affiliation                 |
| Change owner any              | Change constellation user, changing which user has a lock, any affiliation |
| Change status own             | Change status, removing locks, and so on, own affiliation only            |
| Change owner own              | Change constellation user, changing user lock, own affiliation only       |
| Delete/embargo                | Delete or embargo constellations  (editor)                                |
| Propose delete/embargo        | Propose delete or embargo                                                 |
| Ontology propose              | Propose headings in ontologies, but cannot approve headings               |
| Ontology approve              | Approve ontology headings (editor)                                        |
| Propose NACO                  | Create(?) NACO contributions, but not push(?) to NACO                     |
| NACO approve/finalize/submit  | Approve NACO contributions (editor)                                      |
| Enroll                        | Enroll new SNAC participants, create new users                            |
| Role assign own institution   | Assign new roles for own-institution users                                |
| Role assign any institution   | Assign new roles for any institution users                                |
| System administrator          | Maintains server hardware and operating systems                           |
| Developer                     | Writes the SNAC application, a programmer                                 |
| Web administrator             | (duplicate? historical?) May perform admin tasks via the web interface    |
| Database administrator        | Create and maintain the SQL database                                      |
| Block upload                  | Do bulk uploads of EAC-CPF, finding aids, etc.                            |
| Institutional reporter        | Run own-institutional reports                                             |
| Super reporter                | Run any report                                                            |
| Institutional affiliation X     | Affiliated with institution X.                                            |
| Institutional affiliation super | Affiliated with all institution, a super role (discuss?)                   |


More example user types, with their role(s) and description.


| User type                  | Role(s)                                          | User Description                                            |
|----------------------------+--------------------------------------------------+-------------------------------------------------------------|
| Sysadmin                   | System administrator                             | Maintain server, backups, etc.                              |
| Database Administrator     | DBA                                              | Schema maintenance, data dumps, etc.                        |
| Software engineer          | Developer + DBA                                  | Coding, testing, QA, release management, data loading, etc. |
| Manager                    | Enroll + Role assign + Inst. Reporter            | SNAC accounts: create, manage, assign roles, run reports    |
| Peer vetting               | Enroll                                           | Approve moderators, reviewers, content experts              |
| Moderator                  | Editor-publish                                   | Approve maintenance changes, posting those changes          |
| Reviewer/editor            | Contributor + Editor-publish                     | Maintainer privileges, interacts with moderators            |
| Content expert             | Contributor                                      | Domain expert, may have zero institutional roles            |
| Documentary editor         | Contributor                                      | (Any distinguishing roles?)                                 |
| Maintenance                | Contributor, constellation                       | May be older terminology for "contributor"                  |
| Researcher                 | Researcher                                       | Use the discovery interface and history dashboard           |
| Archival description donor | Block upload                                     | May do bulk uploads of EAC-CPF, finding aids, etc.          |
| Name authority manager     | Name authority                                   | (superseded by Editor-NACO?)                                |
| Institutional admins       | Institutional reporter                           | May run own-institutional reports                           |
| Public                     | Public                                           | No SNAC account, single session dashboard                   |
| Contributor                | Contribute + Ontology propose                   | Creates/edit constellations, propose ontology headings      |
| Author                     | Contribute + Publish + Propose Del/Emb+Propose NACO | A contributor, with additional privileges                   |
| Editor                     | Contribute + Publish + Delete/embargo + NACO    | Review constellations, approve and publish                  |
| Author-NACO                | Create provisional NACO                          | Creates NACO entries, sends to editor for submission        |
| Administrator              | Author + editor + enroll + assign                | Everything, only own institution                            |
| Administrator-super        | Administrator + any institution                  | Admin plus assign roles for any user of any institution     |





