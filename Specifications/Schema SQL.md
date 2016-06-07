### Database schema and SQL queries

This file gives detail of schema decisions, and function of the major SQL queries. 

You may be interested in the large schema diagram: [Large schema png file](Specifications/Originals/Schema Diagram.png)

There is an interactive schema web site: [Schema web site](http://shannonvm.village.virginia.edu/~twl8n/schema_spy_output/)

### Table of contents

* [Constellations that are SNAC institutions](#constellations-that-are-snac-institutions)
* [Multiple names, alternate names, components](#multiple-names-alternate-names-components)
* [How dates are stored](#how-dates-are-stored)
* [How versioning works](#how-versioning-works)
* [Foreign key related records](#foreign-key-related-records)
* [Vocabulary table history and rationale](#vocabulary-table-history-and-rationale)
* [Vocabulary separate SQL file](#vocabulary-separate-sql-file)
* [Common fields and their meaning](#common-fields-and-their-meaning)
* [Identical text tables](#identical-text-tables)
* [Geographic authority](#geographic-authority)


### Constellations that are SNAC institutions

We need to add a new table snac_institutions which is a linking table identifying which constellations are
SNAC institutions. This is used by the user admin code to show/search institutional affiliation.


### Multiple names, alternate names, components

A constellation may have multiple entity names. All names are in table name related to the constellation by main_id
as with all first order data. Alternate names are names in all senses, and are only distinguished by having a
less than maximum preference score. We assume that "preferred" name is dependent on context, and that the context
is outside the name table. There will be a function that when given a constellation and a user's account will
return names in preferred order. That function may be complex, and its behavior will be determined by
policy. 

Existing names are parsed by software into components. Primarily, components exist to separate surname from
the rest of he components, and even then, surname is the "alphabetical listing" name. This concept varies
widely by local custom. The parser may create many components for a name, but as the concepts of "name" and
"component" are very fluid, this aspect is nothing more than a software guess. In fact, there may be no
canonical list or ordering of components. The database allows a single list and a single ordering. As policy
evolves this may change.

### How dates are stored

We have chosen to store date as a comprehensive date range record. This is a single record type, setting
attribute fields as appropriate, and leaving unused fields empty when necessary. Thus a single date and date
range are both the same record type, but the single date lacks a toDate, and is_range=false. Many fields of a
date range may be missing (and explicitly noted as missing). Lack of precision can be captured as well.

Date sets are handled relationally by using multiple date_range records. Relational data modeling needs no
"date set" table.

We did not consider separating fromDate and toDate into separate records. There is no gain in normalization,
and the queries would be more complex.

The date_range table:

```
create table date_range (
        id              int default nextval('id_seq'),
        version         int not null,
        main_id         int not null,
        is_deleted      boolean default false,
        is_range        boolean default false, -- distinguish 1 or 2 dates from a range with possibly missing bounds
        missing_from    boolean default false, -- from date is missing or unknown, only is_range=t
        from_date       text,                  -- date as an iso string
        from_type       int,                   -- (fk to vocabulary.id) birth, death, active
        from_bc         boolean default false, -- just in case we ever run into a BC date
        from_not_before text,
        from_not_after  text,
        from_original   text,                  -- from date tag value
        missing_to      boolean default false, -- to date is missing or unknown, only is_range=t
        to_date         text,
        to_type         int,                   -- (fk to vocabulary.id) birth, death, active
        to_bc           boolean default false, -- just in case we ever run into a BC date
        to_not_before   text,
        to_not_after    text,
        to_present      boolean,
        to_original     text,                  -- the to date tag value 
        fk_table        text,                  -- table name of the related foreign table. Exists only as a backup
        fk_id           int,                   -- table.id of the related record
        primary         key(id, version)
        );
```

To and from dates are ISO strings. to/from _not_before/_not_after are also ISO date strings and deal with lack
of precision. Many dates are simply not known exactly. Some dates are simply "3rd century" or "1800s". These
would be:

```
date 201, not_before 201-01-01T00:00:00Z, not after 300-12-31T24:00:00Z, 
date 1800, not_before 1800-01-01T00:00:00Z, not after 1899-12-31T24:00:00Z
```

Note the subtle 1 year offset between "1800s" and "19th century".

Using not_before and not_after also allows us to capture month and day lack of precision.

To/From _original is the parsed (or user entered) portion of the date. This is important for CPF import, but
may have limited use for dates entered in the user interface. We anticipate enforcing some date data entry
rules in the user interface so that there's no guessing (or minimal guessing) later on.

from_bc and to_bc identify dates before the common epoch. All date values are represented as positive numbers
to facilitate date math. 

is_range=false for a single fromDate as a single date. This is used when the intention is to capture a single
event date such as a wedding date, or date of laying a cornerstone. 

missing_from=true and missing_to=true to denote that from or to do exist, but are unknown. This deals with
cases where either birth or death date is unknown. Every person is known to have been born, but there may be
florishe date with a known toDate, but missing fromDate. It is possible to know a person is deceased without
knowing the date. from_missing and to_missing allow us to capture certain types of "unknown but certain" data.

Dates have type-of information: from_type and to_type. These are primarily: birth, death, active. We
anticipate additional type-of dates added as the system matures.

We allow date to be "from YYYY to present" via the to_present flag. We do this knowing that "to present" may
only have been true when the record was modified, but record modification date is always avaiable. Policy will
clearly be necessary to guide use of this field.


### How versioning works

```

create table version_history (
        id int default nextval('version_history_id_seq'),
        main_id int default nextval('id_seq'), -- main constellation id, when inserting a new identity, allow this to default
        is_locked boolean default false,       -- (not used, see status) boolean, true is locked by version_history.user_id
        user_id int,                           -- fk to appuser.id
        role_id int,                           -- fk to role.id, defaults to users primary role, but can be any role the user has
        timestamp timestamp default now(),     -- now()
        status icstatus,                       -- enum icstatus note: an enum is a data type like int or text
        is_current boolean,                    -- (not used) most current published, optional field to enhance performance
        note text,                             -- checkin message
        primary key (id, main_id)
        );
```

The version_history table is the central table to a CPF constellation (aka record). The
version_history.main_id is the constellation id. By convention, all SNAC tables have a field 'id', which is
the record id. (Except nrd which use nrd.main_id as record id, and may not have field nrd.id.) Field
version_history.id is known by the alias "version" in all locations outside table version_history. All
first-order data tables have fields id, version, and main_id. (Except nrd as noted above.)

No data is ever updated or deleted. All operations are insert, although some inserts are "update" and some
inserts are "delete". Every insert is associated with a record in table version_history describing the
intellectual nature: insert, update, or delete. Version number always increases. There is a single sequence
supplying version numbers, so all version numbers are always unique to a particular version. A constellation
is always composed of data from the several tables, and each of those tables may have a different current
version number. The "current" version is the version <= max(version_history.id) for a given main_id and record
id in a table.

If we imagine a simplified view of version_history and name, data would look as follows after insert, update,
and delete:


| version | comment               | name               | is_deleted |   |
|---------+-----------------------+--------------------+------------+---|
|       1 | insert initial import | George Warshington | false      |   |
|       2 | update fix spelling   | George Washington  | false      |   |
|       3 | update delete         | George Washington  | true       |   |


After insert, the database contains row 1, with the name misspelled.

After update, the database contains rows 1 and 2, where row 2 is the "current" record now corrected. There are
two record for name, but only the most current record is visible to users.

After delete, the database contains all 3 rows, and is_deleted is now true. This deleted record will not be
normally be displayed to users. An "undelete" feature would show this record, and a review of modification
history of the record would reveal this. Users authorizef for the role "data administrator" can see all data
including deleted records.

It may help to understand some of the following specification by knowing that the unique record key for nearly
all tables is (id,version). For all tables the constellation id is main_id. Table nrd is special, with has 1:1
data fields with the constellation and is (naturally) joined to the constellation (table version_history) via
nrd.main_id. Do not join constellation data to table nrd. The "root" of the constellation is
version_history. Table nrd is (essentially) a data table. Unlike the other tables which use table.id as their
foreign key, the "foreign key" of nrd is nrd.main_id.

Note that Constellation->getID() returns main_id. All other classes Object->getID() returns a record id.
(Actually table.id is a record-group id due to versioning, conceptually it is a "record id" as opposed to a
"constellation id".)

There are some basic traits of versioning:

1) No record is ever deleted. Old versions of every record are left in the database.

2) The current published version is noted in table version_history, so we can select the published version of
every record from every table.

3) An update to part of a constellation might update a single table. That update will have a new version
number. This saves data copying and redundancy. Every update includes inserting a new record into
version_history with the existing main_id, but a new version_history.id which is the version number.

4) Due to (3), actual SQL to select a given record is based on selecting that record's version <= the current
version in table version_history.

5) Table version_history is the core or root of the constellation. The constellation is related to
version_history.main_id. Related tables include table nrd, which is special, but is not the root table.

The following SQL illustrates the some relationships between version_history and a typical table such as
"name" for a simplified scenario of a single version for a given fictional constellation id 1234.

First we want the maximum which is most recent version number for constellation id 7. 

```
select max(id) as version, main_id
from
version_history
where
main_id=7
group by id,main_id;
```


That query returns a single record and tells us the "current version" for constellation 7. Imagine there have
been many changes to this constellation, each change only effecting some parts. Every time the database was
updated, the version number incremented. We can go back to any version, but when editing we always use the
most recent version of each record in each table. That is: <= max(version).

In the case of published, we use a similar query, but constrain by status='published'. This query illustrates
the concept for selecting the most recent version number of the most recently published constellation id 7:

```
select max(id) as version, main_id
from
version_history
where
main_id=7 and
status='published' and
group by id,main_id;
```

Note that max(id) is aliased as 'version'. To all the world outside the version_history.id is a version
number. In order to get the max() of one column relative to another, we must "group by", and since we need
both id and main_id, we group by both columns.

Remember that we're calling SQL from php, and php caches values from queries. This means that we can call
smaller SQL queries, capture returned values, then pass those values to other relatively small queries. The
non-php option would be larger stored procedures. It turns out that php+sql results in better performance, and
the more granular queries are individually easier to read. In some of the following queries you will see php
placeholders $1, $2, $3, etc. These are subsituted safely from php to avoid SQL injection issues.

Selecting records from data tables requires a subselect. It looks a bit daunting, but is very fast, and is
simple enough that we can treat it as a convention or idiom. 

```
select
aa.is_deleted,aa.id,aa.version, aa.main_id, aa.original, aa.preference_score
from name as aa,
(select id,max(version) as version from name where version<=$1 and main_id=$2 group by id) as bb
where
aa.id = bb.id and 
aa.version = bb.version and
not aa.is_deleted
order by preference_score,id
```

We join our table (name) to a subquery on the same table. Note that we alias the original table as aa, and the
subquery as bb. Conceptually we're doing this:

```
select aa.* from aa,bb where ...
```

This is a relational join that works just like any join between any two tables. It just so happens that table
bb is a subquery.

The subquery is aliased as bb:

```
(select id,max(version) as version from name where version<=$1 and main_id=$2 group by id) as bb
```

The sole purpose of the subquery is to get an record id and matching max() version. Joining these back to the
original table constrains returns records to only each most recent individual name, <= the supplied
version. For normal edits, we look up the current version via the first query above.

The where clause of the larger select should now start to look simpler, now that we understand the subquery as
simply another related table. With in-line comments the where clause is:

```
where
aa.id = bb.id and            -- join and constrain id
aa.version = bb.version and  -- join and constrain version
not aa.is_deleted            -- only select non-deleted records
order by preference_score,id -- order the results
```

While there's some subtle behavior here, the where clause is a typical two table join. 

### Foreign key related records

Several descriptive tables are related via foreign key back to the main table. These include: language,
date_range, place_link. All of these tables have a 1:many relation back to their "main" table, so this
relation is logical. (The list used to include source, and scm.)

Note fields fk_id and fk_table. We save fk_table as a backstop in case something goes wrong. More on that
later.

Also note that all the ids here come from a single sequence, and they must come from a single sequence. All
record id values are unique across all tables because they are all generated from sequence 'id_seq'. Sequences
are atomic, so it is impossible for two processes requesting and id to get identical values. For what it is
worth, this feature is impossible or difficult in non-Postgres databases. Other techniques must be used
(probably a more complex foreign key). 


```
create table language (
        id                int default nextval('id_seq'),
        version           int not null,
        main_id           int not null,
        is_deleted        boolean default false,
        language_id       int,  -- fk to vocabulary
        script_id         int,  -- fk to vocabulary
        vocabulary_source text,
        note              text,
        fk_table          text, -- table name of the related foreign table. Exists only as a backup
        fk_id             int,  -- table.id of the related record
        primary           key(id, version)
        );
```

This is a simplified example of saving the language of the Constellation, the 'constellation' being table
version_history. Assume the constellation ID aka main_id is 456, and the language_id is 123. Select is the mirror of insert.

```
insert into language (language_id, fk_table, fk_id) values (123, 'version_history', 456);
select * from  language where fk_id=456;
```

Conceptually, we're doing a join where language.fk_id=version_history.main_id. We use main_id when joining data to "the constellation". 

```
select language.* 
from language, version_history
where
language.fk_id=version_history.main_id
```

There are a number of second-order data tables which are related to first order data using table.id as the
foreign key. We use the single-sided foreign key join in the related table, and we can do that because all our
record id values come from a single sequence, id_seq.

A few things happen in the background that give us the SCM ID value. Code must have already determined the scm
record based on constellation id (main_id) version number which gives us the scm.id. In other words, you have
to read the scm.id out of a SNACControlMetadata object via getSNACControlMetadata() or you must have the id as
a return value from an SCM insertMeta() function call.

This is a simplified example of saving the language of the SNAC Control Meta or SCM.  Assume the scm.id is
789, and the language_id is 123. Select is the mirror of insert. (This simplified example ignores version
number and is_deleted. See below for real-world sql.)

```
insert into language (language_id, fk_table, fk_id) values (123, 'scm', 789);
select * from  language where fk_id=789;
```

Conceptually, we're doing a join where language.fk_id=scm.id.  The insert is essenially this simple,
conceptually, and in actual sql.

```
select language.* 
from language, scm
where
language.fk_id=scm.id
```


However, the real-world select needs to account for version and is_deleted, so the select is a bit more
complex than the insert. $1 is the record id of the related table, and $2 is the version.

Select a meta data record(s). The query relies on a parameter $1 for the foreign key id of the record to which
this applies. In typical SQL relational fashion this query only needs a foreign key and version number.
   
Constrain sub query where fk_id, but group by id and return max(version) by id. Remember, our unique key is
always id,version. Joining the fk_id constrained subquery with the table on id and version gives us all of the
relevant id,version records, and nothing else.


```
select aa.version, aa.main_id, aa.id, aa.language_id, aa.script_id, aa.vocabulary_source, aa.note
from language as aa,
(select id,max(version) as version from language where fk_id=$1 and version<=$2 group by id) as bb
where 
not is_deleted and aa.id=bb.id and aa.version=bb.version

```

This is fairly similar to a version-aware select from a normal table. We constrain on fk_id and group by id,
whereas the normal query constrains on main_id and groups by id.

Because we're using php+sql, we have the related table's id in a variable, so we don't do a two table join,
although we're effectively doing just that via 2 simple queries instead of one more complex query (or one
larger stored procedure).

We save the related table name in language.fk_table. This is redundant, but might be useful for reporting. The
language.fk_id will be related to many tables, and typically we first query the related table, and second
query language. However, there may be a circumstance (which I cannot imagine) where we only have access to the
language table, but we still want to know all the records relating to scm. We could do this:

```
select * from language where fk_table='scm';
```

That query presupposes that table scm is not avaiable for a join, which would be extremely odd.

The fk_table field might be useful if we exported records to a database which lacks sequences, for example
MySQL, SQLite, or Oracle. Although so many other problems would arise that having fk_table is hardly
useful. Besides, we can always populate fk_table with a trivial SQL query which we would run for each possible
related table:

```
update language set fk_table='scm' where fk_id in (select main_id from scm);

```

Still, inserting values into fk_table is a fine, if redundant idea.


### Vocabulary table history and rationale

All controlled vocabulary terms are in a single table. A vocabularies share a common set of fields:
value(term), uri, description. Storing all vocabulary in a single tables makes it easy to add a new
vocabulary. At this time, vocabulary is mono-lingual, that is, term and description exist in a single
language, and might be duplicated in another language.

```
create table if not exists vocabulary (
        id          int primary key default nextval('vocabulary_id_seq'),
        type        text,        -- Type of the vocab
        value       text,        -- Value of the controlled vocab term
        uri         text,        -- URI for this controlled vocab term, if it exists
        description text         -- Textual description of this vocab term
        );
```

A proposed alternative was to store each vocabulary type in a separate table, but that has down side. Adding a
new type would require creating a new table (with identical field names to existing vocabulary tables) as well
a new insert and select functions, and this is more work than simply inserting new vocabulary data with a new
type. When tables are identical except for table name, table name is data. In this case table name would be
exactly equivalent to vocabulary type.

SQL databases give us an additional hint that vocabulary should be in a single table. We can prepare SQL
queries, and use placeholders. However, table name is not avaiable as a placeholder. The twenty vocabulary
types differ only by "type", and "type" is data best stored as a field, and not as name of twenty tables.

Each "vocabulary" is distinguished by vocaulary.type:

```
        type        
--------------------
 record_type
 script_code
 entity_type
 event_type
 name_type
 occupation
 language_code
 gender
 nationality
 maintenance_status
 agent_type
 document_role
 document_type
 function_type
 function
 subject
 date_type
 relation_type
 place_match
 source_type
```

### Vocabulary separate SQL file

The vocabulary related table and sequence SQL schema is in separate file, install/sql_files/vocabulary_init.sql.

This was done in order to facilitate rebuilding the development copies of the database. Vocabulary is fairly
large, and takes between 20 seconds and a minute to load. Developers may wipe their copy of the database
several times per day, but if they aren't working on any vocabulary code, it saves time to leave the vocabulary
tables intact.

See the install script: install/install.php



### Common fields and their meaning

Fields id, main_id, version, and is_deleted are common to all first order data tables.

table.id is the record id, and (table.id,table.version) forms a unique key. Field nrd.id is not used and may
not exist, but if it exists it will have the same value as nrd.main_id. If you need to join to "the
constellation" join on version_history.main_id.


table.main_id=version_history.main_id, and table.main_id is the constellation id for all tables. 


table.main_id is a constellation id, that is a grouping column. Across all tables, a single main_id value
identifies all the records belonging to a single constellation. As a constellation is edited, there will be
multiple version numbers, but main_id must remain constant.

table.version=version_history.id and you will notice that in all cases where we select version_history.id we
alias it as version. In SQL.php see: sqlCurrentVersion(), sqlMultiNameConstellationID(), selectDemoRecs().

table.is_deleted is true for deleted records

Remember that with our versioning system this database is insert-only. There are no updates. There will
eventually be a case where some non-constellation data table has an update, but as of Feb 12 2016, SQL.php
does not have a single update.

Delete is accomplished by getting a new version number, and using it along with setting is_deleted to true to
insert the "deleted" record. All the normal queries ignore is_deleted records, and only special admin queries
will ever be able to see deleted records.

### Identical text tables

We have four tables (and their related classes) that contain identically structured
textual data:

```
convention_declaration
structure_genealogy
general_context
mandate
```


As noted above, when the only difference between tables is table name, those tables should be combined. We may
eventually combine all the tables holding data from AbstractTextData class into a single table, but that has
not been done yet.

Each table looks like mandate, differing only by table name:

```
create table mandate (
    id            int default nextval('id_seq'),
    version       int not null,
    main_id       int not null, -- fk to version_history.main_id
    is_deleted    boolean default false,
    text text                   -- the text term
);
```


Insert to these is handled by insertTextCore(), and selecte via selectTextCore(). The only difference in the
arguments is the table name, that is the "term type". 

When the time comes to add a new text item, it will be necessary to add new wrapper insert and select
functions, as well as adding a new table. It is probably better at that time to join the tables together, and
refactor the code to support the single table paradigm as with vocabulary.

### Geographic authority

We are currently working on geographic a authority, based primarily on geonames. The authority table is
geo_place. Table place_link hold relations between original data tables and geo_place.

Currently only table version_history (the Constellation) has place links. 

In theory, place via <place>, <places>, or <placeEntry> can occur in elements: description, chronItem,
cpfRelation, function, functionRelation, legalStatus, localDescription, mandate, occupation, place,
resourceRelation.



```
create table geo_place (
    id                  int default nextval('id_seq'),
    version             int not null,  -- not an fk to version_history.id. 
    uri                 text,          -- URI/URL, geoname_id, or vocabularySource attribute
    name                text,          -- The geonames name; we do not have alt names yet
    latitude            numeric(10,7), -- Fixed precision
    longitude           numeric(10,7), -- Fixed precision
    admin_code          text,          -- later change to an fk to geo_place.id for the encompassing admin_code?
    country_code        text,          -- later change to an geo_place.id for the encompassing country_code?
    primary key(id, version)
    );
```

