


- EAC-CPF data
    - All CPF data saved in SQL tables
    - Meta data
        - Version system
            - data current public version
            - data current edit version (for records being edited)
            - data old public versions
            - data old edit versions
    - Historical Research Tool per-user search history
    - Maintenance tool per-user workflow task status
    - Notifications, all users
- Links to outside resources
    - Archives
    - Finding aids
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
- Institutions
    - Admins
    - Users
    - SNAC CPF entries for institutions
- Users
    - name, email, user id, password
    - roles, as many as necessary
- Roles
    - Created and maintained by admins with role privileges
    - Single privilege per role
    - One role per institution
    - Potentially, roles for ad-hoc groups (sub-institution, department, professional orgs, etc.)
- Name format system
    - Multiple known formats
    - Canonical SNAC format?
    - Language, script, and user sensitive?
- Multilingual strings
    - Web UI labels
    - Controlled vocabulary strings, including labels and definitions
  


- create data constellation outline:

CPF
workflow

merge meta data

archivist edit workflow
merge workflow
policy workflow
technical workflow
snac users and roles
human merge
identity reconciliation suggested merge
data versions
name format
finding aids
archives
institutions as CPF entities
external resources
