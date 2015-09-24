
# Internal Data Storage

The data should be stored in a SQL database. Every piece of data is in a separate field to the extent that is practical.
Data is organized into fields (columns) records (rows) and tables. Fields related to each other are in the
same table. Every record has a unique, permanent, numerical id often called a "key" or "primary key". For
the SNAC Co-op we have decided that records are never overwritten during update.  An update operation creates a new record identical to the old record except for updated
fields. All old records are available for viewing via special interface. The old records are invisible to
operations that are intellectually acting on "current" data.

Version history, including past versions of a field and record, users that made changes to that data, institution history, and timestamps must be kept in the internal data storage.

Provenance of each element must be captured as well, including across merges and splits of identity constellations.


The application must avoid storing mixed markup as much as possible.  (Brad Westbrook sugests we avoid mixed markup).


## Captured actions on data

Prior to human edits, merged records can be algorithmically split by the computer, assuming we write code to
perform such a split. After human edit, a split must be performed by a human. It is a requirement that all
previous versions can be viewed (read-only) during the human-mediated split operation so the human can refer
back to previous information.

After human edits, rollback only applies to human edited versions. There is a fire-break where rollback cannot
cross from human edits back to machine-merged descriptions. The policy group needs to supply policy
requirements for the tech folks to implement.

The broad requirements for the application are: edit data, split records, merge records. Secondary features to
make the system useful include: work flow enforcement, search, reporting (including "watch" features),
administration, authorization (data privileges).
