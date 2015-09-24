# User Management

Authentication is validating user logins to the system. Authorization is the related aspect of controlling
which parts of the system users may access (or even which parts they may know exist).

We can use OpenID for authentication, but we will need a user profile for SNAC roles and authorization. There
are examples of PHP code to implement OpenID at stackexchange:

http://stackoverflow.com/questions/4459509/how-to-use-open-id-as-login-system

OpenID seems to constantly be changing, and sites using change frequently. Google has (apparently) deprecated
OpenID 2.0 in favor of Open Connect. Facebook is using something else, but apparently FB still works with
OpenID. Stackexchange supports several authentication schemes. If they can do it, so can we. Or we can support
one scheme for starters and add others as necessary. The SE code is not open source, so we can't see how much
work it was to support the various OpenID partners.

Authorization involves controlling what users can do once they are in the system. That function is sort of
more solved by OAuth or OpenID by sharing the user profile. However, SNAC has specific requirements,
especially our roles, and those will not be found in other system. There is not anything we must have from
user profiles. We might want their social networking profile, but social networking is not a core function of
SNAC.

By default users can't do anything that isn't exposed to the non-authenticated public users. Privileges are
added and users are given roles (aka groups) from which they inherit privileges. The authorization system is
involved in every transaction with the server to the extent that every request to the server is checked for
authorization before being passed to the code doing the real work.

The Linux model of three privilege types "user", "group", and "other" works well for authorization permissions
and we should use this model.  "User" is an authenticated user. "Group" is a set of users, and a user may
belong to several groups. In SNAC and the non-Linux world "group" is known as "role", so SNAC will call them
"roles". "Other" privileges apply to SNAC as public, non-authenticated users, although we don't really have
"other", and the "researcher" role applies to public users.

Users can have several roles, and will have all the privileges of all their roles. Role membership is managed
by an administrative UI (part of the dashboard) and related API code. User information such as name, phone
number, and even password can also change. User ID values cannot be changed, and a user ID is never reused,
even after account deletion.

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

Every account will be in the "Researcher" role which has the same privileges as the general public, but with a
TBD set of basic privileges including: search history, certain researcher reports.


| User type                  | Role                | Description                                                           |
|----------------------------+---------------------+-----------------------------------------------------------------------|
| Sysadmin                   | Server admin        | Maintain server, backups, etc.                                        |
| Database Administrator     | DBA                 | Schema maintenance, data dumps, etc.                                  |
| Software engineer          | Developer           | Coding, testing, QA, release management, data loading, etc.           |
| Manager                    | Web admin           | Web accounts: create, manage, assign roles, run reports               |
| Peer vetting               | Vetting             | Approve moderators, reviewers, content experts                        |
| Moderator                  | Moderator           | Approve maintenance changes, posting those changes                    |
| Reviewer/editor            | Maintenance         | Maintainer privileges, interacts with moderators                      |
| Content expert             | Maintenance         | Domain expert, may have zero institutional roles                      |
| Documentary editor         | Maintenance         | Distinguished by?                                                     |
| Maintenance                | Maintenance         | Distinguished by?                                                     |
| Researcher                 | Researcher          | Use the discovery interface and history dashboard                     |
| Archival description donor | Block upload        | Bulk uploads of CPF or finding aids                                   |
| Name authority manager     | Name authority      | Donates name authority data perhaps via bulk upload                   |
| Institutional admins       | Institutional admin | Instutional role admin dashboard, institutional reports               |
| Public                     | Researcher          | No account, researcher role, no dashboard or single session dashboard |


Remember: institutional affiliation roles aren't in the table above. There will be many of those roles, and
users may have zero, one, or several institutional roles that define which insitutions that user is a member
of.

It is possible for an institutional admin to be a member of more than one institution. Institutional Admins
have abilities:

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
