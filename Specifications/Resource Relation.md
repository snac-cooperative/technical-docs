
### Overview

Archival resources as described in the CPF resourceRelation should have more data than captured by
EAC-CPF. SNAC had begun this work by adding an objectXMLWrap element to the resourceRelation. This XML snippet
contained some MODS or a fragment of EAD. The data includes details about the resource and it's holding
institution.

### Description of new fields and tables

We will add fields to table related_resource, and create new table related_resource_creator (was
related_resource_name). We will have to go back to the originally extracted SNAC CPF files because we need 2
additional fields from MODS (datafield 040$a, 300), and 1 field from EAD (prefercite).

From WorldCat MARC we need:

1) The original WorldCat 040$a "institution identifier" which is either an OCLC Symbol or a Marc Organization
Identifier. We already have this data, and a software pipeline is processing this data into additional fields
in table related_resource, as well as new constellations for each repository. Ideally, the new constellations
will have an address and other geographic information as part of their place element.

2) The 300 extent data which is human readable information about the size/extent of the archival materials


In the case of EAD we need the \<prefercite> element where there is no \<repository> element. We will review
the data since the two elements are not supposed to be interchangable. Element prefercite contains the
institution name and often the address as well, often in a single line. There is often a prefercite when
repository is missing or empty. We there is a good repository, prefercite usually seems to be left out or
empty.

Repository name/info is saved in a constellation, and any resources that need it will link to it via ic_ic as
a foreign key relation. A repository's role is always "repository" or

```
http://id.loc.gov/vocabulary/relators/rps
```

todo: are there other role types? Answer: There sort of can't be, but we need to do something with them.

Need to scan through the data for non-repo, non-orig.

Note: repository is superceded by looking up oclc/marc org code or ead repo.



related_resource_creator is creator aka origination aka originationName as from ResourceRelationstoPostgres.txt

Example: A constellation has resourceRelation to a field book (archival object) written by Clausen, Jens (Jens
Christian), 1891-1969. The related resource creator is Clausen, Jens (Jens Christian), 1891-1969.

Resource language is a many-to-one relation related resource. We use a reverse foreign key from each related
record in the language table, back to the related_resoure.id. This means we have to parse the EAD
langmaterial/language elements.

We do not have language for MARC derived records. While it is possible to get some language info from the MARC
546, the values are discursive as opposed to language codes, or even a textual language name.


```
language.fk_id=related_resource.id and language.fk_table='related_resource'
```

Resource creator name is a many-to-one. Use a reverse foreign key from related_resource_creator.fk_id to related_resource.id.

```
related_resource_creator.fk_id=related_resource.id and related_resource_creator.fk_table='related_resource'
```

The place and address associated with a repository is handled via a place in the constellation. Address data
exists inside a place. Using the on-going oclc symbol/marc org code project, we can create a place element with an
address in the constellation for each repository. In many cases, we will be creating a CPF stub record for the
holding repository, using the fairly extensive information provided by WorldCat and the LoC marc org code
databases.


### New fields

Several new fields are added to the related_resource table:

- repo_ic_id is the ic_id of the repository (institution) holding the resource item.

- res_title is the resource title, from MARC mods/title or EAD unittitle

- res_abstract is the resource abstract, from MARC mods/abstract or EAD abstract

- res_extent is the resource extent aka size, from MARC or EAD physdesc/extent (multiple!)

As noted above, we did not capture the MARC 300 extent, so I will have to parse the original WorldCat records for that data.

The related_resource_creator table is new.


```

--
-- The role aka roleTerm of repo_ic_id is always http://id.loc.gov/vocabulary/relators/rps
-- 
create table related_resource (
    id                  int default nextval('id_seq'),
    version             int not null,
    ic_id               int not null,
    is_deleted          boolean default false, --
    role                int,                   -- @xlink:role, fk to vocabulary.id, type document_type, e.g. ArchivalResource
    arcrole             int,                   -- @xlnk:arcrole, fk to vocabulary.id type document_role, creatorOf, referencedIn, etc
    type                int,                   -- @xlink:type, fk to vocabulary.id type source_type, always "simple"?
    href                text,                  -- @xlink:href, link to the resource
    relation_type       text,                  -- @resourceRelationType, only from AnF, maybe put in a second table
    relation_entry      text,                  -- relationEntry (name) of the related eac-cpf record (should be unnecessary in db)
    relation_entry_type text,                  -- relationEntry@localType, AnF, always "archival"?
    descriptive_note    text,
    object_xml_wrap     text,                  -- from objectXMLWrap, xml
    repo_ic_id          int,                   -- holding repository ic_id, fk to ic_id
    res_title           text,                  -- resource title, from EAD?
    res_abstract        text,                  -- resource abstract, from EAD/
    res_extent          text,                  -- resource extent, from EAD and from original MARC
    primary key(id, version)
    );

-- 
-- This is origination name aka creator.
-- No need for field role aka roleTerm since all these names are creators.
--
-- http://id.loc.gov/vocabulary/relators/cre
--
-- This table can be updated via the web UI, so it needs the id,version,ic_id. Name is simply a string and we
-- will not require them to be SNAC identities at this time. Later we will link these names to constellations
-- via ic_id.
-- 
create table related_resource_creator (
    id       int default nextval('id_seq'),
    version  int not null,
    ic_id    int,                            -- ic_id of this creator, eventually filled in by humans
    name     text,                           -- name of creator of the related resource
    fk_id    int,                            -- fk to related_resource.id
    fk_table text default 'related_resource' -- related table name
);

```
