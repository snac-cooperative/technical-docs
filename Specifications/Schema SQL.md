### Database schema and SQL queries

This file gives detail of schema decisions, and function of the major SQL queries. 


### How versioning works.

```
create type icstatus as enum ('published', 'needs review', 'rejected', 'being edited', 'bulk ingest');

create table version_history (
        id int default nextval('version_history_id_seq'),
        main_id int default nextval('id_seq'), -- main constellation id, when inserting a new identity, allow this to default
        is_locked boolean default false,       -- boolean, true is locked by version_history.user_id
        user_id int,                           -- fk to appuser.id
        role_id int,                           -- fk to role.id, defaults to users primary role, but can be any role the user has
        timestamp timestamp default now(),     -- now()
        status icstatus,                       -- enum icstatus note: an enum is a data type like int or text
        is_current boolean,                    -- most current published, optional field to enhance performance
        note text,                             -- checkin message
        primary key (id, main_id)
        );
```

The version_history table is the central table to a CPF constellation (aka record). The
version_history.main_id is the constellation id. By convention, all SNAC tables have a field id, which is the
record id. Field version_history.id is known by the alias "version" in all locations outside table
version_history. All first-order data tables have fields id,version, and main_id. It may help to understand
some of the following specification by knowing that the unique record key for nearly all tables is
(id,version). For all tables except nrd, the constellation id is main_id. Table nrd is 1:1 data fields, and is
special, therefore nrd.id is both record id and constellation id.



There are some basic traits of versioning

1) No record is ever deleted. Old versions of every record are left in the database.

2) The current published version is noted in table version_history, so we can select the published version of
every record from every table.

3) An update to part of a constellation might update a single table. That update will have a new version
number. This saves data copying and redundancy.

4) Due to (3), actual SQL to select a given record is based on selecting that record's version <= the current
version in table version_history.

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

In the case of published, we use a similar query, but constrain by is_current or by status='published'. The
exact implementation has not been settled, and there are plusses and minuses. Nonetheless, this query
illustrates the concept for selecting the most recent version number of the most recently published
constellation id 7:

```
select max(id) as version, main_id
from
version_history
where
main_id=7 and
is_current and
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
date_range, place_link. All of these tables have some 1:many relation back to their "main" table, so this
relation is logical. (The list ssed to include source, and scm.)

Note fields fk_id and fk_table. We save fk_table as a backstop in case something goes wrong. More on that
later.

Also note that all the ids here come from a single sequence, and they must come from a single sequence. All
record id values are unique across all tables because they are all generated from sequence 'id_seq'. Sequences
are atomic, so it is impossible for two processes requesting and id to get identical values. For what it is
worth, this feature is impossible or difficult in non-Postgres databases. Other techniques must be used
(probably a more complex foreign key). 


```
create table language (
        id              int default nextval('id_seq'),
        version           int not null,
        main_id           int not null,
        is_deleted        boolean default false,
        language_id       int,  -- fk to vocabulary
        script_id         int,  -- fk to vocabulary
        vocabulary_source text,
        note              text,
        fk_table          text, -- table name of the related foreign table. Exists only as a backup
        fk_id             int,  -- table.id of the related record
        primary         key(id, version)
        );
```

This is a simplified example of saving the language of the Constellation, the 'constellation' being table
nrd. Assume the constellation id is 456, and the language_id is 123. Select is the mirror query.

```
insert into language (language_id, fk_table, fk_id) values (123, 'nrd', 456);
select * from  language where fk_id=456;
```

Conceptually, we're doing a join where language.fk_id=nrd.id

```
select language.* 
from language, nrd
where
language.fk_id=nrd.id
```

The insert is essenially as simple as above. However, the select needs to account for version and is_deleted,
so the select is a bit more complex than the insert. $1 is the record id of the related table, and $2 is the version:

```
select aa.version, aa.main_id, aa.id, aa.language_id, aa.script_id, aa.vocabulary_source, aa.note
from language as aa,
(select fk_id,max(version) as version from language where fk_id=$1 and version<=$2 group by fk_id) as bb
where 
not is_deleted and aa.fk_id=bb.fk_id and aa.version=bb.version

```

This is fairly similar to a version-aware select from a normal table. We constrain on fk_id and group by fk_id,
whereas the normal query constrains on main_id and groups by id.

Because we're using php+sql, we have the related table's id in a variable, so we don't do a two table join,
although we're effectively doing just that via 2 simple queries instead of one more complex query (or one
larger stored procedure).

We save the related table name in language.fk_table. This is redundant, but might be useful for reporting. The
language.fk_id will be related to many tables, and typically we first query the related table, and second
query language. However, there may be a circumstance (which I can hardly imagine) where we only have access to
the language table, but we still want to know all the records relating to nrd. We could do this:

```
select * from language where fk_table='nrd';
```

That query presupposes that table nrd is not avaiable for a join, which would be extremely odd.

The fk_table field might be useful if we exported records to a database which lacks sequences, for example
MySQL, SQLite, or Oracle. Although so many other problems would arise that having fk_table is hardly useful. Besides, we can always populate fk_table with a trivial SQL query:

```
update language set fk_table='nrd' where fk_id in (select id from nrd);

```

Still, inserting values into fk_table seems like a fine, if redundant idea.


### Why vocabulary is in a separate schema file

We have moved all vocabulary related table and sequence related SQL into a separate file, install/sql_files/vocabulary_init.sql.

This was done in order to facilitate rebuilding the development copies of the database. Vocabulary is fairly large, and takes between 20 seconds and a minute to load. Developers may wipe their copy of the database several times per day, but if the aren't working on any vocabulary code, it saves time to leave the vocabulary tables intact.

See the install script: install/install.php


### Common fields and their meaning


Fields id, main_id, version, and is_deleted are common to all first order data tables.

table.id is the record id, and (table.id,table.version) forms a unique key.

table.main_id=version_history.main_id, and table.main_id is the constellation id for all tables except
nrd. The constellation id of nrd is nrd.id and nrd.main_id, although it is best to go with nrd.id.

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
