
### Introduction

This document is the requirements for the database user API, DBUser API. Eventually, there will be a
corresponding document in Specifications. This document covers user data requirements as related to
authentication and authorization (Au/Az).

For background about users, please see:

[User Management Requirements](Requirements/User Management.md)

### What users must be able to

Users must be able to have a unique id, authenticate (login), and do various authorized functions. The list of
functions are in other requirements docs, and we will cover how people are granted authorized access, and how
users and authorization are managed.

Each user as account info, name, email, user id, password.

Each user as one or more roles, as many as necessary.

When logged in, a user as a "session", and the session has some associated data. We anticipate that there
could be multiple simultaneous sessions per user, and each session needs its own unique data. Perhaps it is
better to defer in depth discussion of session until user requirements and code are both functioning.

The REST API needs session as well. REST API users will have the same Au/Az strictures.

The system has roles conceptually extensively integrated throughout. Each user is granted roles in addition to
their base data.

#### Roles

Roles are somewhat synonymous with Linux groups. Users have a primary group, but may have several groups. The
user has all privileges associated with each group of which they are a member.

Roles have a number of traits:

- Created and maintained by admins with role-granting privileges
- There is (ideally) a single privilege per role
- Roles and privileges must be coordinated with workflows and application features
- At least one role exists per institution
- At least one role per user, Public aka History Research Tool (HRT) user
- Potentially we can have roles for ad-hoc groups (sub-institution, department, professional orgs, etc.)
- Can be deprecated, but it repurposing roles is inadvisable from a security standpoint
- Need explicit, on-going policy guidance
   
Unlike Linux where every file and directory is "owned" by a user and group, SNAC constellations have no
ownership.

Every account will have the "Researcher" role which has the same privileges as the general public, but with a
TBD set of basic privileges including: search history, certain researcher reports. An account is not necessary
to use SNAC. Members of the public are mostly identical to Researchers. The primary feature gained by having
an account is a persistent dashboard.


| Role                         | Role Description                                                       |
|------------------------------+------------------------------------------------------------------------|
| Public HRT                   | No account, but may use HRT public interfaces to SNAC                  |
| Researcher                   | May use the discovery interface and history dashboard, has an account  |
| Create/edit                  | Create and edit constellations but cannot publish (contributor)        |
| Publish                      | May approve constellation publication (editor)                         |
| Delete/embargo               | May delete or embargo constellations  (editor)                         |
| Propose delete/embargo       | May propose delete or embargo                                          |
| Ontology propose             | May propose headings in ontologies, but cannot approve headings        |
| Ontology approve             | May approve ontology headings (editor)                                 |
| Propose NACO                 | May create(?) NACO contributions, but not push(?) to NACO              |
| NACO approve/finalize/submit | May approve NACO contributions (editor)                                |
| Enroll                       | May enroll new SNAC participants, create new users                     |
| Role assign own institution  | May assign new roles for own-institution users                         |
| Role assign any institution  | May assign new roles for any institution users                         |
| System administrator         | Maintains server hardware and operating systems                        |
| Developer                    | Writes the SNAC application, a programmer                              |
| Web administrator            | (duplicate? historical?) May perform admin tasks via the web interface |
| Database administrator       | Create and maintain the SQL database                                   |
| Block upload                 | May do bulk uploads of EAC-CPF, finding aids, etc.                     |
| Institutional reporter       | May run own-institutional reports                                      |
| Super reporter               | May run any report                                                     |



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
| Name authority manager     | Name authority                                   | (superceded by Editor-NACO?)                                |
| Institutional admins       | Institutional reporter                           | May run own-institutional reports                           |
| Public                     | Public                                           | No SNAC account, single session dashboard                   |
| Contributor                | Create/edit + Ontology propose                   | Creates/edit constellations, propose ontology headings      |
| Author                     | Create/edit + Publish + Propose Del/Emb+Propose NACO | A contributor, with additional privileges                   |
| Editor                     | Create/edit + Publish + Delete/embargo + NACO    | Review constellations, approve and publish                  |
| Author-NACO                | Create provisional NACO                          | Creates NACO entries, sends to editor for submission        |
| Administrator              | Author + editor + enroll + assign                | Everything, only own institution                            |
| Administrator-super        | Administrator + any institution                  | Admin plus assign roles for any user of any institution     |


### What data has to be stored for the user

### What data is bookkeeping

### Authentication login

### Authorization privileges

### Web Session and user interface

- Users
    - Dashboard tabs
        - Historical Research Tool per-user search history
        - Maintenance tool per-user workflow task status
        - Notifications, all users
