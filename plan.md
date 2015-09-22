
#### Big questions


- (solved) how is gitlab backed up?

  - Shayne backs up the whole gitlab VM.

- We need a complete understanding of our requirements for controlled vocabulary, ontology, and/or tagging as
  well as how this relates to search facets. This also impacts our future ability to make assertions about the
  data, and is somewhat related to semantic net. See [Tag system](#controlled-vocabularies-and-tag-system).
  
### Documents we need to create

- Operations and Procedure Manual

- Formal Requirements Document

- Formal Specification Document

- Research Agenda

- User Story Backlog

- Design Documents (UI/UX/Graphic Design)
  
      - ideally someone writes a (possibly brief) style guide
      
      - a set of .psd or other images is not a style guide

### Governance and Policies, etc.

- Data curation, preservation, graceful retirement

- Data expulsion vs. embargo

- Duplicates, backups, restore, related policy and technical issues

- Broad pieces that are missing or underdeveloped [Laura]

- Refresh relationship with OCLC [John, Daniel]


### Overview and order of work

1. List requirements of the overall application. (done)

2. Organize requirements by component, clean up, flesh out, and vet. (requires team meetings)

3. Create formal specifications for each component based on the official, clean, requirements document. Prototypes will help ensure the formal spec is written correctly.

4. Define a timeline for development and prototyping based on the formal specifications document.

5. Create tests for test-driven development based on the formal specification.  This includes creating and mining ground-truth data.

6. Develop software based on formal specification that passes the given tests.



### Non-component notes to be worked into requirements 
- CPF record edit, edit each field

- CPF record split, split data into separate cpf identities, deprecate old ARK, mint new ARKs

- CPF record merge, combine fields, deprecate old ARKs, mint new ARK

### System Design

#### Developed Components

- Data validation engine
  - **API:** Custom JSON (needs formal spec)

  - The data validation engine applies a written system of rules to the incoming data.  The rules must be written in a human-readble form, such that non-technical individuals are able to write and understand the rules.  A rule-writing guide must be supplied to give hints and help for writing rules properly.  The engine will be pluggable and written as an MVC application.  The model will read the user-written rules (stored in a postgres database or flat-file system, depending on the model) and apply them to any input given on the view.  The initial view will be a JSON API, which accepts individual fields and returns the input with either validation suggestions or valid flags. 

  - rule based system abstracted out of the code
  - rules are data
  - change the rules, not the actual application code 
  - rules for broad classes of data type, or granular rules for individual fields
  - probably used this to untaint data as well (remove things that are potential security problems)
  - send all data through this API
  - every rule includes a message describing what when wrong and suggesting fixes
  - rules potentially editable by non-programmers
  - rules are based on co-op data policies, which implies a data policy document, or the rules **can** be the
    policy documentation.

- Identitiy Reconciliation (aka IR) (architect Robbie)
  - **API:** Custom JSON (needs formal spec) 

 - needs docs wrangled

- workflow manager (architect Tom)
  - **API:** Custom JSON? (needs formal spec)

  - exists, needs tests, needs requirements
    * **We need to stop now, write requirements, then apply those requirements to the existant system to ensure we meet the requirements**
  
  - needs to be integrated into an index.php script that also checks authentication
  
  - can the workflow also support the login.php authentication? (Yes).
  
- PostgreSQL Storage: schema definition (Robbie, Tom)
  - **API:** SQL

  - exists, needs tests, needs requirements
    * **We need to stop now, write requirements, then apply those requirements moving forward to ensure we meet the requirements**
  
  - should we re-architect tables become normal tables, the views go away, and versioned records are moved to shadow tables.
  
  - add features for delete-via-mark (as opposed to actual delete)
  
  - add features to support embargo
  
  - *maybe, discuss* change vocabulary.sql from insert to copy. It would be smaller and faster, although in reality as soon as
    it is in the database, the text file will never be touched again.
    
- discuss; Can/should we create a tag system to deal with ad-hoc requirements later in the project? [Tag system](#controlled-vocabularies-and-tag-system)

- CPF to SQL parser (Robbie)
  - **API:** EAC-CPF XML input, JSON output? (needs formal spec)

  - exists, needs tests, needs requirements
    * **We need to stop now, write requirements, then apply those requirements moving forward to ensure we meet the requirements**
  
- NameEntity serialization tool, selectable pre-configured formats

- NameEntity string parser

  - **API:** subroutine? JSON?

    - Can we find a grammar-based parser for PHP? Should we use a standalone parser?

    - Can we expose this as a JSON API such that it's given a name-string and returns an identity object of that identity's information?  Possibly a score as well as to how well we thought we could parse it?
- Name parser (only a portion of the NameEntity string) 
  - **API:** subroutine? JSON?
  - Can this use the same parser engine as the name string parser?

- Date parser
  - **API:** subroutine? JSON?

  - Can this use the same parser engine as the name string parser?
  - **This should be distinct, or may be a subroutine of the nameEntry-string parser**
  - Can we expose this as a JSON API such that given a set of dates, it returns a date object that lists out the individual dates?  Then, it could be called from the name-string parser to parse out dates.

- Editing User interface
  - **API:** HTML front-end, makes calls to internal JSON API
  - Must have ajax-backed interaction for displaying and searching large lists, such as cpfRelations for an identity.
    - We need to have UI edit/chooser widget for search and select of large numbers of options, such as the Joseph
      Henry cpfRelations that contain some 22K entries. Also need to list all fields which might have large numbers
      of values. In fact, part of the meta data for every field is "number of possible entries/reapeat values" or
      whatever that's called. From a software architecture perspective, the answer is 0, 1, infinite.

- History Research Tool (redefined)
  - **API:** HTML front-end, makes calls to internal JSON API
  - Needs to be reworked to support the Postgres backend

#### Off-the-shelf Components

- gitlab for developer documentation, code version management, internal issue tracking and milestone keeping

- github public code repository

- test framework, need to choose one

- authentication
  - session management, especially as applies to authentication tokens, cookies and something which prevents
    XSS (cross-site scripting) attacks
    
- JavaScript UI component tools, JQuery; what others?
  - Suggestions: bootstrap, angular JS, JQueryUI

- reports, probably Jasper

- PHP, Postgres, Linux, Apache httpd, etc.

- language modules ?



#### Controlled vocabularies and tag system 

Tags are simply terms. When inplemented as fixed terms with persistent IDs and some modify/add policy, tags
become a flat (non-hierarchal) controlled vocabulary.

The difference being a weaker moderation of tags and more readiness to create new tags (types). The tag table
would consist of tag term and an ID value. Tag systems lack data type, and generally have no policy or less
restrictive policies about creating new tags

Below are some subject examples. It is unclear if these are each topics, or if "--" is used to join granular
topics into a topic list. Likewise it is unclear if this list relies on some explicit hierarchy. 
  
```
American literature--19th century--Periodicals
American literature--20th century--Periodicals
Periodicals
Periodicals--19th century
World politics--Periodicals
World politics--Pictorial works
World politics--Societies, etc.
World politics--Study and teaching
```

**RH: I agree, this is super tricky.  We need to distinguish between types of controlled vocab, so that we don't mix Occupation and Subject.  A tagging system might be very nice for at least subjects.**

