# System-Generated Documents

The following documents and data should be generated from the completed system.

## Data Interoperability

Data should be available to be downloaded in the following formats:

* EAC-CPF XML
    * Individual identity constellations should be download-able as fully-formed EAC-CPF XML documents
* Turtle Triples
    * Subsets of the data, including the entire database, should be exportable as well-formed Turtle triples
* RDF Triples
    * Subsets of the data, including the entire database, should be exportable as well-formed RDF triples
* JSON-LD
    * Subsets of the data, not including the entire database, should be exportable as well-formed JSON-LD


## System Reports

While the web interface is the primary public face of SNAC, many other views of the data and meta data are
necessary, especially for admins and governance. Those "views" are reports and will primary be generated via
integration of a third-party reporting package such as Jaspersoft Business Intelligence Suite, which is free,
open source, and includes a full range of tools.

For each user of the system, the following reports should be available for download:

* List of records the user has edited
* Number of records the user has edited

For each holding institution, the following reports should be available for download:

* Number of records the institution has edited
* Number of records the institution has contributed
* List of records the institution has contributed
* List of records the institution has edited
* List of individuals within the institution and the records edited by each person
* List of records the institution has contributed with individuals who contributed to each record

General reporting:

* Number of participating holding institutions
* Number of records edited per hour, day, month, year
* Number of identity constellations available in the database
