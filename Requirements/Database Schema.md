
### Introduction

SNAC data is saved and managed in an SQL Relational Database Management System called PostgreSQL or simply
Postgres.

This document gives background and some commentary about the schema which in the RDBMS sense are the SQL
commands to create the tables of the database. The details of schema are best seen by reading the schema SQL
files which contain extensive comments.

There is a brief discussion of RDBMS technology: [Relational Databases](../Discussion/Relational Databases.md)

A related document is the Data Overview: [Data Overview](Data Overview.md)

The specifications have details about implementation: [Schema SQL](../Specifications/Schema SQL.md)

The schema consists of several SQL text files which can be found in the SNAC source tree in
install/sql_files/. There are two types of SQL files: tables, and data. The table files create tables and
indexes. The data files are for static data such as controlled vocabulary and place data.


| File                                         | Type   | Description                                      |
|----------------------------------------------|--------|--------------------------------------------------|
| install/sql_files/initialize_users.sql       | data   | init default system users and roles (privileges) |
| install/sql_files/languages.sql              | data   | language codes and descriptive labels            |
| install/sql_files/places_vocabulary.sql      | data   | place entries, gatherd from the CPF files        |
| install/sql_files/places_vocabulary_init.sql | tables | place schema                                     |
| install/sql_files/schema.sql                 | tables | the main schema, especially constellation        |
| install/sql_files/schema_meta.xml            | xml    | table constraints in XML for use by SchemaSpy    |
| install/sql_files/scripts.sql                | data   | written script codes and descriptive labels      |
| install/sql_files/vocabulary.sql             | data   | 400,000 terms for all controlled vocab           |
| install/sql_files/vocabulary_init.sql        | table  | vocabulary table schema                          |


### Background

It is important to realize that SNAC is not a document-oriented system. We do not save or edit XML files. The
database stores all the supported EAC-CPF elements and attributes in a relational database. This vastly
improves every aspect of performance, and guarantees data integrity as well as handling critical features such
as database management.

The schema itself is fairly simple. Using Postgres we make extensive use of "sequences". A sequence is a
special table that exists to generate unique, sequential numbers. We use a single sequence across all data
tables. This powerful and elegant usage of sequence simplifies our foreign keys. Every row id of every data
table has a unique sequence number identifier, thus foreign keys need only consist of the row id (and version
number, as it turns out).

Incidentally, sequences use bigint so the max value of a record id is 9223372036854775807. To put that in
perspective, 1000 editors making 1000 changes per second would need 294,471 years to cause the row id values
to roll over. The record id values are never displayed to users, and are only used internally. (A SQL database
row is a "record" in the database world, so we will use database terminology in this document.)

We use the data type 'text' for all text fields. Postgres 'text' performs as well as
varchar, but will accept very large strings, thus freeing us from varchar size constraints. We do not use
foreign key constraints. They carry a large performance penalty, and our constraints are handled in the PHP
code. We also avoid an entire category of constraint-related bugs by using id_seq for all tables.

We are currently using very simple SQL. There are no views, triggers, or special data types. We don't use
stored procedures, and instead have a single PHP function per query which obviates the needs for stored
procedures, and may actually be faster.

### About version

Additional detail about the version system can be found in the Specification on Schema SQL:
[How versioning works] (../Specifications/Schema%20SQL.md#how-versioning-works)

An basic requirement is to save every edit to a constellation. This is fairly unusual. Most systems have
nightly backups, and even those are rotated so that there are only certain fixed, historical checkpoints. The
SNAC SQL schema has a row id, constellation ID, and version number for every row of every constellation data
table. A version history table tracks all constellations, versions, and per-version notes. (These notes are
akin to git or svn checkin note, not to be confused with CPF source, citation, or control data.) Update
operations insert a new record, but the old record remains. Delete operations insert a new record which is
marked as deleted, but the old records remain. Deleting an entire constellation simply marks the constellation
as deleted, making no change and deleting nothing.

Every table contains every version of every edit to any data ever made on that table, no matter how
trivial. Happily, Postgres and most other SQL databases can easily handle this situation. Individual record
retrieval is generally in the sub-millisecond range. However, the complexity of the versioning makes the SNAC
SQL queries somewhat more complicated, although nothing that would be considered complex compared to many
truly complex databases. Nearly every query has at least one sub-query. All the queries can be found in
SQL.php in the source code tree.

The tricky part about version is that the most current version number is the max(version) for the current row
id. Even more tricky is that the version that matches some constellation version is for the current row,
max(version) less than or equal to the constellation version. We simplify this by always providing the desired
constellation version. Top level code knows about "current". Low level code is always asked for data less than
or equal to a specific version number. In other words, if we want current, we look up the current for the
constellation which is simply max(version), and then call a low level data retrieval functions that return
row(s) <= that version number.

In SQL the subquery looks like this where data_table is the table name, $1 is the id, $2 is the version:

```
(select id,max(version) as version from data_table where id=$1 and version<=$2 group by id) as bb
```

We simply join that subquery with the data_table (a type of self-join) and then the where clause constrains on
not is_deleted.

The situation is slightly more interesting for related tables:

```
(select id,max(version) as version from related_table where fk_id=$1 and fk_table=$3 and version<=$2 group by id) as bb
```

Note that fk_id=$1 but we still group by id. The fk_table is a superfluous belt-and-suspenders thing.


### Example data table

See the name data table below. Columns are also known as fields: id, version, ic_id, is_deleted,
preference_score, original.

All data tables have fields: id, version, ic_id, and is_deleted. Fields id and version constitute a unique
primary key. Version is the version number of this row. The constellation ID is ic_id. We never delete
anything, so the field is_delete is set to true when this row has been logically deleted.

The actual data in this table is preference_score and original. 


```
create table name (
    id               int default nextval('id_seq'),
    version          int not null,
    ic_id            int not null,
    is_deleted       boolean default false,
    preference_score float, -- Preference to use this name
    original         text,  -- actual name (in <part>)
    primary key(id, version)
    );
```


### Second order related tables are more interesting

All the first order data of EAC-CPF such as name, occupation, subject, biog-hist, cpf-relation, and
resource-relation (and so on) are related to "the constellation" via id, version, and ic_id. Second order data
is more meta and includes: scm (SNAC Control Meta Data), date, language, place. Each record in these tables is
related to one specific record in a first order table. That relationship is managed via a reverse foreign key
fk_id, which is possible due to use of id_seq for row id values.

In table scm below we see the typical id, version, ic_id, and is_deleted. Actual data fields are citation_id,
sub_citation, source_data, rule_id, and note. Two data fields are actually foreign keys to the vocabulary. See
the section below that describes use of controlled vocabularies.

The foreign key between scm and the related table is fk_id. We never need to use the fk_table. When joining a
first order data table to the related second order data, we know the id and simply join on
first_order.id=second_order.fk_id where ic_id is the same and version=max(version) for the given record id.

We never start with a second order table and an unknown related first order table, but we could. If we join a
row in a second order table to all first order tables using fk_id and version there will only be one row of
one first order table that matches, even without using fk_table. Of course, with fk_table we can simply pull
the related row from the first order data table. We are unable to imagine a situation where we need to do
that, but if it comes up, we can do it.


```
create table scm (
    id           int default nextval('id_seq'),
    version      int not null,
    ic_id        int not null,
    is_deleted   boolean default false,
    citation_id  int,  -- fk to source.id
    sub_citation text, -- human readable location within the source
    source_data  text, -- original data "as entered" from CPF
    rule_id      int,  -- fk to some vocabulary of descriptive rules
    note         text,
    fk_id        int,  -- fk to related table.id
    fk_table     text  -- table name of the related foreign table. This field exists as backup
);
```


