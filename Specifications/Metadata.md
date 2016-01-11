# Metadata

Each piece of first-order data in the database may have metadata associated with it.  The information the database will keep track of is:

* _source citation_ : The citation associated with this particular first-order data.
* _sub citation_ : The part of the citation associated with this first-order data (i.e. "page 5").
* _source data_ : The text found in the citation related to this first-order data.  This is an "as seen" field. 
* _descriptive rules_ : The rules associated with creating this first-order data from the source citation.
* _note_ : The note from the user associated with this citation and first-order data.

This specification leads to a SQL database with the following structure:

```
id              int primary key default nextval('id_seq'), -- id for Metadata
fk_id           int                                      , -- FK to data described
fk_table        text                                     , -- table name for FK
version         int                                      , -- version from version_history
citation        int                                      , -- FK to source table
sub_citation    text                                     , -- subcitation information
source_data     text                                     , -- source data
descriptive_rules text                                   , -- FK to vocabulary
note            text                                       -- human-readable note
```


