# Documentation

Documentation is best created as files (preferrably markdown format) in the repository, and in a relevant directory.

The currently-being-revised TAT requirements are found in the [tat_requirements](tat_requirements).


Note for TAT functional requirements: need to have UI widget for search of very long fields, such as the Joseph Henry cpfRelations
that contain some 22K entries. Also need to list all fields which migh have large numbers of values. In fact, part of the meta data for
every field is "number of possible entries/reapeat values" or whatever that's called.This wiki serves as the documentation of the SNAC technical team as relates to Postgres and storage of data.  Currently, we are documenting:
* the schema and reasons behind the schema, 
* methods for handling versioning of eac-cpf documents, and
* elastic search for postgres.

* Need a data constraint and business logic API for validating data in the UI. This layer checks user inputs against some set of rules and when there is an issue it informs the user of problems and suggests solutions. Data should be saved regardless. We could allow "bad" data to go into the database (assumes text fields) and flag for later cleaning. That's ugly. Probably better to save inputs in some agnostic repo which could be json, frozen data structures, or name-value pairs, or even portable source format. The problem is that most often data validation is hard coded into UI JavaScript or host code. Validation really should be configurable separate from the display of data, workflow automation, and data storage. While our database needs to do certain rudimentary sanity checks, the database can't be expected to send messages all the way back up to the UI layer. Nor should the database be burdened with validation rules which are certain to be mutable. Ideally, the validation rules would work in the same state machine framework as the workflow automation API, and might even share some code.

* Need a mechanism to lock records. Even mark-as-deleted records won't necessarily solve all our problems, so we might want records that are "live", but not editable for whatever reason. The ability to lock records and having the lock integrated across all the data-aware APIs is a good idea.

* On the topic of data, we will have user/group/other and r/w permissions, and all data-aware APIs should be designed with that in mind. 

* "Literate programming" Using MCV and the state machine workflow automation probably meets (and exceeds) Knuth's ideas of Literate programming. Look at his idea and make sure we aren't missing any key concepts.

* QA and testing needs to be several layers, one of which is simple documentation. We should have code that examines data for various qualities, which when done in a comprehensive manner will test the data for all properties described in the requirements. As bugs are discovered and features added, this data testing code would expand. Code should be tested on several levels as well, and the tests plus comments in the tests constitute our full understanding of both data and code.

* Entities (names) have ID values, ARKs and various kinds of persistent ids. Some subsystem needs to know how to gather various ID values, and how to generate URIs and URLs from those id values. All discovered ids need to be attached to the cpf identity in a table related_id. We will need to track the authority that issued the id, so we need an authority table. Perhaps it is best (as previously discussed) to create a CPF record for each authority, and use the CPF persistent ID as the authority identifier in the related_id table. 

```
create table related_id (
        ri_id auto primary key,
        id_value text,
        uri text,
        url text,
        authority_id int -- fk to cpf.id?
);

```


* Allow config of CPF output formats via web interface. For example, in the CPF generator, we can offer some format and config options such as name formats in <part> and/or <relationEntry>

  - include 4 digit fromDate-toDate for person
  - include dates for corporateBody
  - use "fl." for active dates
  - use "active" for active dates
  - use explicit "b." and "d."
  - only use "b." or "d." for single 4 digit dates
  - enclose date in parentheses
  - add comma between name and dates (applies only if there is a date)

and so on. In theory that could be done for all kinds of CPF variations. We should have a single checkbox for "use most portable CPF formats" although I suspect the best data exchange format is not XML, but SQlite, SQL INSERT statements, or json.

* Does our schema track who edited a record? If not, we should add user_id to the version table.

* We should review the schema and create rules that human beings follow for primary key names, and foreign key names. Two options are table_id and table_id_fk. There may be a better option
  * Option 1 is nice for people doing "join on", but I find "join on" syntax confusing, especially when combined with a where clause
  * Option 2 seems more natural, especially when there is a where clause. Having different field names means that field names are more likely to be unique, thus not requiring explicit table.field syntax. Option 2a will be used most of the time. Option 2b shows a three table join where table.field syntax is required.

```
-- 1
select * from foo as a, bar as b where a.table_id=b.table_id;

-- 2a
select * from foo as a, bar as b where table_id=table_id_fk;
-- or 2b
select * from foo as a, bar as b, baz as c where a.table_id=b.table_id_fk and a.table_id=c.table_id_fk;

```

* Identity Reconciliation has a planned "why" feature to report on why a match matches. It would be nice to also report the negative: why didn't this match X? I'm not even sure that is possible, but something to think about. 

