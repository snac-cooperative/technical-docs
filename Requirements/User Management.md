# User Management

### Introduction

Authentication is: "validating user logins to the system." Authorization is: "privileges
allowing user access to system features."

We are using OpenID for authentication, but we need a user profile for SNAC roles and authorization. 

OpenID seems to constantly be changing, and sites using it change frequently. Google has (apparently)
deprecated OpenID 2.0 in favor of Open Connect. Facebook is using something else, but apparently FB still
works with OpenID. Stackexchange supports several authentication schemes. If they can do it, so can we. Or we
can support one scheme for starters and add others as necessary. The SE code is not open source, so we can't
see how much work it was to support the various OpenID partners.

### Authorization

Authorization involves controlling what users can do once they are in the system. That function is sort of
more solved by OAuth or OpenID by sharing the user profile. However, SNAC has specific requirements,
especially our roles, and those will not be found in other system. There is not anything we must have from
user profiles. We might want their social networking profile, but social networking is not a core function of
SNAC.

By default, the lowest privileged users can't do anything that isn't exposed to the non-authenticated public
users. Privileges are added and users are given roles (aka groups) from which they inherit privileges. The
authorization system is involved in every transaction with the server to the extent that every request to the
server is checked for authorization before being passed to the code doing the real work. (Every request is
also checked for authentication as well, naturally.)

### Roles analagouse to groups

The Linux model of three privilege types "user", "group", and "other" works well for authorization permissions
and we should use this model, albeit somewhat simplfied.  "User" is an authenticated user. "Group" is a set of
users, and a user may belong to several groups. In SNAC and the non-Linux world "group" is known as "role", so
SNAC will call them "roles". "Other" privileges apply to SNAC as public, non-authenticated users, although we
don't really have "other", and the "researcher" role applies to public users.

### Roles and privileged application features

Each feature has a list of one or more authorized roles which may access that feature.

Users can have several roles, and will have all the privileges (access to features) of all their roles. Role
membership is managed by an administrative UI (part of the dashboard) and related API code. Our system allows
access to every feature associated with any user role. (Just an aside: some high-security systems restrict
access to the least privileged role; like Linux, SNAC has a different model.) User information such as name,
phone number, and even password can also change. User ID values cannot be changed, and a user ID is never
reused, even after account deletion.

We expect to create additional roles as necessary for application functions.

Roles include a large number "is instution member" roles. These should be roles like any other, but we may
want to flag these role records to make them easy to manage and easy to display in the UI. Any user can have
zero or more roles that define their instutional affiliation. This primarily effects reporting and admin. In
the case of reports, membership in an institution constrains the reporting. When setting up a report, users
may only choose from institutions of which they are members. Some reports may auto-detect the user's
membership.

By and large when we refer to "accounts" we mean web accounts managed by the Manager/Web admin. The general
public can use the discovery interface without an account, but saving search history, and other
session related discovery tools requires an account. It is technically possible to have a single session
dashboard. Although that has not been mentioned as a requirement and is probably a low priority, it might be
almost trivial to implement.

### Tables of roles and user types

Every account will be in the "Researcher" role which has the same privileges as the general public, but with a
TBD set of basic privileges including: search history, certain researcher reports.


See the two tables "Role" and "User type" in the "DBUser API" documentation:

[Role in Database User API](Requirements/DBUser API.md#roles)

Remember: institutional affiliation roles aren't in the tables. There will be many of those institutional
affiliation roles, and users may have zero, one, or several institutional roles that define insitutions
with which the user is affiliated.

It is possible for an institutional administrator to also be a member of more than one
institution. Institutional Admins have abilities:

- view membership lists of their institution(s)
- add or remove their instutional role for users.

Roles which require one or more instutitutional roles (affiliation):

- Block upload
- Name authority
- Institutional admin

Roles which may have zero or more institutional roles:

- Web admin
- Vetting
- Moderator
- Maintenance (likely to have one or more)
- Researcher


There are several dashboard sections:

- Standard researcher history
- Standard user account management (password, email, etc.)
- Web admin account creation, deletion, role assignments
- Vetting admin (if we have vetting)
- Available reports.
