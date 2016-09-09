
### Overview

Archival resources as described in the CPF resourceRelation should have more data than captured by
EAC-CPF. SNAC has begun this work by adding an objectXMLWrap element to the resourceRelation. This XML snippet
contains some MODS or a fragment of EAD. The data includes details about the resource and it's holding
institution.

See these files:

https://www.dropbox.com/home/SNACCooperativeProgram/ResourceRelationsExamples/ResourceRelationsExamples.xml

https://www.dropbox.com/home/SNACCooperativeProgram/ResourceRelationsExamples/ResourceRelationstoPostrgres.txt


### Description of new fields and tables

We will add fields to table related_resource, and create new table related_resource_origination_name (was
related_resource_name). We will have to go back to the original source files because we need 3
additional fields from WorldCat MARC (datafield 040$a, 041, 300), and 1 field from EAD (prefercite).

WorldCat MARC:

1) (Institutional data gathering is already underway.) The original WorldCat 040$a "institution identifier" is
either an OCLC Symbol or a Marc Organization Identifier. We already have this code, and a software pipeline is
being developed to process this data and gather additional fields. The data will be used to populate fields in
related_resource, as well as creating new constellation stubs as necessary for each repository. When complete
data is available, the new constellations will have an address and other geographic information as part of
their place element.

2) The 041$a, if it exists may have a subfield with a language code. There may be multiple 041$a fields, or
there may be multiple 3 letter language codes in a single 041$a. Daniel's document is unclear about how many
language codes to use. He says it is repeatable, but then refers to language as "it" in the singular. He seems
to intend that we look at all 041 subfields for 3 letter language codes.

```
/data/source/WorldCat/wc_xml_files/wc_232826328.xml
    <datafield tag="041" ind1=" " ind2=" ">
      <subfield code="a">ara</subfield>
      <subfield code="a">heb</subfield>
    </datafield>

/data/source/WorldCat/wc_xml_files/wc_625954442.xml
    <datafield tag="041" ind1="0" ind2=" ">
      <subfield code="a">freeng</subfield>
    </datafield>


/data/source/WorldCat/wc_xml_files/wc_651392673.xml
    <datafield tag="041" ind1="0" ind2=" ">
      <subfield code="a">spalat</subfield>
    </datafield>

/data/source/WorldCat/wc_xml_files/wc_711880498.xml
    <datafield tag="041" ind1="0" ind2=" ">
      <subfield code="a">eng</subfield>
      <subfield code="a">ger</subfield>
      <subfield code="h">ger</subfield>
    </datafield>
```

3) The 300 extent data which is human readable information about the size/extent of the archival materials


EAD:

In the case of EAD we need the \<prefercite> element where there is no \<repository> element. We don't have
prefercite in the objectXMLWrap, so we must re-parse the original EAD. When parsing the original files, it
might be best to gather both prefercite and repository. We will review the data since the two elements are not
supposed to be interchangeable. Element prefercite contains the institution name and often the address as well,
often in a single line. There is often a prefercite when repository is missing or empty. We there is a good
repository, prefercite usually seems to be left out or empty.

Repository name/info is saved in a constellation, and any resources that need it will link to it via ic_id as
a foreign key relation. A repository's role is always "repository" or

```
http://id.loc.gov/vocabulary/relators/rps
```

The only current role types are creator and repository. Every WorldCat record was checked.

Note: The objectXMLWrap/mods repository is superseded by looking up OCLC/marc org code or EAD repo.

related_resource_origination_name is creator aka origination aka originationName as from ResourceRelationstoPostgres.txt

Example: A constellation has resourceRelation to a field book (archival object) written by Clausen, Jens (Jens
Christian), 1891-1969. The related resource creator is Clausen, Jens (Jens Christian), 1891-1969.

Resource language is a many-to-one relation related resource. We use a reverse foreign key from each related
record in the language table, back to the related_resource.id. This means we have to parse the EAD
langmaterial/language elements.

Daniel wants to use language codes from MARC 041, probably $a, and implicitly, we must parse the 041 subfields
for a language code. See ResourceRelationstoPostrgres.txt

```
language.fk_id=related_resource.id and language.fk_table='related_resource'
```

Resource creator name is a many-to-one. Use a reverse foreign key from related_resource_origination_name.fk_id to related_resource.id.

```
related_resource_origination_name.fk_id=related_resource.id and related_resource_origination_name.fk_table='related_resource'
```

Conceptually, places hold addresses, and the constellation as well as parts of the constellation have
places. For a holding institution (aka repository) the address is conceptually part of a place associated with
"the" constellation, either table nrd or table version_history.

Using the on-going OCLC symbol/marc org code project, we can create a place_link record with related
address_line records in the constellation that corresponds to a repository. In many cases, we will be creating
a CPF stub record for the holding repository, using the fairly extensive information provided by WorldCat and
the LoC marc org code databases.

A constellation, or a part of the constellation that has place data uses table place_link.
A place_link relates to zero or more address_line records via reverse foreign key
A place_link relates to zero or one geo_place records (many-to-one linking from place_link to geo_place)

From ResourceRelationstoPostrgres.txt:

Summary of data fields and their database storage location

```
= RepositoryCode: (1:1)  entityId.text
= RepositoryName: (1:1) name.original
= RepositoryAddress: (M:1) place_link.id == address_line.place_id
= RepositoryLatitude: (1:1) geo_place.latitude
= RespositoryLongitude: (1:1) geo_place.longitude
= Title: (1:1) related_resource.title
= Abstract: (1:1) related_resource.abstract
= Extent (1:1) related_resource.extent


Repeatable set (M:1) becomes a related language record
=LanguageName: 
=LanguageCode: 
=ScriptCode: 

= OriginationName: (M:1) becomes a related_resource_origination_name.name
```


### New fields

Several new fields are added to the related_resource table:

- repo_ic_id is the ic_id of the repository (institution) holding the resource item.

- title is the resource title, from MARC mods/title or EAD unittitle

- abstract is the resource abstract, from MARC mods/abstract or EAD abstract

- extent is the resource extent aka size, from MARC or EAD physdesc/extent (multiple!)

As noted above, we did not capture the MARC 300 extent, so I will have to parse the original WorldCat records for that data.

The related_resource_origination_name table is new.


```

--
-- The role aka roleTerm of repo_ic_id is always http://id.loc.gov/vocabulary/relators/rps
-- 

create table related_resource (
    id                  int default nextval('id_seq'),
    is_deleted          boolean default false,

    -- this constellation information
    version             int not null,
    ic_id               int not null,

    -- connection between this constellation and resource
    arcrole             int,  -- @xlnk:arcrole, fk to vocabulary.id type document_role, creatorOf, referencedIn, etc

    -- information about the resource
    relation_entry      text, -- relationEntry — “shortcut” description of this resource
    title               text, -- resource title, from EAD/MODS
    abstract            text, -- resource abstract, from EAD/MODS
    extent              text, -- resource extent, from EAD and from original MARC
    href                text, -- @xlink:href, URL link to the resource
    repo_ic_id          int,  -- holding repository ic_id, fk to ic_id
                              -- has name, address, and location information of the holding institution
    role                int,  -- @xlink:role, fk to vocabulary.id, type document_type, e.g. ArchivalResource
                              -- to  be deprecated
    type                int,  -- @xlink:type, fk to vocabulary.id type source_type, always "simple"?
                              -- to be deprecated
    relation_type       text, -- @resourceRelationType, only from AnF, maybe put in a second table
    relation_entry_type text, -- relationEntry@localType, AnF, always "archival"?
                              -- to be deprecated
    object_xml_wrap     text, -- contains more information about the resource (title, extent, etc)
                              -- to be deprecated
    -- other useful information
    descriptive_note    text,
    primary key(id, version)
);


-- 
-- This is origination name aka originationName aka creator.
-- No need for field role aka roleTerm since all these names are creators. The term "origination" seems to be from EAD.
--
-- http://id.loc.gov/vocabulary/relators/cre
--
-- This table can be updated via the web UI, so it needs the id,version,ic_id. Name is simply a string and we
-- will not require names to be SNAC identities at this time. Later we will link these names to constellations
-- via field name_ic_id.
-- 
create table related_resource_origination_name (
    id         int default nextval('id_seq'),
    version    int not null,
    ic_id      int not null,                          
    name_ic_id int,                            -- ic_id of this creator, eventually filled in by humans
    name       text,                           -- name of creator of the related resource
    fk_id      int,                            -- fk to related_resource.id
    fk_table   text default 'related_resource' -- related table name
);



```
