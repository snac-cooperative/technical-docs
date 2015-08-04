## General Notes

* Lots of institutions are plugging into snac

Nara Website Platform (for information access for the pilot group and snac team)

* Want a cross between a website and a facebook page
* Jive platform (internally)
    * They are wanting to expand it to an external collaboration effort (for the external research community)
    * Blogs, other collaboration tools
    * [Website](https://www.jivesoftware.com/) 
* Other Suggestions
    * [Ning](http://www.ning.com)

## SNAC Coop Launch 

* Possible Dates
    * Sept 28-30
    * Sept 30-Oct 2
    * Oct 14-16
    * Oct 21-23
* Pilot Policies and Procedures
    * Learn as we go, ask questions in meetings
    * Really put our heads around the participants
    * Best Practices Team: Daniel, Amanda, Jerry


## [ArchivesSpace](http://archivesspace.github.io/archivesspace/doc) Presentation

* Uses MySQL
* 4 Accesses
    * Public UI (8080?)
    * Staff UI (editing)
    * API interface (8089)
    * Solr (8090) search interface
* JSON Model
* 4 Types of Agents (They all inherit the Agent data model)
    * Corporate Entities
    * Persons
    * Families
    * Software
* Other Links
    * [Technical Architecture](http://archivesspace.github.io/archivesspace/doc/file.ARCHITECTURE.html)
    * [API Reference](http://archivesspace.github.io/api/)
    * [Test Interface](http://test.archivesspace.org:8080) User: admin, Password: admin
* Other Notes
    * Background Jobs creation: do bulk imports in the background
    * Look at New Person Agent (agents/agent_person/new) for their creation UI.  It's not very user friendly.
    * BiogHists
        * They have a wrap-and-tag editor.  (Type '<' to get the options for tags -- inline options are limited)
        * Allow subnotes of type chronList, etc.
    * Sounds like a front-end for what we're planning to do. 

## Match/Merge Presentation (Ray, Yiming)

* Postgres, Cheshire, Python 2.x
    * SQLAlchemy ORM, libxml (xml parser), setuptools packaging
    * Using SQLAlchemy, can switch SQL backends easily (ish)
* Matching
    * ULAN is not being used.
    * VIAF loaded into Cheshire
    * Record groups that ties all the matching records together
        * They are not merged at this point.  If they match, they are linked together by pointers within Postgres
        * There is also a maybe same as table that shows possible matches
    * EAC is input, loaded into Postgres, then 3 stage process
        * Connect exactly matching records (if available)
            * Matching an exact normalized name (identically)
                * Note: What happens if there is a match that both don't have dates?
        * Use Cheshire to find possible match through VIAF (if the exact match above failed)
        * Merge
    * When loading an EAC record, it just stores the filename (not the entire data)
* Matching in detail
    * Names are the most important attribute
    * Finding candidates using names
        * Exact name with normalization (just in postgres? or also VIAF/cheshire?)
        * Name components as keyworkds (Cheshire only)
        * Ngram (overlapping 3-char sequences) matches (Cheshire only)
    * Validate returned candidate records
        * Either accept candidate as a match, maybe a match or reject candidate
    * Generating Candidates
        * Look up in cheshire
        * Look up in postgres (exact name only)
        * Try to parse Western names
            * name order, suffixes, epithets, middle names
            * British library records were the most egregious for this (Lance, Eveline Anne, Nee La Belinaye; Wife of T Lance, the younger,...)
            * Using Python Library: nameparser (note: I don't think it would work well with name identity strings)
    * Validation of candidates
        * Edit distance + string length
            * Smith vs Smythe
            * Zabolotskii, Nikolai Alekseevich vs Zabolotskii, Nikolai Aleksevich
            * For Western names: name part to name part comparison, initial to name comparison
                * Different strengths of match for each component
            * Mistakes (some differences) in Matches of last name are likely false positives and are ignored (according to Yiming)
            * Matches where the last name is a good match, but there are mistakes/differences in first names, then they are flagged as maybe matches (ex: initials vs full names)
        * Dates of activity
            * Within some tolerance depending on the era
            * Flourished vs birth/death
* State of the database
    * 6 million input
    * ~3 million unique groups
* Spirit Index
    * Ghost-written records
        * Franklin, Benjamin, 1706-1790 (Spirit)
    * Special exclusion
* Merging
    * Record Group is marker for records to point to if they think they are the same entity
    * Record Groups can be invalidated (valid flag)
        * As a way to break apart groups (there is a tombstone record left, so that the improperly merged record could still be generated)
        * keep a linked list of record groups that have been invalidated
    * ... exact matches and authority 
    * Create output
        * Record assembly
            * In case of source changes, no reprocessing needed
            * Output script detects if record changed by an update
            * Lots of legacy code from v1
        * Cancelled records (split, merged, record prior history)
            * Creating tombstones if these things happen
    * Final merged records are only just in time created (dumped out at the end; no merged record stored in the database, but always created from the sources)
    * Combining
        * They say they are not combining biogHists (but this is happening: see Richard Nixon)
        * Brian suggested that if the humans edit the combined biogHist, then that's what is shown, but the others are kept around hidden in a tab to view the originals
        * There is a focus on the XML documents (EAC-CPF) as the canonical record; not a current database that can handle multiple versions of various pieces of the records.
* Discussion
    * Two dimensions to editing a record (EAC-CPF XML), according to Brian
        * EAC-CPFs that are JIT merged; how do you edit that?
        * Put the edits upstream and rerun the merge
    * Ray is still talking about looking at the XML files and merging based on diffs, for example
    * Any new batches that will be coming in, they would just get added to the list (and it would run through the same merge process, maybe flagged as human or something)
        * So, batch uploads would be processed just like a human editing the record
    * Brian's proposal
        * Refactor the match and merge processes
            * Match is useful in a lot more contexts, like archivesspace hitting against
            * Merge process should use the same API to update the database that the front-end uses (back-end and front-end use the same api)
                * Daniel likes this and agrees
    * Ray didn't use a database with all the information because they wanted to keep all the pieces (pre-merge) separate
    * We need to address multiple edits (or re-edits)
        * An editor does a lot of work, then a batch process (or another editor) overwrites or changes that work, that original editor might get offended
            * What is the policy that a person/process can edit the record
            * Right "now," that policy is that they can log in.
    * **The proposal doesn't allow for automatic merging**
        * Identity Reconciliation will just flag possible matches, but will not try to automatically merge records.
        * A person would have to see that there are X incoming records that match, and they needed to be included (by hand merge)

