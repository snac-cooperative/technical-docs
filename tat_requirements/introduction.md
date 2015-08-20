#### TAT Functional Requirements

Before reading this you should have read:

[Plan](plan.md) (External, broad view roadmap)

[Co-op Background](co-op_background.md)  (Currrent state and background explanations)

[Introduction ](introduction.md) (This document. The technical requirements)

[Requirements](requirements.md) (Tech requirements from Rachael's spreadsheets)

#### Introduction to Planned Functionality

The functional requirements below specify in detail all of the
capabilities of the new [production?] system. A separate section about
user interface (UI) specifies the visual/functional aspects of the UI
and includes discussion of the user experience (UX). Some of the
functional requirements exist only to support actions of the UI, and
UI-related functions should exist in their own independent API.

#### Software development, processes, and project management


Choices for programming languages, operating system, databases, version
control, and various related tools and practices are based on extensive
experience of the developer community, and a complex set of requirements
for the coding process. Current best practices are agile development
using practices that allow programmers wide leeway for implementation
while still keeping the processes manageable.

Test-driven development ideally means automated testing, with careful
attention to regression testing. It takes some extra time up front to
write the tests. Each test is small, and corresponds to small sections
of code where both code and text can be quickly created. In this way,
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
tasks are completed, someone (developers) update feature status with "in
progress", "completed” and so on. There might be additional status
updates from QA and release, but SNAC probably isn't large enough to
justify anything too complex.

#### QA and Related Tests for Test-driven Development


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

#### Documentation

System documentation is in http://gitlab.iath.virginia.edu in markdown files.

Every aspect of the system requires documentation. Most visible to the public is the user interface for
discovery. Maintenance will be complicated, and our processes are somewhat novel, so this will need to be
extensive, well illustrated with screenshots, and carefully tested.

Documentation intended for developers might be somewhat sparse by comparison, but will be critical to the
on-going software development process. All the databases, operating system, httpd and other servers need
complete documentation of installation, configuration, deployment, starting, stopping, and emergency
procedures.

#### Required new features


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

- Web application (architect: Robbie)

The web application is a wrapper for all the APIs. It can have an API of it own, or not. It handles all http
requests, validating the data, deciding what needs to be done, doing real work, and handing some output back
to the user. Typically the output is HTML, but we are already planning for file downloads, and JSON data as
output from REST API calls. 

- Data validation API

Data from the web browser needs sanity checking and untainting before being handed to the rest of the
application. Initially the data validation API can consist of nothing more than untaining input from the
browser. We can add various checks and tests. We need to decide if the validation API can reject data, and if
it can, then it needs to interact with the work flow engine, the actual work flow, and whatever messaging
system we use to display messages to end users.

- Identitiy Reconciliation (aka IR) (architect: Robbie)

This API uses many aspects of identity, testing each against a target population of other identities. The
final anwser is a floating point number giving a match strength. IR has two modes of operation. Mode one
compares two identities and returns a match strength. Mode two compares a single identity againast the entire
database returning match strength. Mode two is somewhat unclear.

- workflow manager (Tom)

Every action the application can perform is part of the work flow. The names of these actions along with names
of their requisites are organized into a work flow table. The work flow engine does not know how to do real
work, but it does know the names of the functions which do the real work. A new feature (aka function, task)
is added to the application, by adding its name to the work flow, and creating a function of the same name in
the application. Likewise, requistes are determined by boolean functions, and every requisite must have a
matching function known to the work flow engine. The work flow enforces role-based behavior by testing the
requisites. The workflow engine exists, but needs to be ported from Perl to PHP, and the work flow data should
be stored in the SQL database.

- Support for work history and task staging. 

Editing consists of several stages of work that may be performed by different people and/or different
roles. We need database tables to support saving of work state data. Create a prototype table schema so we can
think about this problem and create a functional spec.

For an edit we need the CPF id, user id, timedate stamp, bitfield or work flow tags, optional user notes. For
search we need: user id, search string, timedate stamp. 

- SQL schema (Robbie, Tom)

All data is stored in a SQL database. Details are given elsewhere.
    
- Controlled vocabulary subsystem or API [Tag system](#controlled-vocabularies-and-tag-system)

We need controlled vocabulary for several data fields. This system handles all aspects of all controlled vocabularies.

- CPF to SQL parser (Robbie)

The input for the application is CPF files. These files need to be parsed into data fields and input into the
SQL database. This application exists, but needs some additional functionality.

- Name serialization tool, selectable pre-configured formats

Outputting name strings based on name data fields in the database is a tricky problem. There are several
output formats. The name serialization deals with this issue.

- Name string parser

Names in CPF files are currently strings. The CPF <part> element has been imported into the SQL database as a
string, but data needs require individual name components. Parsing names is a tricky problem, but several
parsers exist. We need to integrate one or more parsers, and perhaps tweak those parsers to handle the SNAC names.

- Date parser

We have several date parsers, but none are fully comprehensive. We can use the existing parsers, but they need
to be integrated into a single, comprehensive parser.

- CPF record edit, edit each field

Record editing on the server is handled by a collection of functions. The specifications for this may evolve
in parallel to the code. We know that each field needs to be changed, but the details of work flow and data
validation have not been determined. Work flow and validation are both likely to change as the SNAC policies
evolve. There are UI requirements for editing.

- CPF record split, split data into separate cpf identities, deprecate old ARK, mint new ARKs

Record splitting requires a set of functions and UI requirements documented elsewhere.

- CPF record merge, combine fields, deprecate old ARKs, mint new ARK

Record merge requires a set of functions and UI requirements documented elsewhere.

- Object architecture, coding style, class template (architect Robbie)

We will have a specific architecture of the web application, and of the classes and objects involved.

- UI widgets, mostly off the shelf, some custom written. We need to have UI edit/chooser widget for search and
  select of large numbers of options, such as the Joseph Henry cpfRelations that contain some 22K
  entries. Also need to list all fields which might have large numbers of values. In fact, part of the meta
  data for every field is "number of possible entries/reapeat values" or whatever that's called. From a
  software architecture perspective, the answer is 0, 1, infinite.

One important aspect of the project is long-term viability and preservation. We should be able to export all
data and metadata in standard formats. Part of the API should cover export facilities so that over time we can
easily add new export features to support emerging standards.

The ability to export all the data for preservation purposes also gives us the ability to offer bulk data
downloads to researchers and collaborating peer institutions.

#### Web application overview

Some aspects of the web app aren't yet clear, so there are details to be worked out, and some large-ish
concepts to clarify. I'm guessing we will agree on most things, and one of us or the other will just concede
on stuff where we don't agree.

Requirements:

- expose an http accessible API that is viable for wget, browser <form>, and Ajax calls.

- Supported input format depends on the complexity of the requested operation. 

- Public functions require no authentication. Everything else must include authentication data.

Internal flow: 

1. validate the inputs. 

1. Somehow slice and dice the CGI params of the REST call into an abstracted request we can pass to the
internal API. I suppose that the external and internal APIs are very similar, but we almost certainly need
some level of symbolic reference aka abstraction. Each REST call has its requisite data. Some data is as
simple as a record id, and some will be fairly interesting json data structures.

1. The web app API does the tasks specified by the REST request and the work flow engine's directions.

    a. Every http request must go through the work flow engine so that the work flow is validated and
    managed. 

    a. Every web app has a work flow, but people mostly just cobble that together with a bunch of implied
functionality using conditionals and side-effect-full function calls. In our code, the internal API is 100%
work flow agnostic. 

    a. I can explain this in more detail, but it makes a huge improvement in the structure of the
application.

1. Create the output data object if it wasn't created by the functions doing the work.



The work flow engine needs a number of functions that read application data and return booleans so that the
work flow engine can detect the application's relevant state. I guess that sounds confusing because the work
flow engine has state, and the application has state. Those two types of state are vastly different and only
related to each other in that the work flow engine can detect the application's state. The internal API of the
web app has no idea that the work flow engine even exists. And the work flow engine knows what work needs to
be done, but has no idea how it will be done.This is a very lovely separation of concerns.

My strong preference is to use an existing template module for output. I have attached a CPF xml template
based on the Template Tookit http://www.template-toolkit.org/ for which there is a Perl module, and apparently
a Python module. Template modules are fairly common, so I'm almost certain we will have several to choose from
in PHP.

I don't know how you feel about web frameworks, but I don't like them. Too hard to work with and very little
functionality I need, and often they break MVC, and they generally made debugging nearly impossible. Much
better to use a select few modules to create a lightweight quasi-framework that is perfectly matched to our
needs.

The internal API does its work, and creates the data for the output. Output data is passed to a rendering
layer that relies on the template module. The only code that knows anything about rendering is the rendering
layer. To all the non-rendering code, there is only "output data" which does conform to a standard structure
(almost certainly an output data object). The rendering layer uses the output object, and the requested format
of the output (text, html, pdf, xml, etc.) to create the output. Happily, "rendering" is generally a single
function call. Create a template object, call its "render" method with two arguments: (1) template file name,
(2) the output data object. Default behavior is to write the output to stdout, but the render method can also
return the output in a variable so we can create an http download.

Templates are human created static files with placeholders. The template engine fills in the placeholders with
values from relevant parts of the output data. Clearly, the output data object and the template must share a
object/property naming convention. The template engine functionality has single value fields, looping over
input lists, and if statement branching based on input. But that's pretty much it. No work is done in the
template that is not directly concerned with filling in placeholders, not even formatting (in the sense of
rounding numbers, capitalizing strings, or adding html tags). Templates are valid documents of the output
type, except in rare cases. The attached template is well-formed XML.

The web app needs a file download output option as well as output to stdout.


#### Data background

The data is in a SQL database. Every piece of data is in a separate field to the extent that is practical.
Data is organized into fields (columns) records (rows) and tables. Fields related to each other are in the
same table. Every record has a unique, permananent, numerical id often called a "key" or "primary key". For
the SNAC Co-op we have decided that records are never overwritten during update. This is somewhat unusual, but
not unheard of. An update operation creates a new record identical to the old record except for updated
fields. All old records are available for viewing via special interface. The old records are invisible to
operations that are intellectually acting on "current" data.

#### What is "normal form" and what informs the database schema design?

Edgar F. "Ted" Codd created 12 rules (revised with a 13th rule) to clarify the Relational Database Management
System (RDBMS). 

https://en.wikipedia.org/wiki/Edgar_F._Codd

Breaking any of these rules weakens data integrity and the ability of the system to manage the data. An RDBMS
is not merely a bucket of data, but an entire eco-system for the management of data and data related
activities. Before Codd's work, databases were managed on an ad-hoc basis as collections of files with
links. It was a mess. Data was lost. Only the DBA knew how to find the data, and access methods could be very
different for data in different locations. Accessing data could also be extremely slow. In addition to
assuring the integrity of data, as well as managing it, relational database systems are very fast.

https://en.wikipedia.org/wiki/Codd%27s_12_rules

The "R" in RDBMS is "relational" and Codd invented the relational model of data. Key to relational data
modeling is "normal form".

https://en.wikipedia.org/wiki/Database_normalization

The RDBMS world generally uses third normal form. Lower levels of normalization create additional work for
data operations. Higher forms rarely show any improvements. The key concept of normalization is that a datum
only exists in one place. In the RDBMS world where SQL implements relational algebra, normal form is both
convenient and natural. In other venues such as paper ledgers, data stored in flat files, or in spreadsheets,
normal form can seem awkward.

#### Edit architecture requirements

All data is stored in the database as separate tables and fields. In theory, we can consider mixed markup, but
Brad Westbrook sugests we avoid mixed markup. From a data perspective, mixed markup is not a good
practice. Data is data, and the database schema can be modified to accomodate necessary data formats. How the
data is displayed is very much a separate issue. 

Prior to human edits, merged records can be algorithmically split by the computer, assuming we write code to
perform such a split. After human edit, a split must be performed by a human. It is a requirement that all
previous versions can be viewed (read-only) during the human-mediated split operation so the human can refer
back to previous information.

After human edits, rollback only applies to human edited versions. There is a fire-break where rollback cannot
cross from human edits back to machine-merged descriptions. The policy group needs to supply policy
requirements for the tech folks to implement.

The broad requirements for the application are: edit data, split records, merge records. Secondary features to
make the system useful include: work flow enforcement, search, reporting (including "watch" features),
administration, authorization (data privileges).

#### Expanded CPF schema requirements

- Provenance and history of each element/attribute.
  - add this to the schema

- Unique ID per element of CPF if that element is editable.
  - we have a unique id per record, and only one field of each type per unique id, so this is covered.
  
- Version control on a per-element basis.
  - already done, but Tom wants to consider an alternative implementation
  
  
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

#### Merge and watch

Note: Ask Robbie what the database architecture is to support merged records.

Users may "watch" an identity. If a file is being watched, and that file is part of an description (merged or
single) then the watch will apply to the results of human edits, regardless of which part of the description
was modified. It is possible for someone to wish to track a biogHist, but that biogHist could be completely
removed in lieu of an improved and updated description. We do not track individual elements in CPF. We only
track an entire description, regardless the watcher's motivation. The original motivation for watching might
no longer exist after an edit, and if so, the watcher can simply disable their watch. After each edit, all
watchers will get a notification. The watch does not apply to any single field, but to the entire description,
and therefore also to future descriptions which result from merging.

What happens to a watch on a merged description which is subsequently split? Does the watch apply to both
split descriptions or to neither description? Perhaps is it best to disable the watch, and inform the watcher
to re-apply to watch a specific record, along with links and helpful info to make it easy to add the new
watch.

#### Brian's API docs need to be merged in or otherwise referred to:

[https://gist.github.com/tingletech/4a3fc5f59e5af3054286](https://www.google.com/url?q=https%3A%2F%2Fgist.github.com%2Ftingletech%2F4a3fc5f59e5af3054286&sa=D&sntz=1&usg=AFQjCNEJeJexryBtHbvLw-WtFYjxP4VwlQ)

#### Not sure where to fit these topics into the requirements. Some of them may not be part of technical requirements:

Discuss. What is "as it is configured now"? Consider implementing linked data standard for relationship links
instead of having to download an entire document of links (as it is configured now.)

Discuss. This seems to be the controlled vocabulary issue. Sort by common subject headings across all of SNAC - right now SNAC has
subject headings that have been applied locally without common practice
across the entire corpus.

We probably need to build our own holdings authority.

We need to write code to get accurate holdings info from WorldCat records. All the other repositories will
have be handled on a case-by-case basis. Sort by holdings location. Sort by identity's activity location. Sort
and visualize a person through time (show dates for events in a person or organization's lifetime). Sort and
visualize an agency or organization as it changes over time.

Continue to develop and refine context widget.

Sort collection links. Add weighting to understand which collections have more material directly related to
identity. (How is this best handled programmatically or as an input by contributors- maybe both?).

Increase exposure of SNAC to general public by leveraging partnerships.  Suggested agreement with Wikipedia to
display Wikipedia content in SNAC biographical area and work with Wikipedia to allow for links to SNAC at the
bottom of all applicable identities. This would serve to escalate and drive traffic to SNAC.


