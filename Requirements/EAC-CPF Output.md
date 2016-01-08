# EAC-CPF XML Output Requirements

When generating EAC-CPF out of the database, the system should be able to provide the following pieces of information and follow these guidelines.

## Control/Source Data

Daniel has given the following requirement for sources: "What scholars and archivists want is to be able to say who provieded the decriptive data, when, and based on what sources. A description is a constellation of assertions amed by one or more agents/editors based on one or more sources. Thus Source Statement should be repeatable, as any given assertion can be revised over time, or a new editor comes along and simply provides corroborating data from anew source without revising the description otherwise."

A source block should be creatable for the tags for descriptive data (first-order data, FOD) in a constellation. There may be multiple blocks for each piece of data, thus they are repeatable blocks.  Each block must contain:

* Agent responsible for the descriptive assertion (FOD)
* Latest transaction date for the source block (the date the assertion was made or updated)
* Transaction type (was this a new descriptive assertion (FOD) or a revision)
* Source citation (the URI, citation, or source for this assertion (FOD))
* Source data (the text of what the scholar or archivist got out of the source)
* Descriptive rules (any rules that the scholar or activist used to formulate the assertion (FOD) from the source)
* Note (a human-readable note from the scholar or activist about the assertion (FOD))
