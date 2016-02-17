# Overall Plan

### Table of Contents

* [Documents to Create](#documents-to-create)
* [Governance and Policy Discussion](#governance-and-policy-discussion)
* [Overview of Approach](#overview-of-approach-iterative)
* [CPF Requirements](#cpf-requirements)
* [System Design](#system-design)
  * [Developed Components](#developed-components)
  * [Off-the-shelf Components](#off-the-shelf-components)

### Documents to Create

- Operations and Procedure Manual
- Formal Requirements Documents
- Formal Specification Documents
- Research Agenda
- User Story Backstory
- Design Documents (UI/UX/Graphic Design)
    - style guides
    - layouts

### Governance and Policy Discussion

- Data curation, preservation, graceful retirement
- Data expulsion vs. embargo
- Duplicates, backups, restore, related policy and technical issues
- Broad pieces that are missing or underdeveloped [Laura]
- Refresh relationship with OCLC [John, Daniel]


### Overview of Approach (Iterative)

1. List requirements of the overall application.
2. Organize requirements by component, clean up, flesh out, and vet.
3. Create formal specifications for each component based on the official, clean, requirements document. Prototypes will help ensure the formal spec is written correctly.
4. Define a timeline for development and prototyping based on the formal specifications document.
5. Create tests for test-driven development based on the formal specification.  This includes creating and mining ground-truth data.
6. Develop software based on formal specification that passes the given tests.



### CPF Requirements

- CPF record edit, edit each field
- CPF record split, split data into separate cpf identities, deprecate old ARK, mint new ARKs
- CPF record merge, combine fields, deprecate old ARKs, mint new ARK

### System Design

#### Developed Components

- [Data validation engine](/Specifications/Data Validation.md)
    - **API:** Internal programming API (PHP)
        - Hooks should be made in the [Server API](/Specifications/Server API) and REST API

- [Identitiy Reconciliation](/Specifications/Identity Reconciliation.md)
    - **API:** Internal programming API (PHP)
        - Hooks should be made in the [Server API](/Specifications/Server API) and REST API

- [Server Workflow Engine](/Specifications/Workflow Engine.md)
    - **API:** Understands [Server API](/Specifications/Server API) and calls internal programming APIs of other components
    - PERL prototype exists
    - Needs formal description and specification documentation

- [PostgreSQL Storage](/Specifications/Schema SQL.md)
    - **API:** SQL, with custom internal programming API (PHP)
    - Supports versioning and mark-on-delete
    - Needs features to support embargo

- CPF Parser
    - **API:** EAC-CPF XML input, Constellation output
        - Output may be serialized into SQL or JSON [Constellation](/Specifications/Server API/Constellation.md)
    - [List of captured tags](/Specifications/Captured EAC-CPF Tags.md)

- CPF Serializer
    - **API:** Constellation input, EAC-CPF XML output

- NameEntity Serializer
    - electable pre-configured formats

- NameEntity Parser
    - **API:** Internal programming API (PHP)
    - Discussion
        - Can we find a grammar-based parser for PHP? Should we use a standalone parser?
        - Can we expose this as a JSON API such that it's given a name-string and returns an identity object of that identity's information?  Possibly a score as well as to how well we thought we could parse it?

    - Name parser (only a portion of the NameEntity string)
        - **API:** subroutine? JSON?
        - Can this use the same parser engine as the name string parser?

- Date parser
    - **API:** Internal programming API (PHP)
    - Discussion
        - Can we expose this as a JSON API such that given a set of dates, it returns a date object that lists out the individual dates?  Then, it could be called from the name-string parser to parse out dates.

- [Editing User Interface](/Specifications/Editing User Interface.md)
    - **API:** HTML front-end, makes calls to [Server API](/Specifications/Server API)
    - Must have ajax-backed interaction for displaying and searching large lists, such as relations for a constellation.
        - We need to have UI edit/chooser widget for search and select of large numbers of options, such as the Joseph Henry cpfRelations that contain some 22K entries. Also need to list all fields which might have large numbers of values.
    - Need a way of entering metadata about each piece of information

- History Research Tool
    - **API:** HTML front-end, makes calls to [Server API](/Specifications/Server API)
    - May be reworked to support the Postgres backend (later)
    - Retain functionality of current HRT

#### Off-the-shelf Components

The following OTS components will be evaluated and used in development

- GitLab
    - developer documentation,
    - code version management,
    - internal issue tracking, and
    - milestone keeping

- GitHub
    - public code repository

- PHPUnit unit test framework

- Authentication
    - Consider OAuth2 (Google, GitLab, etc) for session management, especially as applies to authentication tokens, cookies and something which prevents XSS (cross-site scripting) attacks

- JavaScript Libraries
    - JQuery
    - UI Components, such as Bootstrap
    - Graphing libraries, such as D3JS

- Reporting Tools
    - Investigate Jasper

- LAMP Stack (Linux, Apache 2, Postgres, PHP)
