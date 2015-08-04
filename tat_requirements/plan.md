
Big questions
---

- (solved) how is gitlab backed up?

  - Shayne backs up the whole gitlab VM.

- We need a complete description of controlled vocabulary hierarchy. It seems pretty clear that some
  controlled vocabularies need to have n sub-vocabularies. And some subclasses can probably appear with
  several super-classes. 'Periodicals' appears in around 400 subjects. It also appears that the order of sub
  and super is not well defined. In some cases 'Periodicals' is the final subject, and in other cases the
  first subject. Curiously, topical subject bears a strong resemblence to the "tags" suggested below in [Tags](#tag-system).
  
```
INSERT INTO vocabulary (type, value) values ( 'subject', 'American literature--19th century--Periodicals' );
INSERT INTO vocabulary (type, value) values ( 'subject', 'American literature--20th century--Periodicals' );
INSERT INTO vocabulary (type, value) values ( 'subject', 'Periodicals' );
INSERT INTO vocabulary (type, value) values ( 'subject', 'Periodicals--19th century' );
INSERT INTO vocabulary (type, value) values ( 'subject', 'World politics--Periodicals' );
INSERT INTO vocabulary (type, value) values ( 'subject', 'World politics--Pictorial works' );
INSERT INTO vocabulary (type, value) values ( 'subject', 'World politics--Societies, etc.' );
INSERT INTO vocabulary (type, value) values ( 'subject', 'World politics--Study and teaching' );
```


Overview and order of work
---


1. create tech documents, filling in as much prose as possible

1. create prototype software to test tech requirements, iterate updating requirements and prototype

1. create tests for test driven development, and validate prototype

1. refactor or rewrite prototype to match requirements

1. create version 1 of software


Code we write<a name="code-we-write"></a>
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
  
  - *maybe, discuss* change vocabulary.sql from insert to copy. It would be smaller and faster, although in reality as soon as
    it is in the database, the text file will never be touched again.
    
- discuss; Can/should we create a tag system to deal with ad-hoc requirements later in the project? [Tags](#tag-system)

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


Tag system
---

The tags are very similar to vocabulary entryies in that the tag values are controlled. The difference being a
weaker moderation of tags and more readiness to create new tags (types). The tag table would consist of tag,
value and is essentially a name-value system. If we create tags, should we try to enforce some data typing,
that is: string, int, date, float, etc.?


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
