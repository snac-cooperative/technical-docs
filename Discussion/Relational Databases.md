# Discussion on Relational Databases

#### What is "normal form" and what informs the database schema design?

Edgar F. "Ted" Codd created 12 rules (revised with a 13th rule) to clarify the Relational Database Management
System (RDBMS).

https://en.wikipedia.org/wiki/Edgar_F._Codd

Breaking any of these rules weakens data integrity and the ability of the system to manage the data. An RDBMS
is not merely a bucket of data, but an entire eco-system for the management of data and data related
activities. Before Codd's work, databases were managed on an ad-hoc basis as collections of files with
links. It was a mess. Data was lost. Only the DBA knew how to find the data, and access methods could be very
different for data in different locations. Accessing data could also be extremely slow. In addition to
assuring the integrity of data, as well as managing it, relational database systems are very fast.

https://en.wikipedia.org/wiki/Codd%27s_12_rules

The "R" in RDBMS is "relational" and Codd invented the relational model of data. Key to relational data
modeling is "normal form".

https://en.wikipedia.org/wiki/Database_normalization

The RDBMS world generally uses third normal form. Lower levels of normalization create additional work for
data operations. Higher forms rarely show any improvements. The key concept of normalization is that a datum
only exists in one place. In the RDBMS world where SQL implements relational algebra, normal form is both
convenient and natural. In other venues such as paper ledgers, data stored in flat files, or in spreadsheets,
normal form can seem awkward.
