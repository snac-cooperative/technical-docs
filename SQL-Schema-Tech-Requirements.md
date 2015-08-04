# CPF SQL schema

## Version implementation

1. each table contains version info.
    1. never update
    2. always insert and insert into version_history
    3. ideally, we don't need to remember to update existing "current" record (trigger?)
2. each table contains `cpf_id` foreign key
3. Current record is where `version=max(version)` or `current<>0`
    1. use trigger as necessary to avoid lots of update on insert
    2. use a view as necessary
    3. maybe simply use where clause and brute force the first implementation
4. table version_history has all version info for all users for all tables
   1. version_history has its own sequence because it is a special table

```PLpgSQL
create table version_history (
    id int primary key default nextval('version_history_seq'),
    user int, -- fk to user.id
    date timestamp, -- now()
)

create table foo_internal (
       id int primary key default nextval('unique_id_seq'),,
       cpf_id int, -- fk to cpf.id
       data text,
       version int -- fk to version_history.id, sequence is unique foreign key 
       current int -- or bool, depending on implementation this field is optional
       );

-- Optional view to simplify queries by not requiring every where clause to use version=max(version)
create view foo as select * from foo_internal where current;

create view foo as select * from foo_internal where valid = true;

-- a view foo_current that is the most current record
create view foo_current as select * from foo_internal where version=max(version);

select * from foo_current where id=1234;

```

Having renamed all the tables to table_internal and created one or more views, any use of the table_internal name tends to make the code un-portable. We need to make sure the views perform as well as the longer, explicit queries.

```PLpgSQL
-- Yikes, using foo_internal means this query breaks if the internal tables have a schema or name
-- change.
select * from foo_internal where id=1234 and version=max(version);

-- Normally, we would use the foo view, not table foo_internal
select * cpf_current as cpf,foo_current as foo where cpf.id=foo.cpf_id;

```

*Caveat* The actual implementation needed to change the setup of `foo_internal`, since in some cases multiple entries could be for the same `cpf_id` (ie name entries, date entries, sources, documents, etc).  So, there are two options: 
    * `(id, version)` primary key in each table, where we get the latest version for each id and join on `cpf_id` for the latest entries for that cpf, or
    * `(cpf_id, version)` foreign key, where a join must also match the current version of the cpf record to get the latest entries for that cpf (note independence on this table's `id` field.
In commit 533fc082fb3c68f7c2bf8edbbace2571c8f963bc, I've chosen the first version of this scheme.

## Splitting of merged records

1. split may result in N new records
2. each field (especially biogHist) might be split, but only via select/cut/copy/paste. Editing of content is not supported.
   1. Edit during split is a later (if ever) feature.
3. In a 2 way split, where originals are A and B, 
   1. we need to show the user the original record A (original fields) and the most current version of each field
   2. ditto original B and current version of each field
   3. each field one checkbox: keep
4. Some web UI is created for splitting, including field subsplit. The UI details by prototyping.
   1. splitting a single field involves select, choose, and a resulting highlight. 
   2. We allow multi-select, multi-choose, and accumulate select/choose sections.
5. Nth phase shows original N plus the current, probably with gray-highlight for previous records. All fields/text are selectable since content is often in several records. 
6. In the database, we create a new cpf record for each split record, and invalidate the merged record.
7. what happens in table merge_history when a merge is split?


## Schema changes to support merge and split

1. add `valid` field to table cpf
   1. change valid to 0/false when the cpf record is merged
2. add table merge_history, all result in multiple records being created
   1. Merge: from=old (singleton) and to=new (merged)
   2. Split from merge: from=old (merged) and to=new (singleton)
   3. Split (original singleton): from=old and to=new (multiple singletons)
3. When splitting merge, need an additional record to link new split with its parent (pre-merge) record. Only 1 generation back.
   1. Does this lineage record need a special field in table split_merge_history? (No.)

```PLpgSQL
create table split_merge_history (
    from_id int, -- fk cpf.id
    to_id int,  -- fk cpf.id
    date timestamp,
);
```
