
### Introduction

For background, please see:

[User Management Requirements](Requirements/User Management.md)

### What users must be able to

#### Roles

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
