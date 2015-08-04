
Big questions
---

- (solved) how is gitlab backed up?

  - Shayne backs up the whole gitlab VM.


Overview and order of work
---


1. create tech documents, filling in as much prose as possible

    - how do we deal with vocabulary hierarchy? 
    

'''
INSERT INTO vocabulary (type, value) values ( 'subject', 'World politics--Periodicals' );
INSERT INTO vocabulary (type, value) values ( 'subject', 'World politics--Pictorial works' );
INSERT INTO vocabulary (type, value) values ( 'subject', 'World politics--Societies, etc.' );
INSERT INTO vocabulary (type, value) values ( 'subject', 'World politics--Study and teaching' );
'''


1. create prototype software to test tech requirements, iterate updating requirements and prototype

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
  
- SQL schema (Robbie)

  - exists, needs tests, needs requirements
  
  - should we re-architect tables become normal tables, the views go away, and versioned records are moved to shadow tables.
  
  - add features for delete-via-mark (as opposed to actual delete)
  
  - add features to support embargo
  
  - (maybe, discuss) change vocabulary.sql from insert to copy. It would be smaller and faster, although in reality as soon as
    it is in the database, the text file will never be touched again.
    
- (discuss) can/should we create a tag system to deal with ad-hoc requirements later in the project? The tags
  are very similar to vocabulary entryies in that the tag values are controlled. The difference being a weaker
  moderation of tags and more readiness to create new tags (types). The tag table would consist of tag, value
  and is essentially a name-value system. If we create tags, should we try to enforce some data typing, that
  is: string, int, date, float, etc.?

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
