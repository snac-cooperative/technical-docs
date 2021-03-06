## Technology

The long-term technological objective for the Cooperative is a platform that will support a continuously expanding, curated corpus of reliable biographical descriptions of people linked to and providing contextual understanding of the historical records that are the primary evidence for understanding their lives and work. Building and curating a reliable social-document corpus will involve a balanced combination of computer processing and human identity verification and editing. The next step towards realizing the long-term objective is to transition from a research and demonstration project to a production web service.  From a technical perspective, this means transitioning from a multistep human-mediated batch process to an integrated transaction-based platform. Instead of the data being passed along from one programmer to another, the architecture will automate the flow of data in and out of the different processing steps by interconnecting the processing components, with events taking place in one component triggering related events in another. For example, the addition of a new descriptive record will lead to automatic updating of graph data in Neo4J and updating the indexed data in the History Research Tool. The coordinated architecture will support both the batch ingest of data and human editing of the data to verify identities and refine and augment the descriptions over time. 

Using techniques developed in the research and demonstration phase of SNAC, computer processing will be used to extract and ingest existing name authority and biographical data from existing archival descriptions. Identity reconciliation, i.e. matching and combining two or more descriptions for the same person, organization, or family, has relied solely on algorithms in the research phase. While identity reconciliation techniques will continue to inform the reconciliation process, Cooperative professional editors, beginning with librarians and archivists though expanding over time to include allied scholars, will verify identities and curate the data. This two faceted approach, combining intelligent computer processing and professional editing, will enable building a large corpus of networked social-document data that is not constrained geographically or by historical period, and over time establishes an expanding core of reliable identities within the overall corpus. (See Appendix 4 for a diagram showing the relationship between certain/uncertain data and dense/sparse evidence for identity resolution.)


### Current SNAC R&D Technology Platform

Current SNAC processing employs a complex sequence of steps that produces a collection of biographical descriptions used to produce the History Research Tool. 

* Acquire source data from archives, libraries, and OCLC WorldCat (EAD-encoded finding aids; MARC21; and ad hoc authority and biographical data sets).
* Extract data essential to assembling descriptions of persons, organizations, and families into standardized descriptions (EAC-CPF).
* Load the name and, when available, the existence dates of the entity described in each EAC-CPF instance into the PostgreSQL database the multi-component platform that attempts to match and combine different descriptions of the same identity into a single description.  
* Names in the PostgreSQL database are matched against more than 25 million identities in the Virtual International Authority File (VIAF), maintained by OCLC. Cheshire, an XML-based indexing tool developed at the University of California, Berkeley is a key component of this step. Based on a sequence of matching attempts, additional data is associated with each identity in the PostgreSQL database.
* Based on an evaluation of the match processing results, a final set of EAC-CPF instances is produced to serve as the basis for the History Research Tool. For instances that are deemed to be for the same identity, the different instances are combined based on complex merge algorithms; instances deemed to be possibly for the same identity but lacking sufficient confidence to be merged are related to one another as "maybe the same as;" and finally instances that do not match or weakly match are passed into the results as is.
* A subset of the EAC-CPF data is extracted and loaded into Neo4J, a graph database. The graph database serves two primary functions: support of graphical representations of the SNAC social-document network, and exposing a subset of the SNAC data as Resource Description Framework (RDF) Linked Open Data (LOD).1 
* The resulting set of EAC-CPF instances is used to produce the History Research Tool. The key underlying platform is XTF, an open-source XML-based publishing tool developed at the California Digital Library. 

Transforming the existing platform into a platform that supports both ingesting of large batches of data but also manual maintenance of the data will require a reconfiguration of major components of the current underlying technology. Two major existing components will be retained with minimal modification during the pilot: the "back end," the processing used to extract data from existing descriptive sources and assemble it into EAC-CPF instances; and the "front end," the History Research Tool. While the code and technology for these two components of the SNAC infrastructure would benefit from additional development and refinement, each is sufficiently robust and functionally complete to remain largely unchanged during the pilot. The intermediate technologies used in loading and matching of the EAC-CPF will need to be thoroughly revised, retaining existing functionality but in a configuration that will support both batch and manual maintenance. One component, the processes used to merge or combine matching EAC-CPF instances will be deferred to a later stage of development (to be described below). Finally, two components that will be developed in the pilot are entirely new: an API to support both batch processing and an Editing User Interface, and development of the Editing User Interface itself. While the transformation of the underlying technology is underway, there will be a one-year pause in batch ingesting new data in order to focus programming resources on the essential development work needed to go forward. No large batches of new source data have been solicited for the pilot, although pilot member institutions will contribute batches of data for use in first testing and then bringing online the batch ingest function of the Cooperative. During the pilot, new sources of batch data will be solicited for the second two-year phase of establishing the Cooperative.

### Data Maintenance Store

In the current processing stream, the EAC-CPF instances are placed in a read-only directory as the primary data store.  A small number of select components (name strings) of each EAC-CPF XML-encoded instance are loaded into a PostgreSQL database.  In order to support dynamic manual editing of the EAC-CPF instances, it will be necessary to parse the entirety of each EAC-CPF instance into PostgreSQL tables.2 Parsing all (or most) components of the EAC-CPF instances into SQL tables is necessary because no open source native XML database will efficiently support the essential maintenance functionality required, in particular effectively managing editing transactions at the component-level of each EAC-CPF instance.3 MarkLogic would enable maintaining the data in XML, but it is an expensive commercial platform. The most robust of the open source native XML databases, eXist, does not support transaction management. Further, EAC-CPF was not designed as a maintenance format, but rather as a communication format and, with this in mind, was intentionally designed to facilitate the serialization of the data into and out of SQL environments.4

The PostgreSQL Data Maintenance Store (DMS) will represent the core foundation of the SNAC technology platform. It will hold the crucial SNAC data, including: the parsed EAC-CPF instances, version control for modified data fields, editor authorization privileges, editor work histories (e.g., edit status on individual EAC-CPF instances), local controlled vocabularies (e.g., occupations, functions, subjects, and geographic names), and workflow management data. An open source authentication system will mediate editor/user access to the DMS. All other component subsystems will rely in large part on the DMS, with several of the DMS functions being generalized to be effective through the component APIs. Additionally, nearly all reporting for editors and administrators will be based on the DMS. We will use an open source reporting package, but the reports themselves will need to be generated by the DMS. With the DMS as the core foundation of the SNAC technology platform, evolving features of the component subsystems will often require further development of corresponding functions in the DMS.

### Identity Reconciliation

A major focus of the SNAC R&D has been on identity reconciliation. A fundamental human activity in the development of knowledge involves the identification of unique "real world" entities (for example, a particular person or a specific book) and recording facts about the observed entity that when taken together uniquely distinguishes the entity from all others. Establishing the identity of a person, for example, involves examining available evidence, including the existing knowledge base, and recording facts associated with him or her. For a person, the facts would include names used by and for them, dates and places of birth and death, occupation, and so on.  Establishing identities is an ongoing, cumulative activity that both leverages existing established identities and establishes new identities. Identity reconciliation is the process by which an encountered identity is compared against established identities, and if not found, is itself contributed to the established base of identities. The networked computing environment presents opportunities for using algorithm-based inference methods for comparing newly encountered entities with established identities to determine the probability that a new entity represents the same person or thing as an established identity. In this way, the ongoing expansion of the base of reliable identities is an interplay of human research, knowledge recording, and computational methods. 

With the emergence of Linked Open Data (LOD) and the opportunity it presents to interconnect distributed sets of information, new names for entities are introduced, namely the URI's used to provide globally unique identifiers to entities. In order to exploit the opportunity presented by LOD, it necessary to include these URI's in the reconciliation process. SNAC assigns its own identifiers (ARKS) because doing so is essential to effectively managing the identities throughout the processing and maintenance. Even if this were not essential for managing the workflow, the majority of the identities in SNAC will not be found in other sources such as VIAF, and thus the SNAC identifiers and associated data that establish the identity are likely to be unique, at least in the near term.5 For those identities that do overlap with VIAF, SNAC processing takes advantage of the VIAF reconciliation to associate the VIAF identifier as well as identifiers for Wikipedia and WorldCat Identity.

SNAC identity reconciliation processing, while sufficiently reliable for the research and demonstration purposes of SNAC, is inadequate for meeting the long-term objective of building a large corpus of networked social-document data that, over time, establishes an expanding core of reliable identities within the overall corpus. This objective will require a calibrated balance of computational methods and human verification of identities, and it must be possible to refine the balance over time as new understandings and insights into the processing arise, and as new data patterns are encountered in new sources data. To inform the refinement of the match processing in balanced relation with the human verification, an ongoing quality review regimen needs to be put in place that supports efficient evaluation of match algorithms that employs both benchmark data used in testing and revising algorithms, and feedback for revision of algorithms. Revision of the match processing will require the following:

* Developing an Identity reconciliation services module with API that supports batch match evaluation processing, evaluation of identities that are manually added through the Editing User Interface, and OpenRefine users interested in reconciling names against SNAC identities.6
* Establishing a quality review process that employs benchmark or "ground truth" data to be used in evaluating and refining match algorithms and a regimen of ongoing human review that is performed collaboratively by Cooperative staff and members. The benchmark data will be initially based on the in-depth match quality evaluation that was performed by an outside consultant during the SNAC research phase. The consultant’s report, describing in detail both methods employed and results will also serve as the basis for training Cooperative staff and members.

The major components of the current match or identity reconciliation processing are Cheshire and PostgreSQL. For each EAC-CPF instance, Cheshire is used to perform a series of queries of the Virtual International Authority File (VIAF), with the results of the queries (positive matches, possible matches, and non-matches) being stored in the PostgreSQL database for use in later processing. The Cheshire index may be replaced because it has proven to be problematic to maintain, though the final decision will be based on ongoing testing of alternatives, such as ElasticSearch, to ensure that that any replacement for Cheshire will provide the performance and functionality required. 

There are three major reconciliation results: reliable match; possible match; and no match. These determinations are based on string matching combined with the relative "identity strength" of each name string, that is, the extent to which the name string is likely to uniquely identify a person, organization, or family in a large population. Factors considered in determining the "identity strength" of a name string are the following: length of the string, the number and length of name components, the order of the name components, the presence or absence of life dates, how common the surname is, and others. Another way of understanding "identity strength" is as a determination of the quantity and quality of available evidence.7 If two matching strings contain sufficient evidence, then two strings are deemed a reliable match; if the strings do not contain sufficient evidence, then the strings are flagged as a possible match.  

The reconfiguration will enable the identity reconciliation processing to be integrated with the extraction/assembling processing, the data maintenance platform, and the editing user interface.8 EAC-CPF extracted and assembled using existing archival descriptions (EAD-encoded finding aids, MARC21, or existing non-standard archival authority records) will be batch ingested into the SNAC Cooperative PostgreSQL database; when ingested, the Identity Reconciliation module will be invoked to flag reliable matches, possible matches, non-matches. The results of the identity reconciliation evaluation will be available to editors through the Editing User Interface to assist them in verifying identities.  When editors create new identity descriptions or revise existing descriptions, the Identity reconciliation module will be invoked to provide the editors with feedback on likely and potential matches that may be otherwise overlooked when employing human-only authority control techniques. 

Merge processing, that is the automatic merging or combining of EAC-CPF instances deemed reliably to be for the same identity, will be deferred until after the revision of the match processing and developing an effective quality evaluation regimen. The current merge processing combines two or more EAC-CPF instances into one. The combining is primarily cumulative, though redundant data fields are combined as a further step. Once human verification and editing is introduced, any automatic merging of records will necessarily need to respect the integrity of the judgment of the human editors.  The merge algorithms will need to be based on policies developed in consultation with the Cooperative community, taking into account quality evaluation findings and the nature of the components of each description. The engagement of the archivists and librarians thus will be essential in developing an appropriate balance of computer and human maintenance of the data. An informed understanding of the issues will not be possible until the editing platform and editing interface are functional, and the community knowledgeably engaged. 

### Editing User Interface

Developing the Editing User Interface (EUI) is a primary objective of the two-year pilot. The SNAC developers have identified the essential functional requirements for the interface, and extensive user studies with archivist and librarians have been used to refine and substantially extend the requirement list. Because the EUI is dependent on the reconfiguration and development of the data maintenance and identity reconciliation modules, and the development of the Edit API, the development will first focus on engaging the pilot participants by means of wireframes of the EUI, in conjunction with rehearsing established research and description tasks, the order or orders in which such task are performed, and walk-throughs of the steps involved in manually adding, revising, merging, and splitting identity descriptions. These activities and the findings that result from them will inform the parallel development of the maintenance platform. When the underlying data maintenance platform is in place, development of the EUI will commence, informed by the activities described above. As the EUI becomes functional, the pilot participants will transition to iteratively testing and using it to perform editing tasks to ensure that the essential functions are supported and that this support makes the performance of the tasks logical and efficient. Those functions of the EUI that overlap with the History Research Tool will employ a common interface. The bulk of the EUI will be based on JavaScript running in modern web browsers.  


### Graph Data Store – Visualizations and Exposure of RDF/LOD

Neo4J, an open source graph database, is currently used to generate social-document network data as GraphML. The generated GraphML supports the graphic representations of the social-document network in the History Research Tool. In addition, it supports exposure of the SNAC data for third-party use through Resource Description Framework (RDF) Linked Open Data (LOD). The Neo4J component will not require major reconfiguration during the pilot, but will be used as an active source of the social-document network data in place of static GraphML representation. In this capacity, the Neo4J database will provide a number of services: serving graph data to drive social-document network graphs in the HRT; and serving and providing LOD through a SPARQL endpoint and RDF exports for third-party consumption. It will, however, be necessary to integrate both the data ingest and data serving functionality of Neo4J into the coordinated system architecture in order to ensure that the graph data and dependent services remain current with the evolving SNAC data. 

While the initial focus of the Cooperative is on the curation of data, it is interested in using the social-document network data in public graphical displays of the networks and exposing the data as LOD in RDF syntaxes (RDF/XML and RDF compatible JSON-LD) for use by third parties. By exposing the data for use in a variety ways (including making available full EAC-CPF XML-encoded instances) the Cooperative will contribute to global open data initiatives that are intended, among other objectives, to promote use and reuse of data in innovative ways, and to interconnect and interrelate complementary resources that currently exist in isolation from one another.   

Currently there is no existing ontology for archival description, and thus the classes and properties used in exposing graph data expressed in RDF is based on classes and attributes selected from existing, well-known and widely used ontologies and vocabularies: Friend of a Friend, OWL, SKOS, Europeana Data Model (EDM), RDA Group 2 Element Vocabulary, Schema.org, and Dublin Core elements and terms.9 In the long term, it should be noted that the International Council on Archives' Expert Group on Archival Description (EGAD; chaired by the PI) is developing an ontology for archival entities and the description thereof. While the initial focus of EGAD necessarily is focused on developing a clear model of the world based on archival curatorial principles, once this work is completed, the group intends to collaborate in mapping the archival ontology to CIDOC CRM, which incorporates both museum and library description.10


### Component Subsystem Integration

In order to integrate the component subsystems of the Cooperative technological platform we will develop a thin middleware component that routes each request from a client or from an intra-server process to the appropriate individual subsystem based on SNAC workflows we will establish. The middleware component invokes the required functions via calls to the subsystems: Identity Reconciliation, PostgreSQL database, Neo4J graph database, History Research Tool, and Editing User Interface. These subsystems deal with the variety of automated and semi-automated transactions required by the Cooperative platform.

We will develop the Cooperative middleware component as a LAMP application rather then using an open source Enterprise Service Bus (ESB).11 We will use LAMP (with PostgreSQL) for efficiency of coding; flexibility enabled by a very large number of available software modules; ease of maintenance; and clarity of software architecture (that is, the model-view-controller).12 The result will be a lightweight and easy to administer software stack. (See Appendix 11 for diagrams illustrating the basic system architecture.) ESBs contain far more features than required by the Cooperative architecture, and because of the complexity these unwanted features present, it would be less efficient to configure and maintain the middleware using an ESB than simply selecting only the components needed by the architecture from a large library of existing open source LAMP modules.

The thin middleware component will be available via a RESTful API, allowing appropriate third party access to services. A simple example might be a dedicated MARC21-to-EAC converter where a MARC21 record is uploaded, data extracted and transformed into EAC-CPF, and returned in a single transaction. Another example is saving an identity record where the data is written to PostgreSQL, EAC-CPF is exported to and indexed by XTF, and the Neo4j database is updated. The three steps are sequenced by the thin middleware component.  

With respect to potential third party applications, Brad Westbrook from Lyrasis has participated in the technical planning for the Cooperative, in anticipation of extending ArchivesSpace to include a SNAC editing interface. Adding this capacity will enable archives, libraries, and museums using ArchivesSpace to benefit from the Cooperative data, and to link the description of local holdings to SNAC descriptions of organizations, persons, and families.  The PI has also had discussions with Susan Perdue at the Virginia Foundation for the Humanities and Director of Document Compass. Perdue is currently in the planning stages of developing an open source platform to support documentary editing. Incorporating a SNAC editing interface into this platform would be of significant benefit to documentary editors (as a reference resource), and would expand the range of professionals participating in the SNAC Cooperative. Finally, the Staatsbibliothek zu Berlin is keenly interested in interrelating Kalliope with SNAC, contributing 600,000 person and organization descriptions linked to 2.5 million descriptions of archival holdings.
