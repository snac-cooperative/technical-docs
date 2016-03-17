
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

- Roles
    - Created and maintained by admins with role privileges
    - Single privilege per role, must be coordinated with workflows and application functions
    - At least one role exists per institution
    - At least one role per user (HRT user)
    - Potentially, roles for ad-hoc groups (sub-institution, department, professional orgs, etc.)
    - Need explicit, on-going policy guidance

Every account will be in the "Researcher" role which has the same privileges as the general public, but with a
TBD set of basic privileges including: search history, certain researcher reports.


| User type                  | Role                               | Description                                                           |
|----------------------------+------------------------------------+-----------------------------------------------------------------------|
| Sysadmin                   | Server admin                       | Maintain server, backups, etc.                                        |
| Database Administrator     | DBA                                | Schema maintenance, data dumps, etc.                                  |
| Software engineer          | Developer                          | Coding, testing, QA, release management, data loading, etc.           |
| Manager                    | Web admin                          | Web accounts: create, manage, assign roles, run reports               |
| Peer vetting               | Vetting                            | Approve moderators, reviewers, content experts                        |
| Moderator                  | Moderator                          | Approve maintenance changes, posting those changes                    |
| Reviewer/editor            | Maintenance                        | Maintainer privileges, interacts with moderators                      |
| Content expert             | Maintenance                        | Domain expert, may have zero institutional roles                      |
| Documentary editor         | Maintenance                        | Distinguished by?                                                     |
| Maintenance                | Maintenance                        | Distinguished by?                                                     |
| Researcher                 | Researcher                         | Use the discovery interface and history dashboard                     |
| Archival description donor | Block upload                       | Bulk uploads of CPF or finding aids                                   |
| Name authority manager     | Name authority                     | Donates name authority data perhaps via bulk upload                   |
| Institutional admins       | Institutional admin                | Instutional role admin dashboard, institutional reports               |
| Public                     | Researcher                         | No account, researcher role, no dashboard or single session dashboard |
| Contributor, constellation | Constellation create/edit          | Create and edit constellations but cannot publish                     |
| Contributor, ontology      | Ontology propose heading           | May propose headings in ontologies, but cannot approve headings       |
| Editor-publish             | Constellation publish              | May approve constellation publication                                 |
| Editor-ontology            | Ontology approve                   | May approve ontology headings                                         |
| Editor-NACO                | NACO approve/finalize/submit       | May approve NACO contributions                                        |
| Editor-delete-embargo      | Constellation delete/embargo       | May delete or embargo constellations                                  |
| Author-constellation       | Contrib+Editor-publish             | Create, edit and publish constellations                               |
| Author-NACO                | Create provisional NACO            | May create(?) NACO contributions, but not push(?) to NACO             |
| Administrator              | Contributor + Editor-constellation | May create, edit, publish, delete, and embargo constellations         |
| Administrator-enroll       | Create new users                   | May enroll new SNAC participants                                      |
| Administrator-assign       | Role assign own institution        | May assign new roles for own-institution individuals                  |
| Superuser                  | Admin-enroll+Admin-assign+Any-inst | Admin plus assign roles for any user of any institution               |

Constellation create/edit
Constellation publish
Constellation delete/embargo
Ontology propose heading
Ontology approve heading
Create provisional NACO
Approve/finalize/submit NACO
Create new users
Role assign own institution
Role assign any institution




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
