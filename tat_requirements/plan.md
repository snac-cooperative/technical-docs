
Big questions
---

- (solved) how is gitlab backed up?

  - Shayne backs up the whole gitlab VM.


Overview and order of work
---


1. create tech documents, filling in as much prose as possible

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

- CPF to SQL parser (Robbie)

  - exists, needs tests, needs requirements
  
- Name string parser

    - Can we find a grammar-based parser for PHP? Should we use a standalone parser?

- Date parser

  - Can this use the same parser engine as the name string parser?

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
