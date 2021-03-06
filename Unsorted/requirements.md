
See "comment:" for comments, discussion, todo, etc.


#### Governance and Policies, etc.

- Data curation, preservation, graceful retirement

- Data expulsion vs embargo vs mark as deleted vs physical record delete vs delete from all tapes/disk/media

- Duplicates, backups, restore, related policy and technical issues

- Broad pieces that are missing or underdeveloped [Laura]

- Refresh relationship with OCLC [John, Daniel]


#### List of requirements

This is the definitive list of all requirements, briefly. Anything the application needs to do must be in this
list. Each item and group of items is explained in detail later in the document. Being a "list", this includes
only sufficient detail to disambiguate items.

* User Management
    - authentication
        - user creation
        - authentication
        - account maintenance
    - authorization
        - user/group/other (ugo) with read/write (rw) privilege system, typical for Linux
        - admin tool for group privs
        - create/edit/delete groups
        - create privs matched to API functionality
* User Interface
    - search/discover cpf data; need a list of filters/facets
        - dashboard
    - dashboard content/tabs
        - search history
            - clear history
            - search history
        - work status
            - change sort order (maybe version 2)
    - system/work flow messages
    - account settings
    - social media
    - web admin (for admins only)
    - institutional admin (for institutional admins only)
    - moderator admin (for moderators only)
    - edit cpf data
        - edit UI
        - per field data validation
        - record validation
        - user message system in UI
        - work flow
        - dashboard for workspace, task list
* Generated Documents
    - available reports
    - EAC-CPF export
    - RDF/Turtle triples
    - JSON-LD


- split merged records, know that some record consists of merged records

  - split UI
  - work flow
  - ARK assign, deprecate (generally: manage ARKs)
  - dashboard

- merge records

  - Identity Reconciliation (IR)
  - search/view merge candidates
  - work flow
  - dashboard
  - manage ARKs

- algorithmically determine merge candidates

  - IR
  - name-string parser
  - date parser
  - architecture of identity, expandable design
  - authority and controlled vocabulary management

- work flows

  - may want wild-west non-locking edits
  - may want locked, moderated work flow
  - ability to issue reminders
  - integrated email notification
  - "watched" records (might be reporting, and not specifically work flow)

- reporting

- help desk

- issue tracking

- test driven development

- data integrity testing

#### Requirements from Rachael's spreadsheet


- Programmers contribute some time to help with technology side of the gap analysis of institutional capability

- We need a concrete plan for persistent IDs.

  - We need to manage base HREF stubs that are combined with persistent IDs to form working URLs. Ideally, all
    the URLs could be composed via a format string (printf), so we could just store the ID, HREF stub, and
    format string and be done with it. However, some URLs have interesting issues that require code and thus
    exceed the abilities of normal format strings. We can certainly roll out an early version with format
    strings, and add some clever functions later as necessary.
    * **RH: I don't understand this.  If we're generating persistant IDs, then we don't need to write "clever functions"**

- Do we need any additional requirements for related name linking, or more accurately identity linking? Each
  identity has and ARK which is a persistent ID with an assciated URL. Use cases for identity links:

  1. SNAC links one identity to another internally based on relations between identities

  1. SNAC links to itself as a name authority

  1. SNAC links to external identities

  1. SNAC links to external archival resources

  2. External resources link to SNAC as an authority. (Tom comment: is SNAC also an archival resource?)

- Clarify: the co-op version 1 is not going to support bulk data ingest
    * This may be a simple bi-product of the Rest API being available

- Clarify: the co-op version 1 is not going to support bi-directional data exchange and update

- Do we need full delete? For example, a CPF contains something illegal and must be fully deleted. How do we
  delete from backups? Are either of these even required by policy?

- Are we assuming that data from the web browser has been sanity checked before hitting the server? (Yes, by
  the data validation engine)
    * **Todo: Where will the engine live, in the browser (Javascript) or on the server?**

- Does the server need to save temporary edit data prior to writing the data to the cpf database? For example, what if
  someone enters "19th century" in a date field? It isn't valid, but we need to save their work. (Yes, we need to save invalid user input, and give the user a useful message for each type of data validation failure.)
    * **Todo: Where does the data validation engine live?  If we make that part of the UI, it may live on the browser as a downloaded JS engine**

- We need to sanity check any links we create, especially links back into SNAC.

- Don't forget (to create) the X-to-CPF field mapping documentation, and this ties in to the "CPF data contributor's"
  guide (Below)

- We need the "CPF data contributor's" guide.

- What authority work will we be doing?

    - For example, holding institution ISIL identifier, name, address, contact person, etc.

- What authority data from other sources do we cache locally?

    - We need examples of this, as well as a process to manage those resources. It is important to know where
      the data came from, technically how it was acquired, the date we acquired it, and some methods of
      updating the current local cache. This implies that all external data we use has internal persistent
      ids.

- Create detailed functional requirements for controlled vocabularies, and a detailed implementation
  specification.

- Clarify: versioning is per-record, not per-field.

- Need a watch/notification API. It needs a canonical name. Is there an off-the-shelf event monitor that will
  easily integrate with the web REST API and work flow manager?

      - We can write our own status and staging API. It only requires modest SQL schema work. Most of the
        necessary data is already planned for other features. For example, records can be locked by a user, we
        know who has the lock, we need administrative functions for unlocking and transfering locks, the work
        flow explicitly lays out the process for each user interaction with the application.

- Clarify: Are we integrating SNAC and ArchiveSpace in co-op version 1? Will ArchiveSpace have to use our REST API?

- How is embargo implemented at the database level? What are the requirements for embargo?

- comment: Clarify and/or verify: Technical review vs content review is handled by a combination of roles and
  work flow.

- comment: Reports: Where are we keeping the Big List of All Reports?

- comment: Clarify: row 43, (unclear) Consider implementing inked data standard for relationship links
  instead of having to download an entire document of links, as it is configured now.

- Search: need the Big List of Search Facets, and someone needs to verify that Elastic Search can do facets.

- Does co-op version 1 have a timeline visualization? Does it have a "sort by timeline"?

    - What does it mean to sort by timeline?

- comment: Clarify: What is a context widget? - row 52, Continue to develop and refine context widget. (technical
  requirements unclear)

- comment: Clarify: we need requirements for citations, and details about where they integrate with the rest of the
  system.



#### List of Application Programmer Interfaces (APIs)


The following include both direct programming language intefaces, and REST interfaces. We need to determine
which (REST/direct) is available for each. Modifying data should probably go through authorization and should
probably be subject to work flow, and that implies that the work flow has a REST interface, and that the REST
interface is the only public interface.

- Identity Reconciliation (IR) (direct)

- work flow manager (direct, )

- name string parser (direct)

- date parser (direct)

- data validation (direct)

- web UI messages (direct)

- work flow messages (direct)

- record watching (REST?)

#### Work flow engine

The work flow engine (wfe)is a simple state machine that exists only to direct work flow for each REST request, and
editing stages. The wfe does no work, but directs what work is done and the order of work. The wfe is can run
small functions in two categories:

1. test functions that return a boolean based on the underlying state of the application, or user work stage

1. action functions that perform a task

The work flows are encoded in a 4 column table which is presumably more or less legible to
non-programmers. There is only one state table for the entire application.

See the work flow engine readme for more information. [Work flow engine readme](http://gitlab.iath.virginia.edu/snac/Work-flow-engine)


#### Maintenance Functionality


Maintenance falls into four areas: discover, split, merge, and edit.

- Discovery is the process of finding errors.

- Splitting is the process of distributing data from one description
    to two or more new description. This may involve descriptions that
    have been incorrectly merged.
- Merging is the process of combining two or more separate descriptions for the same CPF identity into a
  single description.

- Editing is the modification of descriptions.

We will build a maintenance system based on a core of researchers
working in a moderated environment. Primarily, the people involved in
maintenance in the pilot stage will be professional archivists and
institution-affiliated experts with a vested interest in the data. In
the future, the maintenance function may be opened to highly qualified
(perhaps amateur) content experts. The software must therefore support
policies such as vetting and moderation so that we avoid the pitfalls of
unregulated crowd sourcing.

The system will require changes to be reviewed by a moderator before
becoming part of the production system. Administrative policy may
streamline these requirement, but the software functionality needs to
exist at the most granular level for which we can imagine reasonable
business logic. For the sake of security and general peace of mind,
every change to the system must be captured (aka versioning) in an audit
trail, and there are no destructive changes. For example, there is no
"delete" per se, because the delete feature only hides descriptions from
public view. Updated descriptions will be subject to version control so
changes can be rolled back.

#### Functionality for Discovery

The discovery tools for maintenance may be somewhat different from the
normal discovery tools for scholarly research. We have a standard
discovery tool, but we almost certain need additional tools that
identify descriptions that are more likely to need manual merging or
splitting. We assume that as part of some maintenance workflows, a
person will go fishing for merge/split problems. We will need a user
interface and functions that support this focused discovery.

Users will have individual accounts, so we can enable a search history,
internal bookmarks, and various saved reports (assuming faceted search
where it could take many mouse clicks to accrete a specific search).

#### User interface for Discovery

#### Functionality for Splitting

comment: Add prose to explain how splitting interacts with the work flow (historically called the "queue").

comment: Add prose to cover the manual splitting of single record components (bioghist) into multiple parts.

Keeping in mind that our descriptions are authoritative, and will be referenced via persistent identifier
(ARK), it will be necessary to de-authorize or invalidate the ARK of a description which has been split. The
ARK server will note the new ARKs of the resulting descriptions in both human readable, and machine-actionable
formats.  Outside parties with an invalid ARK will probably have to manually update their descriptions, since
the entity name is too confusing for a computer to disambiguate. (Although we can easily create a report of
deprecated ARKs on a per-institution basis.) When merging descriptions, the main ARK will be retained, and
merged ARKs can simply redirect to it.

Note: determine which operations require a new ARK, either due to the old ARK being so much changed as the
original reference is meaningless, or other causes TBD.

Having found a description in need of splitting, we need UI to support
creating one or more additional descriptions. This should have a "save"
feature so that the work can continue over time. This implies that we
also mark descriptions that are being worked on as "being worked on"
that others don't duplicate the work. Completed splitting is "reviewed"
by moderators before being "posted", where posting makes the
modifications visible to the standard discovery tools. There are also
some issues in how we manage ARKs of split descriptions.

comment: confirm collaborative editing is not a requirement

comment: confirm that locking is a requirement

In theory, several people in separate locations could collaborate in real time on description
maintenance. However, that type of collaboration is fairly complex. We don't want to support collaborative
description splitting in the first version, so we need a feature to "lock" descriptions. Which means we need
mechanism for seeing who has the lock, and for sending that person a message. Unless we're going to expose the
email addresses of our users we will need an anonymized email system (or email forwarding system).

An ideal split UI will easily allow text/fields to be selected and moved
to one of the possibly multiple splits, via a single mouse click or
simply drag-and-drop. This feature needs undo, or at least a reciprocal
ability to move data from a new split back to the original description.
Meanwhile the UI has to display multiple descriptions in some clear
manner. It is probably a good idea to have a snapshot of the original
(pre-split) description to refer back to. This process can be quite
confusing and time consuming, so people need to know what it was they
started with, even when they are well along in the splitting process.

Any description that has been manually modified in any way should have
special properties that prevent the automated match/merge pipeline from
touching that description record in the future. We also need to be able
to search based on the types of modifications that have been performed
on descriptions, both for reporting, and for future manual modification.

During the split, new descriptions will be created, but will remain
locked, and invisible outside the splitting operation. These
descriptions can be deleted by the person doing the split, but are not
visible to other users.

When the split data is ready, the user goes into the review and post
phases. Review saves all the work, and presents some final, read-only
view of the work. Review also does a validation of the description/data,
and gives meaningful messages when validation fails. The "post" button
should come with various warnings and notifications and the typical "are
you sure". Posting will save all work, perform the any required database
bookkeeping, and unlock all the involved descriptions.

One type of bookkeeping during the post phase is managing ARKs. The ARK
of a split description must be deprecated, and new ARKs created for all
the splits. The deprecated ARK will have a "permanently moved" redirect
in the ARK system that gives the new ARK values and the names associated
with the new authority descriptions in both machine actionable and human
readable formats.

We need a feature to abandon the split, and this feature needs an "are
you sure" check.

Descriptions that are in the process of being modified should have some
kind of icon/warning in the normal discovery interface, just so
researchers know that the description in question may soon change.

To review split:

1.  lock original description,
2.  mark descriptions as being maintained,
3.  give descriptions a locked-for-maintenance icon in the discovery
    interface,
4.  view original (unsplit) description,
5.  create new descriptions (also locked),
6.  copy data between descriptions,
7.  undo copy,
8.  enter new data into any of the description fields,
9.  edit data in any of the description fields,
10. delete new descriptions (aka undo create),
11. "done splitting",
12. undo "done splitting" (go back into splitting UI),
13. review split (just a read-only UI?),
14. moderator posts  the completed split,
15. revert entire split,
16. contact person who has locked description,
17. see when description was locked,
18. save progress,
19. user function to view descriptions I have locked,
20. admin function to view locked descriptions by user,
21. choose one of my locked descriptions to continue work.

#### User interface for Splitting

#### Functionality for Merging

We need to allow our experts to merge descriptions. This may be far more
common than splitting since the automated pipeline was designed to only
merge when the evidence was overwhelming.

The process begins with discovering two or more descriptions for the
same CPF entity. Discovery history needs to allow persistent research
across sessions.

When starting description maintenance, the descriptions involved are
locked to prevent other users from modifying them. The system notes this
lock and makes the locked state visible in the discovery interface. It
seems safe to assume that one of the merged descriptions will become the
authoritative record description. This single description will be
retained, and the other merged descriptions marked at deleted. We can
retain the ARK of the single retained description. The main description
will be copied, with the original still visible to the discovery tool,
albeit marked as "under maintenance" or similar. The copy will be
modified by the merging process, and will not be visible until
completion of merging.

The system might be able to automatically join the descriptions into the
main description, but we always need the ability to edit the main
description. Secondary descriptions should become read-only, and be
locked from any modifications. We need the ability edit each field of
the main description, and we need to be able to create new fields,
especially alternative name forms. Merging needs the usual save, undo,
and abandon features.

When merging is complete, the new description is validated, and sent to
a moderator for review. The moderator may post or "send back" the
description for the editor to make additional changes.

During the post phase, bookkeeping is done. The now-deprecated merged
descriptions are marked internally as deprecated, and their ARK values
set to redirect. The original main description is also deprecated,
replaced by the newly merged description, and the new description open
for public view.

Every description retains its modification history.

To review merging:

1.  discover two or more descriptions needing merge,
2.  lock all descriptions,
3.  show all locked descriptions as locked in the discovery interface,
4.  copy the main description,
5.  lock and hide the copy from discovery,
6.  allow merge save, allow merge abandon,
7.  allow the public to contact the maintainer,
8.  auto-join all description to the main description (if possible),
9.  lock secondary descriptions to be read-only by the maintainer,
10. allow editing of the main description especially adding new fields
    such as alternate name forms,
11. validate merged description,
12. send merge to review,
13. review locks changes and notifies a moderator,
14. moderator can send back,
15. send back unlocks and notifies the maintainer of additional required
    work,
16. moderator can post the merge,
17. post performs ARK deprecation,
18. description deprecation,
19. locks and hides original,
20. makes merged description publically visible.

#### User interface for Merging

#### Functionality for Editing

Modifications we expect include but are not limited to: spelling
corrections, date corrections, editing or expanding biographical data,
and fixing typographical errors. Editing also includes adding, deleting,
and correcting relations between descriptions. Metadata such as the URL
of the original finding aid may also be updated. The maintenance system
also needs to support bulk data edits of several types.

#### User interface for Editing

#### Admin Client for Maintenance System

Does this mean the admin dashboard?


#### System Administration


This is boilerplate server administration, for the most part.
Preservation of original material may not be necessary. Since our data
is derived from original sources and we know the location of those
sources, individual lost descriptions could be restored from the
originals.

The simplest server model has shell logins for sysadmins, DBA, and
developers via SSH. If the institution hosting the project can only
allow employees on the server, then we may need to create a new server
strategy.

One option is to do our hosting on Amazon. If so, what is the hosting fall back if Amazon has an outage? If we
host with Amazon, do we have to pay extra for multiple availablity zones? Where does Amazon house offsite
things like tape backups? If we're using Amazon we will have to research the list of things that go wrong
since our current sysadmins are experienced with the model of local hardware colocation.

One common failure of standard server practice is to assume that backups
are working. We should test our backups on some schedule to verify that
all the files have been restored as expected. File checksums and file
counts work well for this verification.

All storage systems (including RAID arrays) are vulnerable to undetected
data corruption known as bit rot. Historically this issue has been
largely ignored (because it is rare). Anti-bit-rot parity error
correcting file systems (ZFS, Btrfs) may not be production quality at
this time. If we want to deploy an anti-bit-rot technology, our
alternative may be limited to using Par2/Quick Par/Parchive.

#### Community Contributions


Researcher interface/functionality including public facing discovery and
dissemination (All, especially Brian)

In addition to current and planned features (need a list) we should
consider the following:

- Expose all CPF descriptions to search crawlers so that Google and Bing can index our data. Google has
  started using schema.org for improved hinting about certain kinds of data.

- Expose the facets of our data as web pages or directories of web pages so that the facets can be browsed
  outside XTF, and indexed by Google and Bing.

- Administration interface/functionality, including private/admin facing, internal discovery tools, and data
  modification (Tom, Brian, Rachael, Ray)

The last item above is available only to management and editorial
admins, but not required by any other users. Not all admins should (or
need) all admin features. Admins need to create accounts, reset
passwords, and lock accounts.

If we have a vetting process then we need to know the related business
logic that we will support with UI, code, and database tables/fields.

Many reports will be limited certain roles. Admin users will likely be
heavy report users.



#### ArchiveSpace Feature Planning via Brad

This section will require some discussion (conference calls) with Brad
and others.
