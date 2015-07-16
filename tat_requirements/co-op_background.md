Gap analysis
------------

This is gap analysis between the current and SNAC2-prototype. Perhaps
this should be in the Required and Planned Functionality below.

Data maintenance
----------------

A goal of the pilot phase it to demonstrate cooperative maintenance of
the data resource.  The prototype does not have robust support for
maintaining the corpus of EAC-CPF identity documents.

While the current architecture supports the incremental addition of new
records through input to the match/merge system there is no way to
directly create a record for a known new identity.  A workflow that
includes an archival description operator looking up identities in the
system; and creating a new record if the identity they are trying to
reference does not exist; must be supported.^[[g]](#cmnt7)^ The new
maintenance system will allow creation of a new identity record via the
web interface.

With the current architecture, textual information that comes from the
un-merged EAC-CPF documents - such as biographical history notes - could
be maintained by editing the un-merged EAC-CPF source and re-exporting
the merged EAC-CPF document.  New information could be added to the
merged documents by adding another un-merged document into the input
directory.  New un-merged EAC-CPF documents containing ARKs could be
matched directly, bypassing the match/merge searching.  It is less clear
how factual information that comes from VIAF could be modified in the
current prototype.

Early research indicates the most import section of a merged EAC-CPF
document is the section that contains links to primary and secondary
research materials about the named identity. The overarching goal of
archival description it to enable researchers to find materials relevant
to their interests. In the current prototype, the only mechanism to add
a new link into the “Archival Collections” (a more accurate label would
be “Archival Materials”) or “Related Resources” sections of links would
be to generate an un-merged EAC-CPF record with the links and add run
that through the match/merge system.

The current prototype maintains information about relationships between
the entities described by the merged EAC-CPF documents.  Currently,
“associated with” and “corresponded with” are the only two relationship
types supported.  Early research suggests that archival description
practitioners are interested in using a more extensive vocabulary of
relationship types.

[ problem of linking to EAD if the URLs are not known ]

    •    establish procedures to accepting more batch submissions

    •    XTF is not designed to handle so many documents; XTF will can
not be run in a “clustered” mode; must scale up, not scale out

    •    Cheshire II does not have a Open Source Initiative certified
license

Pilot phase architecture
------------------------

Alternative 1^[[h]](#cmnt8)^
----------------------------

The most expeditious way to launch a pilot phase would be to leave the
basic technical architecture of the prototype in place, and to focus
initial energies into establishing policies and procedures that work
within the constraints of this architecture.  Two key systems that would
need to be set up for this approach to work are a customer relationship
management (CRM) system and ticketed help desk.

Customer relationship management systems have historically be used as a
sales support tool.  Information on current and potential customers,
including contact information and institutional affiliation, are
maintained in a database.  All pilot members institutions and designated
contacts should be entered into a CRM system for the pilot phase.  All
correspondence, call, contracts and agreements with accepted and
potential pilot phase members should be logged or stored in the CRM
system.^[[i]](#cmnt9)^

The CRM system should support or integrate with a help desk that issues
work ticket numbers. ^[[j]](#cmnt10)^ Any addition or change in the
maintained corpus of merged EAC-CPF records will require a work ticket
number.  Expectations for response times for issued tickets should be
established, clearly communicated, and measured for compliance.  A
customer service manager^[[k]](#cmnt11)^ will actively monitor the queue
of work tickets pending.  An operations manual will be maintained so
that the customer service manager or any additional first tier support
staff will be able to handle a set of ticket types.  If a procedure for
the request is not yet documented in the operations manual - or if the
manual indicates this is a task for second tier - then the ticket will
be escalated to the second tier support programmer.  The second tier
support programmer will have the technical skills to manipulate the
technical infrastructure; such as through editing XML files or directly
altering the database.  The second tier support programmer would also be
responsible for performing data extraction and normalization of non
EAC-CPF data sources processed during the pilot phase.^[[l]](#cmnt12)^ 
The volume and type of tickets will help establish priorities for
establishing procedures that can be automated for first tier support and
for future phases that do not require pilot members to contact the help
desk and obtain work tickets.

An automated way to establish a new identity should established early in
the pilot phase, so that participants can mint a new ARK identifier
without creating a work ticket.  Initially, a work ticket would still be
generated once the participant was ready to submit the new record though
the match/merge process.

Given the importance of maintaining links from the merged EAC-CPF record
to related resources, a link harvesting protocol should be developed
early in the pilot phase.  When a pilot phase participant identifies a
match in SNAC with a name they have in a collection description; the
link harvesting protocol would specify how to publish that link in their
HTML display of their collection description or through some other
mechanism (perhaps through an extension to the sitemap protocol, along
the lines of how ResourceSync works).  Procedures would be established
to then notify SNAC to harvest links from the participant, and the SNAC
“related collections” section would be automatically updated.  Such
updates would be based on a “linked data” technology rather than the
submission of XML files.

Alternative 2
-------------

Pure XML architecture for edits (edit the merged EAC-CPF records, maybe
with something like xEAC and with the merged files in revision control.
 This might make export from the match/merge challenging)

Alternative 3
-------------

Pure RDF architecture

Current State Conclusion (All, Daniel, Tom)
-------------------------------------------

The current systems functions well enough for researchers and other
stakeholders to see large data sets fully processed. These systems will
benefit from additional work to make them more mature in the usual ways
that software develops: robustness, testing and QA, documentation,
examples, consistent API. Most of the current software will be used in
the production product.

Required and Planned Functionality (All authors)
================================================

(We need to break out each item into UI functionality, and API
functionality.)

Expanded CPF schema requirements
--------------------------------

Provenance and history of each element/attribute.

Unique ID per element of CPF if that element is editable.

Version control on a per-element basis.

Expanded Database Schema
------------------------

The current database (Postgres) is sufficient for the current project
only. It will expand, and the expansion will probably be fairly
dramatic. We need to determine what tables and fields are necessary to
support additional functions. Each section of this document may need a
“data” section, or else this database schema section needs to address
every functional and UI aspect of all APIs that have anything to do with
the database.

Each field within CPF may (will?) need provenance meta data. Likewise
many fields in the database may need data for provenance.

The database needs audit trail ability to a fairly granular (field)
level. Audit is a new table at the very least. It seems likely that
nearly every table will gain some audit related fields.

Will database records be versioned? How is that handled? Seems like it
may be done via versioning table and some interesting joins. We need to
evaluate the various standard methods for database internal versioning.

CPF record has links to a “watch” table so users can watch each record,
and can watch for certain types of changes. Need UI for the watch
system. Need an API for the watch system.

Need a user table, group table, probably a group permission table so
that permissions are hard code with groups. We also want to allow
several permissions per group. Need UI for user, group, and
group-permission management.

If we create a generalized workflow system (as opposed to an ad-hoc
linked set of reports) then we need workflow tables. The tables would
establish workflow paths, necessary permissions, and would be linked to
users and groups.

Need fields to deal with delete/embargo. This may be best implemented
via a trigger or perhaps a view. By making what appear to be simple
SELECTs through a view, the view can exclude deleted records. We must
think about how using a view (or trigger) will effect UPDATE and INSERT.
Ideally the view is transparent. Is there some clever way we can
restrict access to the original table only via the view?

Need record lock on some types of records. This lock needs to be honored
by several modules, so like “delete”, lock might best be implemented via
a view and we \*only\* access the table in question via the view.

If there are different levels of review for different elements in the
record, then we need extra granularity in the workflow or the edited
record info to know the type of record edited apropos of workflow
variations.

If there different reviewers for different parts of the record, then
workflow data (and workflow configuration) needs to be able to notify
multiple people, and would have to get multiple reviewer approvals
before moving to the next phase of the workflow.

Institutional affiliation is probably common enough to want a field in
the user table, as opposed to creating a group for each institution. The
group is perhaps more generalized and could behave identical (or almost
identical) to a field (with controlled vocabulary) in the user table.

Make sure we can write a query (report) to count numbers of records
based type of edit, institution of the editor, and number of holdings.

If we want to be able to quickly count some CPF element such as outgoing
links from CPF to a given institution, then we should put those CPF
values into the SQL database, as meta data for the CPF record.

What is: How many referral links to EAC records that they created?

Be able to count record views, record downloads. Institutional dashboard
reports need the ability to group-by user, or even filter to a specific
user.

Reporting needs to help managers verify performance metrics. This
assumes that all changes have a date/timestamp. Once workflow and
process decisions are set, performance requirements for users such as
load/performance (how many updates and changes to records can be handled
at once), search response time, edit time (outside of review workflow),
and update times need to be set.

Effort reporting to allow SNAC and participants to communicate to others
the actual level of effort involved. This sounds like a report with time
span and numbers of records handled in various ways. SNAC might use this
when going from pilot into production so that everyone knows what effort
will be required for X number of records/actions (of whatever action
type).

Time/activity reporting could allow us to assess viability, utility, and
efficiency of maintenance system processes.

Similar reports might be generated to evaluate the discovery interface.
Something akin to how much time was required to access a certain number
of records. Rachael said: Assess viability of access funtionality-
performance time, available features, and ease of use.

We could try to report on the amount of training necessary before a new
user was able to work independently in each of various areas (content
input, review, etc.)

Introduction to Planned Functionality
-------------------------------------

The current system works, but is somewhat skeletal. It requires careful
attention from the developers to run the data processing pipelines. It
lacks administrative controls and reporting. Existing software
development process follows modern agile practices, but the some
processes are weak or incomplete. The research tools are somewhat
rudimentary. It needs infrastructure where domain experts can correct
and update merged authority descriptions.

The functional requirements below specify in detail all of the
capabilities of the new [production?] system. A separate section about
user interface (UI) specifies the visual/functional aspects of the UI
and includes discussion of the user experience (UX). Some of the
functional requirements exist only to support actions of the UI, and
UI-related functions should exist in their own independent API.

Software development, processes, and project management
-------------------------------------------------------

Choices for programming languages, operating system, databases, version
control, and various related tools and practices are based on extensive
experience of the developer community, and a complex set of requirements
for the coding process. Current best practices are agile development
using practices that allow programmers wide leeway for implementation
while still keeping the processes manageable.

Test-driven development ideally means automated testing, with careful
attention to regression testing. It takes some extra time up front to
write the tests. Each test is small, and corresponds to small sections
of code where  both code and text can be quickly created. In this way,
the software is kept in a working state with only brief downtimes during
feature creation or bug fixes. Large programs are made up of
intentionally small functions each of which is tested by a small
automated test.

Regression testing refers to verifying that old bugs do not reappear.
Every bug fix has a corresponding test, even if the function in question
did not originally have a test for the bug. Each new bug needs a new
test. Bugs frequently reappear, especially in complex sections of code.

Source code version control is vital to both development process, and to
the release process. During development, frequent small changes are
checked-in to the version control, along with a meaningful comment. The
history of the code can be tracked. This occasionally helps to
understand how bugs come into existence. In the Git system, the history
command is “blame”, a bit of programmer dark humor where the history is
used to know who to blame for a bug (or any undesirable feature).

Moving code into Quality Assurance (QA) and then into the production
environment are both integral with source code management. Many version
control systems allow tagging a release with a name. The collected
source code files are marked as a named (virtual) collection, and can be
used to update a QA area. Human testing and review happens in QA. After
QA we have release. Depending on the nature of the system release can be
quite complex with many parties needing to be notified, and coordination
across groups of developers, sysadmin, managers, support staff, and
customers. Agile development tends towards small, seamless releases on a
frequent (weekly or monthly) basis where communication is primarily via
update of electronic documentation. The process needs to assure that
fixes and new features are documented. The system must have tools to see
the current version of the system with its change log, as well as
comparing that to previous releases. All of these are integrated with
change management.

Bug reporting and feature requests fall (broadly speaking) into the
category of change management. Typically a small group of senior
developers and stakeholders review the bug/feature tracking system to
assign priorities, clarify, and investigate. There are good
off-the-shelf systems for tracking bugs and feature requests, so we have
several choices. This process happens almost as frequently as the
features/bug fix coding work of the developers. That means on-going,
more or less continuous review of fix/features requests every few days,
depending on how independent the developers are. Agile applies to
everyone on the project. Ideal change management is not onerous. As
tasks are completed, someone (developers) update feature status with “in
progress”, “completed” and so on. There might be additional status
updates from QA and release, but SNAC probably isn’t large enough to
justify anything too complex.

QA and Related Tests for Test-driven Development (Tom, Brian, Ray)
------------------------------------------------------------------

The data extraction pipelines manage massive amounts of data, and
visually checking descriptions for bugs would be inefficient if not
infeasible. The MARC extraction process is verified by just over 100
quality assurance descriptions. The output produced from each
description is checked for some specific value that confirms that the
code is working correctly and historical bugs have not reappeared. The
EAD extraction has a set of QA files, but the output verification is not
yet automated. A variety of file counts and measures of various sorts
are performed to verify that descriptions have all been processed. All
CPF output is validated against the Relax NG schema. Processing log
files are checked for a variety of error messages. Settings used for
each run are recorded in documentation maintained with the output files.
The source code is stored in a Subversion repository.

Our disaster recovery processes must be carefully documented.

The match/merge process is validated by …

Required new features
---------------------

The majority of new features will be in two areas: the maintenance
system, and the administration system. None of this code exists. The
maintenance system has a web UI and a server-based back end that
interacts with the same database used by the match-merge. The
maintenance system also requires an authentication system (login) that
allows us to manage the extensive collaborative efforts. The current
processing of data is accomplished only on servers at the command line,
and is handled directly by project programmers. In the new maintenance
system, that will be driven by content experts via a web site, and
therefore must expect the issues of authentication and authorization
inherent in collaborative data manipulation web applications.

The system will require reports. These will cover broad classes of
issues related to managing resources, usage statistics, administration,
maintenance, and some reports for end user researchers.

(Fill in prose introducing the other subsystems such as reporting)

One important aspect of the project is long-term viability and
preservation. We should be able to export all data and metadata in
standard formats. Part of the API should cover export facilities so that
over time we can easily add new export features to support emerging
standards.

The ability to export all the data for preservation purposes also gives
us the ability to offer bulk data downloads to researchers and
collaborating peer institutions.

Documentation (all authors)
---------------------------

Every aspect of the system requires documentation. Most visible to the
public is the user interface for discovery. Maintenance will be
complicated, and our processes are somewhat novel, so this will need to
be extensive, well illustrated with screenshots, and carefully tested.

Documentation intended for developers might be somewhat sparse by
comparison, but will be critical to the on-going software development
process. All the databases, operating system, httpd and other servers
need complete documentation of installation, configuration, deployment,
starting, stopping, and emergency procedures.

It is probably wise to choose a wiki-like documentation system at the
outset of the project.

