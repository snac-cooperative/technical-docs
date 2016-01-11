
SNAC Data outline

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
