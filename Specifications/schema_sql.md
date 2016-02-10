### Database schema and SQL queries


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
record is. Field version_history.id is known by the alias "version" in all locations outside table
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

