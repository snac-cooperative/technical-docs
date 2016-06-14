# User Management

### Introduction

Authentication is: "validating user logins to the system." Authorization is: "privileges
allowing user access to system features."

We are using OpenID for authentication, but we need a user profile for SNAC privileges (authorization) and
roles (groups of privileges).

OpenID seems to constantly be changing, and sites using it change frequently. Google has (apparently)
deprecated OpenID 2.0 in favor of Open Connect. Facebook is using something else, but apparently FB still
works with OpenID. Stackexchange supports several authentication schemes. If they can do it, so can we. Or we
can support one scheme for starters and add others as necessary. The SE code is not open source, so we can't
see how much work it was to support the various OpenID partners.

### Authorization

Authorization involves controlling what users can do once they are in the system. That function is sort of
more solved by OAuth or OpenID by sharing the user profile. However, SNAC has specific privileges which will
not be found in any other system.

By default, the lowest privileged users can't do anything that isn't available to the non-authenticated public
users. Privileges are added to roles (groups) and users are given roles from which they inherit sets of 
privileges. The authorization system is involved in every transaction with the server to the extent that every
request to the server is checked for authorization before being passed to the code doing the real work. (Every
request is also checked for authentication as well, naturally.)

### Roles analagous to groups

The Linux model of three privilege types "user", "group", and "other" works well for authorization permissions
and we should use this model, albeit somewhat simplfied.  "User" is an authenticated user. "Group" becomes
"Role" and is a set of users, and a user may belong to several groups. Privileges are carried by roles, and
users are members of one or more roles. In SNAC and the non-Linux world "group" is known as "role", so SNAC
will call them "roles". "Other" privileges don't apply to SNAC. Non-authenticated users have the "researcher"
role.

### Roles and privileged application features

Each feature has a list of one or more authorized roles which may access that feature.

Users can have several roles, and will have all the privileges (access to features) of all their roles. Role
membership is managed by an administrative user interface (probably part of the dashboard, or there will be a
special admin dashboard) and related API code. Our system allows access to every feature associated with any
user role. (Just an aside: some high-security systems restrict access to the least privileged role, like
Linux; SNAC has a different model.) User information such as name, phone number, and even password can
change. User ID values cannot be changed, and a user ID is never reused, even after account deletion.

We expect to create additional roles as necessary for application functions.

Historically, there was a suggestion that users could be affiliated with multiple institutions. This is not
practical. Any user zero or one institutional affiliation. This primarily effects reporting and admin. In the
case of reports, membership in an institution constrains the some aspects of some reports. Certain reports may
auto-detect the user's institutional affiliation.

When we refer to "accounts" we mean web accounts managed by the Manager/Web admin. The general
public can use the discovery interface without an account, but saving search history, and other
session related discovery tools requires an account. It is technically possible to have a single session
dashboard. Although that has not been mentioned as a requirement and is probably a low priority, it might be
almost trivial to implement.

### Tables of privileges and roles

Every account will be in the "Researcher" role which has the same privileges as the general public, but with a
TBD set of basic privileges including: search history, certain researcher reports.

See the two tables "Privilege" and "Role" in the "DBUser API" documentation:

[Privilege table](Requirements/DBUser API.md#privilege_table)

[Role table](Requirements/DBUser API.md#role_table)

Remember: institutional affiliation roles listed. Users may have zero, or one role that define insitutions
with which the user is affiliated. Our current direction is not to support multiple institutional affiliations
because this creates many problems. User who have multiple affiliations need to have separate logins for each
affiliation.

Institutional Admins have abilities:

- view membership lists of their institution(s)
- add or remove their instutional role for users.

Privileges which require and instutitutional affiliation:

- Block upload
- Name authority
- Institutional admin

Roles which may have zero or more institutional roles:

- Web admin
- Vetting
- Moderator
- Maintenance (likely to have one or more)
- Researcher


There will be several dashboard sections or dashboards. For more detail, see the dashboard requirements and
specification. (link needed)

- Standard researcher history
- Standard user account management (password, email, etc.)
- Web admin account creation, deletion, role assignments
- Vetting admin (if we have vetting)
- Available reports.
