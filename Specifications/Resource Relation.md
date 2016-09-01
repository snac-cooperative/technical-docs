
Question: Why are we using resource name instead of doing a join to the related constellation via ic_id? Does
related_resource_name exist for performance reasons?




Resource language is a many-to-one. Use a reverse foreign key from the multiple records in the language
table, back to the related_resoure.id.

```
language.fk_id=related_resource.id and language.fk_table='related_resource'
```

Resource name is a many-to-one. Use a reverse foreign key from related_resource_name.fk_id to related_resource.id.

```
related_resource_name.fk_id=related_resource.id and related_resource_name.fk_table='related_resource'
```

The place and address associated with a repository is handled via a place in the constellation. Address data
exists inside a place. Using the on-going oclc symbol/marc org code project, we can create a <place> with an
address in the constellation for each repository. In many cases, we will be creating a CPF stub record for the
holding repository, using the fairly extensive information provided by WorldCat and the LoC marc org code
databases.

Several new fields are added to the related_resource table:

- repo_ic_id is the ic_id of the repository (institution) holding the resource item.

- res_title is the resource title, from MARC mods/title or EAD unittitle

- res_abstract is the resource abstract, from MARC mods/abstract or EAD abstract

- res_extent is the resource extent aka size, from MARC or EAD physdesc/extent (multiple!)

Unfortunately we did not capture the MARC 300 extent, so I will have to parse the original WorldCat records for that data.


```


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
    relation_entry_type text,                  -- relationEntry@localType, AnF, always "archiva"?
    descriptive_note    text,
    object_xml_wrap     text,                  -- from objectXMLWrap, xml
    repo_ic_id          int,                   -- holding repository ic_id, fk to ic_id
    res_title           text, -- resource title, from EAD?
    res_abstract        text, -- resource abstract, from EAD
    res_extent          text, -- resource extent, from EAD
    primary key(id, version)
    );

create table related_resource_name (
    id       int default nextval('id_seq'),
    version  int not null,
    ic_id    int not null,
    name     text,
    fk_id    int,                            --  fk to related_resource.id
    fk_table text default 'related_resource' -- related table name
);

```
