
#### Big questions


- (solved) how is gitlab backed up?

  - Shayne backs up the whole gitlab VM.

- We need a complete understanding of our requirements for controlled vocabulary, ontology, and/or tagging as
  well as how this relates to search facets. This also impacts our future ability to make assertions about the
  data, and is somewhat related to semantic net. See [Tag system](#controlled-vocabularies-and-tag-system).
  

Overview and order of work
---


1. create tech documents, filling in as much prose as possible
   - currenly on-going

1. create prototype software to test tech requirements, iterate updating requirements and prototype
   - Work flow engine is working and has both a command-line and web interface
   - We have a SQL database schema

1. create tests for test driven development, and validate prototype

1. refactor or rewrite prototype to match requirements

1. create version 1 of software


Code we write
----

- Identitiy Reconciliation (aka IR) (architect Robbie)

 - needs docs wrangled

- workflow manager (architect Tom)

  - exists, needs tests, needs requirements
  
  - needs to be integrated into an index.php script that also checks authentication
  
  - can the workflow also support the login.php authentication? (Yes).
  
- SQL schema (Robbie, Tom)

  - exists, needs tests, needs requirements
  
  - should we re-architect tables become normal tables, the views go away, and versioned records are moved to shadow tables.
  
  - add features for delete-via-mark (as opposed to actual delete)
  
  - add features to support embargo
  
  - *maybe, discuss* change vocabulary.sql from insert to copy. It would be smaller and faster, although in reality as soon as
    it is in the database, the text file will never be touched again.
    
- discuss; Can/should we create a tag system to deal with ad-hoc requirements later in the project? [Tag system](#controlled-vocabularies-and-tag-system)

- CPF to SQL parser (Robbie)

  - exists, needs tests, needs requirements
  
- Name serialization tool, selectable pre-configured formats

- Name string parser

    - Can we find a grammar-based parser for PHP? Should we use a standalone parser?

- Date parser

  - Can this use the same parser engine as the name string parser?

- CPF record edit, edit each field

- CPF record split, split data into separate cpf identities, deprecate old ARK, mint new ARKs

- CPF record merge, combine fields, deprecate old ARKs, mint new ARK

- coding style, class template (architect Robbie)

- We need to have UI edit/chooser widget for search and select of large numbers of options, such as the Joseph
  Henry cpfRelations that contain some 22K entries. Also need to list all fields which might have large numbers
  of values. In fact, part of the meta data for every field is "number of possible entries/reapeat values" or
  whatever that's called. From a software architecture perspective, the answer is 0, 1, infinite.


Controlled vocabularies and tag system 
---

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


Code we use off the shelf
---

- gitlab for docs, code version management, issue tracking(?)

- github public code repository?

- test framework, need to choose one

- authentication
  - session management, especially as applies to authentication tokens, cookies and something which prevents
    XSS (cross-site scripting) attacks
    
- JavaScript UI component tools, JQuery; what others?

- reports, probably Jasper

- PHP, Postgres, Linux, Apache httpd, etc.

- language modules
