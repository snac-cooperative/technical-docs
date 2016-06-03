
### SNAC Data outline

This is a broad view of various kinds of data in the SNAC web application. At the core, SNAC data was
historically EAC-CPF. Working with the CPF data causes two things to happen. First, CPF data itself becomes
more of a constellation than discrete fields. Second, using and manipulating the data requires many types of
meta data.

SNAC has always had aspects of controlled vocabulary and authority work. Both of those are being formalized
and both add data to the SNAC application.

Most of the data resides in the SQL database. Nearly every item below corresponds to a SQL table. The database
also has additional tables serving various linking and record keeping functions. At this time, we have several
non-SQL data stores: XTF, Neo4j, Elastic search index

- EAC-CPF constellation (broadly disambiguated from "identity", "entity", "EAC-CPF", "record", etc).
    - Canonical data in SQL tables
    - XML output generated as necessary
    - Meta data
        - Version system
            - data current public version
            - data current edit version (for records being edited)
            - data old public versions
            - data old edit versions
        - Merge history
        - Links to outside resources
            - Archives
            - Finding aids
    - Multilingual strings
        - Web UI labels
        - Controlled vocabulary strings, including labels and definitions
    - Controlled vocabularies
        - Multilingual strings
        - Have category and hierarchy
        - All vocabularies share a base data structure
        - Use varies by policy; does this imply a vocabulary workflow?
- Name format system
    - Multiple known formats
    - Canonical SNAC format?
    - Context sensitive to language, script, and user?
- Workflows
    - Web UI workflow
        - Workflow specific to web domain, pages, buttons, output type, etc.
    - Server workflow
        - archivist edit
        - split/merge
            - identity reconciliation suggested merge
            - manual merge
        - policy based workflows
        - technical workflows
- Web admins
    - Create and assign institution roles (more powerful than institution admins)
- Institutions
    - Institution admins
    - Users
    - SNAC CPF entries for institutions
    - At least one role per institution
- Users
    - Dashboard tabs
        - Historical Research Tool per-user search history
        - Maintenance tool per-user workflow task status
        - Notifications, all users
    - Account info
        - name, email, user id, password
        - roles, as many as necessary
    - Web session, possibly multiple sessions per user
    - REST API session, similar or identical to web sessions
    - Removing a role from a user revokes the associated privilege
- Roles
    - Created and maintained by admins with role privileges
    - Single privilege per role, must be coordinated with workflows and application functions
    - At least one role exists per institution
    - At least one role per user (HRT user)
    - Potentially, roles for ad-hoc groups (sub-institution, department, professional orgs, etc.)
    - Need explicit, on-going policy guidance
- Reports
    - Read the database
    - Availability based on roles
- XTF full text index
- Neo4j graph database
- Elastic search full text index

### Database Schema Notes

The database schema capures all the data in CPF files, as well as meeting extensive additional data
requirements.

Early on we had the idead that each field within CPF might need provenance meta data. We have added Snac
control meta data (SCM) to each table on a per-record basis. Per-field is not practical.

The new schema has full versions of all records for all time. The version_history table records each table
name, record id, user id who modified, and time datestamp.

Every record has a unique id, although the row id is no longer unique. The unique key in most tables is
id,version.

We have a user table appuser, role table, a role-permission linking table. We allow several permissions per
group.

Constellations have status "deleted" working now. Status "embargo" is planned although the specific behavior is unclear.

Records have additional status "locked editing" (locked to user) and "currently editing" (currently being edited).

Institutional affiliation is a field in table appuser.

### Planned features

The planned watch system is a query run on some schedule (daily, hourly, ?) that checks
to see if a watched record has changed. CPF record has links to a “watch” table so users can watch each
record, and can watch for certain types of changes. Need UI for the watch system. Need an API for the watch
system.

There was an idea that workflow might be granular to parts of constellations. Some reviewers might review one part of the constellation, not not another. 

Be able to count record views, record downloads. Institutional dashboard reports need the ability to group-by
user, or even filter to a specific user.

Reporting needs to help managers verify performance metrics. This assumes that all changes have a
date/timestamp. Once workflow and process decisions are set, performance requirements for users such as
load/performance (how many updates and changes to records can be handled at once), search response time, edit
time (outside of review workflow), and update times need to be set.

Effort reporting to allow SNAC and participants to communicate to others the actual level of effort
involved. This sounds like a report with time span and numbers of records handled in various ways. SNAC might
use this when going from pilot into production so that everyone knows what effort will be required for X
number of records/actions (of whatever action type).

Time/activity reporting could allow us to assess viability, utility, and efficiency of maintenance system
processes.

Similar reports might be generated to evaluate the discovery interface.  Something akin to how much time was
required to access a certain number of records. Rachael said: Assess viability of access funtionality-
performance time, available features, and ease of use.

We could try to report on the amount of training necessary before a new user was able to work independently in
each of various areas (content input, review, etc.)
