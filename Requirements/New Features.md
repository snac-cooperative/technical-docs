# Required New Features

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
