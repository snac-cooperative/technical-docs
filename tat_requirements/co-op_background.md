
#### Authors


Tom Laudeman, Technical lead, University of Virginia, Institute for
Advanced Technology in the Humanities
[twl8n@virginia.edu](mailto:twl8n@virginia.edu)

Brian Tingle, Technical Lead for Digital Special Collections, California
Digital Library

Rachael Hu, User Experience Design Manager, California Digital Library

Ray R. Larson, U.C. Berkeley - School of Information

Robbie Hott

#### Organization of documenatation

[Plan](plan.md) (external, broad view roadmap)

[Introduction (this document) ](introduction.md)

[Requirements](requirements.md)

co-op background

#### Introduction to SNAC


Social Networks and Archival Context (SNAC) is a Mellon-funded project
to aid end-user researchers in discovering, locating, and using
distributed historical record descriptions, especially as relates to
corporate bodies, persons, and families (CPF). These descriptions are
often in finding aids, and they often exist in electronic format. They
are distributed across many geographical locations and many networks.
SNAC brings all this data together in a central system, while retaining
links to the original descriptions. Critically, SNAC attempts to merge
descriptions for the same [matching?] CPF identities, linking those
descriptions to a single authoritative name.
^[[a]](#cmnt1)^^[[b]](#cmnt2)^

We have an existing system (SNAC one?) and need additional work to get
to a new system (SNAC 3?), so part of this document is gap analysis. The
scope of this document is to outline technical specifications and
requirements for a production system for the Cooperative
phase^[[c]](#cmnt3)^ of SNAC. This production system will handle
ingestion, processing, matching/merging, discovery, and dissemination of
archival descriptions that are submitted and added to the Cooperative.  

#### Evaluation of Existing Technical Architecture


##### Overview


This section describes the existing technical architecture, and later
moving on to describe the required functionality for the production
system for the Cooperative.

Many of the archival records that are ingested in SNAC are Encoded
Archival Context - Corporate bodies, Persons and Families (EAC-CPF,
hereafter CPF) records. EAC-CPF is an XML schema endorsed as a standard
by the Society of American Archivists. We speak of CPF descriptions in
the sense of a “computer record”: often a single text file and not a
“record” in the archival sense.

“Linked data” technology related to the Resource Description Framework
is also employed to manage some controlled vocabularies in the project.

The current system consists of three main components: extraction,
match/merge, discovery. Extraction consists of extracting data from
incoming archival description records (primarily EAD, MARC21 and some
other unique formats), to create CPF descriptions. Match/merge is to
process the CPF descriptions in search of name matches and to merge
well-matched descriptions. The resulting data set includes merged
descriptions and descriptions with no matches (called singletons), all
in a single database. Discovery is discovery and dissemination of the
data via a web application.

The production system will have two additional components: maintenance
and administration. Maintenance includes manual corrections, such as
correcting data within a description, splitting incorrect merges,
merging descriptions for the same CPF identity, and description embargo
(embargo hides descriptions from public view for either technical or
administrative reasons). Administration is the typical management of
users, accounts, and reporting on the state of the system.

The first two phases of data processing are extraction, and match/merge.
A database of descriptions, both merged and unmerged is the end
result^[[d]](#cmnt4)^. The process of ingesting extracted data and
merging will continue for the life of the project. An extensive
web-based search engine lets users discover descriptions.

We use the term “merged” loosely when applied to the automated system
since the final database may contain descriptions which should be
merged, but which a computer is unable to reliably determine.  We take a
conservative approach, preferring to only merge descriptions that a
computer program can accurately distinguish.^[[e]](#cmnt5)^ Even so,
some descriptions will have been incorrectly merged, and thus the need
for a (future) maintenance system that allows manually splitting of
descriptions, among other things.

Both Extraction and Match/merge are script based, batch processing,
semi-automatic processes managed entirely by software engineers.
Discovery and Maintenance are both web applications with extensive
public user interfaces intended for researchers. Administration is done
mostly via a non-public web application.

Extraction and match/merge are well developed, although we have some
planned improvements. Discovery is well developed, but existing features
are being refined, and adding new features is on-going. Maintenance and
administration have not yet been created and must be written from the
ground up.

#### Current State of the System


CPF description generation is done at the University of Virginia’s
Institute for Advanced Technology in the Humanities (IATH). IATH handles
the CPF data extraction and hosts servers for data processing and the
SNAC prototype web site. Data processing, XTF indexing (for the
discovery interface), and web hosting take place on a Linux server with
24 CPUs and 94 GB of RAM connected to a 1Gbit network switch. This
server is administered by the IATH sysadmin team. \

Collections of archival description computer descriptions in a variety
of formats are extracted into CPF format XML. This process involves
writing XSLT scripts that extract and transform input descriptions, and
create CPF files as output. The current state of the extraction is a
collection of XSLT scripts supplemented by Perl scripts. The input files
are XML with large numbers of files in EAD, MARC XML, and British
Library XML, as well as several smaller data sets.  A large XSLT code
library is shared among most of the extractions. Each type of extraction
builds a generic internal data structure, which is serialized as EAC-CPF
XML output. The XSLT takes into account various descriptive practices in
the input data, and reformats as necessary to create a single type of
normative CPF output. The complexity of this task centers around the
large number of small differences in descriptive practice. Currently
more than 3 million CPF computer descriptions have been created. The
XSLT processor is Saxon 9 HE, which is the free “home edition” of Saxon.
Saxon implements XSLT 2.0. There are a small number of Perl scripts that
integrate the XSLT into a pipeline, automating tasks such as chunking
data sets into sizes that won’t exceed computer memory.

The current state of the match/merge is (filled in by Yiming/Ray/Sara,
initially a one or two paragraph overview with more detail added later
as necessary).

Overview of Brian’s UI and programming for the SNAC2 XTF discovery tool
(add this to another item if there is an umbrella section more
appropriate).

Is XTF the only discovery tool we will offer? Will SNAC be fully indexed
by Google and Bing?

TK The involvement of the UC Berkeley I School includes the development,
testing and modification of the matching and merging components of the
SNAC system. The current system, described in more detail below, takes
the EAC-CPF records derived from the various source institutions and
compares the names and associated information (especial dates) to
identify the records that likely describe the same person,

organization, or family. The process involves not only comparison across
input records, but also comparison with information from the Virtual
International Authority File, and approximate matching for these records
as well.

TK The involvement of CDL includes … (Brian)

TK We have several extant user studies UI/UX … (Rachael, on-going)

TK The results of these studies are … (Rachael, on-going)

TK The technical implications of these studies are … (Rachael, on-going)

The current system uses a fairly loose software development process.
Source code is primarily maintained on a Linux server which is managed
by standard practices as relate to hardware, software, network, user
accounts, back up, and so on. All the data resides on the server. Source
code is managed by version control systems. The amount of quality
assurance and testing has been increasing over time, as well as
documentation, and management aspects such as release process. All tools
currently used are open source, and the code written for SNAC is open
source. We have begun to formalize feature request and issue tracking.
The development process is agile in that there are frequent small
changes that are committed to the version control, and the code is
nearly always in a working state.

#### Processing Pipeline

TK Describe algorithmic portions, and add a section for new features.

#### Extraction


There are currently several CPF extraction software pipelines: MARC21,
British Library, Smithsonian Agency History, New York State Archives,
Smithsonian Joseph Henry, Smithsonian Field Books, and EAD from nearly
60 institutions.

The first step in adding new records to the SNAC database is to convert
incoming data into EAC-CPF XML.  One EAC-CPF record is created for each
successfully extracted reference to an identity from an archival source.
The processing also allows for some degree of remediation of data
quality issues and serves to normalize the data into a common format.
 Scripting data transformation processes is a significant task that
often requires close communications with data contributors and
customizations to accommodate local practices of the contributors.

Creating an extraction is a complex process since we must deal with
variances in local descriptive practice. The MARC21 tools have been made
available as a web interface and this demonstrates the feasibility of
moving more of the processing responsibility to data donors. If we are
optimistic, we hope that EAD-to-CPF extraction and all other types of
future extractions can be turned into donor-driven tools. Specifically,
we create the tools and then deploy them as web applications and/or
desktop applications. Web hosted extraction tools allow us to leverage
the power of our servers and programmers so that data donors do not need
a large computing infrastructure in order to participate. In any case,
data must be validated before ingest into the match/merge processing.

XSLT and perl are the predominate technologies used in the generation of
the XML documents created by this process.  The code architecture
focuses on reusability of modular routines to facilitate maintenance of
the customizations needed accommodate the diversity of data sources.

Code, sample data, and documentation are in Github. The pipeline is
being run on a server, but the hardware requirements are minimal enough
that most laptop computers could run the extraction. The system requires
unix-like features of Linux, MacOS, or cygwin (for MS Windows). The XSTL
engine is Saxon 9.x HE which is the free, public version of Saxon.


#### Gap analysis


This is gap analysis between the current and SNAC2-prototype. Perhaps
this should be in the Required and Planned Functionality below.

#### Data maintenance


A goal of the pilot phase it to demonstrate cooperative maintenance of
the data resource.  The prototype does not have robust support for
maintaining the corpus of EAC-CPF identity documents.

While the current architecture supports the incremental addition of new
records through input to the match/merge system there is no way to
directly create a record for a known new identity.  A workflow that
includes an archival description operator looking up identities in the
system; and creating a new record if the identity they are trying to
reference does not exist; must be supported.^[[g]](#cmnt7)^ The new
maintenance system will allow creation of a new identity record via the
web interface.

With the current architecture, textual information that comes from the
un-merged EAC-CPF documents - such as biographical history notes - could
be maintained by editing the un-merged EAC-CPF source and re-exporting
the merged EAC-CPF document.  New information could be added to the
merged documents by adding another un-merged document into the input
directory.  New un-merged EAC-CPF documents containing ARKs could be
matched directly, bypassing the match/merge searching.  It is less clear
how factual information that comes from VIAF could be modified in the
current prototype.

Early research indicates the most import section of a merged EAC-CPF
document is the section that contains links to primary and secondary
research materials about the named identity. The overarching goal of
archival description it to enable researchers to find materials relevant
to their interests. In the current prototype, the only mechanism to add
a new link into the “Archival Collections” (a more accurate label would
be “Archival Materials”) or “Related Resources” sections of links would
be to generate an un-merged EAC-CPF record with the links and add run
that through the match/merge system.

The current prototype maintains information about relationships between
the entities described by the merged EAC-CPF documents.  Currently,
“associated with” and “corresponded with” are the only two relationship
types supported.  Early research suggests that archival description
practitioners are interested in using a more extensive vocabulary of
relationship types.

[ problem of linking to EAD if the URLs are not known ]

    •    establish procedures to accepting more batch submissions

    •    XTF is not designed to handle so many documents; XTF will can
not be run in a “clustered” mode; must scale up, not scale out

    •    Cheshire II does not have a Open Source Initiative certified
license

#### Pilot phase architecture


#### Alternative 1^[[h]](#cmnt8)^

(Rewrite this for a web application with SQL database.)


The most expeditious way to launch a pilot phase would be to leave the
basic technical architecture of the prototype in place, and to focus
initial energies into establishing policies and procedures that work
within the constraints of this architecture.  Two key systems that would
need to be set up for this approach to work are a customer relationship
management (CRM) system and ticketed help desk.

Customer relationship management systems have historically be used as a
sales support tool.  Information on current and potential customers,
including contact information and institutional affiliation, are
maintained in a database.  All pilot members institutions and designated
contacts should be entered into a CRM system for the pilot phase.  All
correspondence, call, contracts and agreements with accepted and
potential pilot phase members should be logged or stored in the CRM
system.^[[i]](#cmnt9)^

The CRM system should support or integrate with a help desk that issues
work ticket numbers. ^[[j]](#cmnt10)^ Any addition or change in the
maintained corpus of merged EAC-CPF records will require a work ticket
number.  Expectations for response times for issued tickets should be
established, clearly communicated, and measured for compliance.  A
customer service manager^[[k]](#cmnt11)^ will actively monitor the queue
of work tickets pending.  An operations manual will be maintained so
that the customer service manager or any additional first tier support
staff will be able to handle a set of ticket types.  If a procedure for
the request is not yet documented in the operations manual - or if the
manual indicates this is a task for second tier - then the ticket will
be escalated to the second tier support programmer.  The second tier
support programmer will have the technical skills to manipulate the
technical infrastructure; such as through editing XML files or directly
altering the database.  The second tier support programmer would also be
responsible for performing data extraction and normalization of non
EAC-CPF data sources processed during the pilot phase.^[[l]](#cmnt12)^ 
The volume and type of tickets will help establish priorities for
establishing procedures that can be automated for first tier support and
for future phases that do not require pilot members to contact the help
desk and obtain work tickets.

An automated way to establish a new identity should established early in
the pilot phase, so that participants can mint a new ARK identifier
without creating a work ticket.  Initially, a work ticket would still be
generated once the participant was ready to submit the new record though
the match/merge process.

Given the importance of maintaining links from the merged EAC-CPF record
to related resources, a link harvesting protocol should be developed
early in the pilot phase.  When a pilot phase participant identifies a
match in SNAC with a name they have in a collection description; the
link harvesting protocol would specify how to publish that link in their
HTML display of their collection description or through some other
mechanism (perhaps through an extension to the sitemap protocol, along
the lines of how ResourceSync works).  Procedures would be established
to then notify SNAC to harvest links from the participant, and the SNAC
“related collections” section would be automatically updated.  Such
updates would be based on a “linked data” technology rather than the
submission of XML files.


Pure RDF architecture

#### Current State Conclusion


The current systems functions well enough for researchers and other
stakeholders to see large data sets fully processed. These systems will
benefit from additional work to make them more mature in the usual ways
that software develops: robustness, testing and QA, documentation,
examples, consistent API. Most of the current software will be used in
the production product.

#### Required and Planned Functionality (All authors)


(We need to break out each item into UI functionality, and API
functionality.)


#### Documentation

Every aspect of the system requires documentation. Most visible to the
public is the user interface for discovery. Maintenance will be
complicated, and our processes are somewhat novel, so this will need to
be extensive, well illustrated with screenshots, and carefully tested.

Documentation intended for developers might be somewhat sparse by
comparison, but will be critical to the on-going software development
process. All the databases, operating system, httpd and other servers
need complete documentation of installation, configuration, deployment,
starting, stopping, and emergency procedures.

It is probably wise to choose a wiki-like documentation system at the
outset of the project.

