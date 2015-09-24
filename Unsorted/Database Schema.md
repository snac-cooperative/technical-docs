#### Expanded Database Schema

The database schema has been rewritten to capture all the data in CPF files, as well as meet the various data requirements.


Each field within CPF may (will?) need provenance meta data. Likewise many fields in the database may need
data for provenance. This has not been done, and the developers need policy on provenance, as well as
examples. There seems to be little or no mention of provenance in Rachael's UI requirements.

The new schema has full versions of all records for all time. If not implemented, this is planned. The version
table records each table name, record id, user id who modified, and time datestamp. No changes were made to
existing tables, although existing tables may have gotten a field to distinguish old from current
records. The implementation may change.

Every record has a unique id. The watch system is a query run on some schedule (daily, hourly, ?) that checks
to see if a watched record has changed. CPF record has links to a “watch” table so users can watch each
record, and can watch for certain types of changes. Need UI for the watch system. Need an API for the watch
system.

Need a user table, group (role) table, probably a group permission table so that permissions are hard code
with groups. We also want to allow several permissions per group. Need UI for user, group, and
group-permission management.

We have created a generalized workflow system (as opposed to an ad-hoc linked set of reports). There is a work
flow state table which needs to be moved into the database.

Need fields to deal with delete/embargo. This may be best implemented via a trigger or perhaps a view. By
making what appear to be simple SELECTs through a view, the view can exclude deleted records. We must think
about how using a view (or trigger) will effect UPDATE and INSERT.  Ideally the view is transparent. Is there
some clever way we can restrict access to the original table only via the view?

Need record lock on some types of records. This lock needs to be honored by several modules, so like “delete”,
lock might best be implemented via a view and we \*only\* access the table in question via the view.

If there are different levels of review for different elements in the record, then we need extra granularity
in the workflow or the edited record info to know the type of record edited apropos of workflow variations.

If there different reviewers for different parts of the record, then workflow data (and workflow
configuration) needs to be able to notify multiple people, and would have to get multiple reviewer approvals
before moving to the next phase of the workflow.

Institutional affiliation is probably common enough to want a field in the user table, as opposed to creating
a group for each institution. The group is perhaps more generalized and could behave identical (or almost
identical) to a field (with controlled vocabulary) in the user table.

Make sure we can write a query (report) to count numbers of records based type of edit, institution of the
editor, and number of holdings.

If we want to be able to quickly count some CPF element such as outgoing links from CPF to a given
institution, then we should put those CPF values into the SQL database, as meta data for the CPF record.

What is: How many referral links to EAC records that they created?

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
