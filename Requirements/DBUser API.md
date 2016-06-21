
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

- Each user has one or more roles, and roles have privileges, therefore users have privileges.

- User by belong to groups, for the purposes of workflow. For example a group of users who are "All Reviewers"
  or the smaller group "Reviewers, Welsh".

- Create and destroy Roles

- Add privileges, but this can be done via manual methods since privileges must be integrated with the code.

- Create, destroy, and manage membership of Groups

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

### Authorization management

SNAC has Roles, Privileges, and Groups of users. The critical core of authorization is Privilege. Roles exist
only make the system easier to administer. Groups exist to add flexibiliy and convenience to workflows and
some user interface.

Roles have privileges. Users have Roles and therefore users indirectly have privileges. Users are optionally
members of groups. Groups exist to streamline workflow such as "send for review" by any member of a group of
reviewers. In fact, the primary use case for Groups is the reviewing process.


### Privileges

The SNAC permission system is based entirely on privileges (permissions). Sets of privileges are
roles. Privileges authorize users to run various functions. Ownership of a constellation is a non-concept, and
essentially the system owns all functions and all constellations. However, a user can have a constellation
locked, and a constellation can only be locked to a single user.

Note that affiliation is a property of each user account, and there is a single affiliation per user.

SNAC privileges are additive. Any privilege not explicitly allowed is explicitly forbidden.

In a global sense, the cumulative collection of users and privileges is complicated. However, that complexity is
essential (not accidental) and on a per-user, per-role, per-privilege basis, things are very manageable. When managing the
system, imagine asking these typical, and manageable questions:

- Can user X edit a constellation?

- Can user X publish a constellation?

- Can user X send a constellation for review by user Y?

- List all users who can Add Users

- List all users who can publish constellations

- List all users who may run reports for their own affiliation

- List all users affiliated with my affilation


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


| User             | Description             | Privileges                                                      |
|------------------+-------------------------+-----------------------------------------------------------------|
| Marley Turner    | content expert          | Create + Edit                                                   |
| Remy Roberts     | power editor            | Create + Edit + Publish + Change Locks                          |
| Casey Miyazaki   | editor, affiliation CDL | Create + Edit + Publish + NACO approve + report-own-affiliation |
| Jessie Coughlin  | admin, affiliation NYPL | Add Users + Assign Roles + report-own-affiliation               |
| Avery Valenzuela | super admin             | Add Users + Assign Roles + report-any-affiliation               |


Privileges have a number of traits.

- Privileges are created and maintained by developers to authorize access to application features.
- Every privilege grants authorization to a specific application feature, therefore privileges are maintained
  only by developers.
- Privileges are additive, and all privileges of a user define that user's authorized functions. Everything not
  explicitly allowed is explicitly forbidden.
- Privileges (ideally) have a single feature permission per privilege. Privileges are very granular.
- Privileges must be coordinated with work flows and application features.
- Privilege is independent of institutional affiliation.
- Every user has the conceptually privilege Researcher, although no actual privilege exists for read-only
  access to published constellations.
- Privileges can be deprecated, but re-purposing privileges is inadvisable from a security standpoint.
- Privileges need explicit, on-going policy guidance.
- Privilege label must be unique since the code will almost certainly refer to privileges symbolically, that
  is: by name, and the "name" of a privilege is the label field.
   

The minimal (implied?) privilege "Researcher" has privileges for search history, and certain research reports.
Members of the public are nearly identical to Researchers. The primary feature gained by having an account is
a persistent dashboard. As already noted, Researcher is not an actual privilege, but instead allows use of
features which require no authorization.

Note that privileges are granualar, and each privilege must exist only once. Having one privilege does not
imply having another. The Roles (a bit further below) deal with combinations of privileges.

#### Privilege table

Any privilege which modifies data implies that those modifications can be saved, thus there is no privilege
"Can Save". Some features will require alternative user interfaces, especially Simplified Create and
Suggest Edits. In cases where there is implied workflow (as in: send for review) there is also an implied
user interface to support the feature. For example, "sending" a constellation will require a field for the
recipient's name, a field for a text message and a "Send" button.

Users who can modify or suggest modifications need to be able to lock constellations to themselves. Anyone who
can lock can also transfer their own locks. 

Question: Who they can transfer the locks to? Minimally, the answer is: Anyone who could have locked the
constellation.

Several actions lock a constellation. Edit, and Suggest Edits both lock a constellation to the user. In the
case of Suggest Edits, the lock will later transfer an editor, or to a pool of editors. There may be some
subtle lock-taking behavior around pools of suggested edits, or taking over someone else's edit (where
workflow allows). In any case, a user with Change Locks can transfer lock from any user to any other without
getting permission.

Review is implicit as a workflow for anyone who is able to publish. This system does not solve the governance
problem of "which editor is more powerful". Every change is recorded in version history, so it is always
possible to revert, and impossible to "destroy" anyone else's work. We assume at least a minimal level of
cooperation, and leave dispute resolution as a management problem not enforced by software. 

For example, we have no plans for a feature "prevent user X from editing". In this world, any our miscreant
user X could wait for a constellation to be unlocked, and then make rogue changes to the
constellation. Preventing rogue behavior is a governance problem, with which software can merely assist.

The privilege Change Locks is fairly powerful, allowing locks to be transfered from/to/between other
users. A lock transfering user interface is implied.

Publishing a constellation removes all locks. 

In general, there is no privilege for research, which includes having an account, using the discovery interface
and dashboard. These are features available to anyone with a login.

The privilege Unlock Currently Editing refers to an internal status "currently editing" which is part of the
user interface bookkeeping. In certain rare situations someone will have to change that status back to simply
"locked editing", and we want this privilege to be granular (instead of being conflated with Change Locks).

Discuss: Why are Edit and Create are separate privileges? It seems like a good idea, and particular examples
would clarify our intent.


| Privilege                | Privilege Description                                                   |
|--------------------------+-------------------------------------------------------------------------|
| Edit                     | Edit constellations                                                     |
| Create                   | Create new constellations                                               |
| Publish                  | Publish or commit a constellation after reviewing                       |
| Send for Review          | Send a constellation to reviewer(s)                                     |
| Simplified Create        | Create basic, simplified, constellations                                |
| Suggest Edits            | Suggest constellation edits                                             |
| Change Locks             | Change which user has a constellation locked                            |
| Unlock Currently Editing | Unlock constellations stuck in status Currently Editing                 |
| Add Users                | Enrole new SNAC participants (new users), edit new user info            |
| Assign Roles             | Assign, modify user roles                                               |
| Modify Users             | Modify user email, phone numbers, affiliation, etc.                     |
| Inactivate Users         | Able to inactivate user accounts                                        |
| Manage Groups            | Add users to groups, remove users from groups, create and delete groups |
| Manage My Group          | Administer the membership of groups I belong to                         |



#### Additional privileges

The brief table above will require some additional privileges in order to function smoothly. This list is
almost certainly evolving.

Locking poses the interesting question of where to draw the line on granularity. The ability to transfer a
lock implies that locking should be a privilege in order to be easily testable. We want to ask: Can this user
lock? And not: Does this user have Simplified Create, Suggest Edits, Edit, or Create privileges?

Not every privilege implies locking, although several do: Simplified Create, Suggest Edits, Edit, Create,
Change Locks. Locking involves modifying both the user_id, and the status of a version_history
record.


| Privilege                | Description                                                                |
|--------------------------+----------------------------------------------------------------------------|
| Lock                     | Lock a constellation for edit or suggest edit                              |
| Change status any        | Change any status, removing locks, and so on, any affiliation              |
| Change Locks (owner) any | Change constellation user, changing which user has a lock, any affiliation |
| Change status own        | Change any status, removing locks, and so on, own affiliation only         |
| Change owner (lock) own  | Change constellation user, changing user lock, own affiliation only        |
| Delete/embargo           | Delete or embargo constellations                                           |
| Propose delete/embargo   | Suggest delete or embargo                                                  |
| Ontology propose         | Suggest headings in ontologies, but cannot approve headings                |
| Ontology approve         | Approve ontology headings                                                  |
| Propose NACO             | Create(?) NACO contributions, but not push(?) to NACO                      |
| Approve NACO             | Approve, finalize, submit NACO contributions                               |
| Assign Roles, own        | Assign and modify roles for own-institution users                          |
| Assign Roles, any        | Assign and modify roles for any institution users                          |
| Developer                | Writes the SNAC application, a programmer                                  |
| Block upload             | Do bulk uploads of EAC-CPF, finding aids, etc.                             |
| Report own               | Run own-institutional reports                                              |
| Report any               | Run any report, super reporter                                             |
| Report admin             | Run special administrative reports                                         |
| Name authority           | Create name authority records, perhaps external to SNAC                    |
| Role developer           | Create and modify system roles                                             |



### Example roles

A group of privileges is a "role". Users are assigned roles, and the user gains all privileges in those
roles. The authorization system only cares about privileges, so internal code will read the roles only to get
the permissions. Roles exists to make the system tractable for admins. Role name is unique. It would be
confusing for more than one role to have the same name.

Remember, the table below is only a guess or approximation of real world roles. The software team is very much
open to expanding the requirements to be as complete as possible. (Although not all requirements will
necessarily ever become features of SNAC.)

#### Role table

After the June 2016 meeting, we have a more focused, smaller set of roles.

Question: Should editors also have privileges Simplified Create and Suggest Edits? The additional privileges
can easily be added at any time, and are probably better explicitly stated than being conflated somewhere in
the code.

| Role                      | Privilege(s)                                                               | User Description                                |
|---------------------------+----------------------------------------------------------------------------+-------------------------------------------------|
| Contributor/Basic Creator | Simplified Create + Suggest Edits                                          | Create simplified constellations, suggest edits |
| Editor, Training          | Edit                                                                       | Editor, no publish, training to be Editor       |
| Editor, Full              | Edit + Publish                                                             | Full editor                                     |
| Reviewer                  | Edit + Publish + Change Locks                                              | Editor and moderator                            |
| Administrator             | Add Users + Assign Roles + Modify Users + Manage Groups + Inactivate Users | Manage users, roles, groups                     |
| System Administrator      | All                                                                        | SNAC developers, super users                    |


There are certainly additional roles, and the list is evolving. There may be roles related to reporting. Some
roles will suggest themselves as new features become available.


### API

See the specifications for the API functions.


[User Management API Specifications](Specifications/DBUser API.md)

