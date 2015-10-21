#### Name components

There is only one set of name components for a given cpf identity, thus name_component is related to table
cpf, not table name. To derive the components, we can parse the preferred name. Or using a more complex
algo, we can parse all the names in a given language and build a consensus set, or a canonical set of
components. Initially I thought that a consensus set precludes having a component "japanese family name",
but now I can't see why. There is no reason for a canonical set of components not to include components
used only in a subset of preferred name forms.

If we want language specific name components then we need to add field "language" to name_components. Due
to components being extracted from possibly several name strings, we probably will not join table name to
table name_component. It would be a many-to-one join, and given that name component relates to cpf, and not
to one or more name strings, a join between table name and table name component is not logical.

Do we need information about how each name component was derived? If so we are probably better off saving a
log of the name parser than trying to use the database as part of the historical record of name
parsing. One aspect of database-centric component derivation would be a join table to handle the
many-to-many relation between table name and table name component. Complete info about derivation also
requires the name parsing version number and any configuration at the time the parsing was done.

Suggest: we not become dogmatic about component labels. We should avoid "family name" vs surname even though
family name is perhaps more culturally relevant. Ditto givenname vs forename. Goal: be culturally agnostic
in the name_component vocabulary, and capture cultural practice in some other place/table, such as table
name_format.

Field nc_label (table name_component) must come from a controlled vocabulary in order for dynamic
formatting to work.

We should not allow our technology to be defined by the "minimal existing implementation" of name. We will
cripple SNAC if we only meet the minimal definition of name. Additionally, SNAC has needs beyond that of
our archives and authority stake holders.

#### Minimal list of components labels

surname, forename, additions, numeration, expansion

#### Larger list of components

surname, middle, forename, prefix, suffix, epithet, title, pretitle, numeration, additional

#### Overview

ISNI: prefix, surname, forename (additional forenames), middle name (second and subsequent forenames), suffix

Unimarc: $a Surname (NR), $b Given name remainder (NR), $c Additions (R), $d Roman numerals (NR), $g Expansion (NR)

MARC: $a name (NR), $b numeration (NR), $c titles and other words (R), $q fuller form (NR)


#### Detailed fields by authority

(R) are repeatable fields. (NR) are non-repeating fields.


#### ISNI

From: ISNI fields of tab delimited format for data submission, A. MacEwan et al.

http://dx.doi.org/10.1080/01639374.2012.730601

http://www.tandfonline.com/doi/abs/10.1080/01639374.2012.730601?journalCode=wccq20

ISNI components:

prefix, surname, forename and additional forenames, middle name second and subsequent forenames, suffix

- prefix: e.g. Sir
- surname: all parts of surname in the form commonly used; for alt form use alt name
- forename: one or more forenames or initials
- middle: second and subsequent forenames
- suffix: e.g. Esq
- ISNI's input format supports a single alternate name in indirect format

#### Unimarc

http://www.ifla.org/files/assets/uca/unimarc_updates/BIBLIOGRAPHIC/u-b_700_update.pdf

Unimarc components:

$a Surname (NR), $b Given name remainder (NR), $c Additions (R), $d Roman numerals (NR), $g Expansion (NR)


LoC MARC 1xx

http://www.loc.gov/marc/authority/ad100.html

MARC and Unimarc are surname oriented: "that part of the name by which the name is entered in ordered
lists", although they allow the main entry to be a forename (distinguished by indicators attribute). All
non-surname parts of the given name are thrown together into forename, both have a numeration field, both
allow many additional components. MARC $c acknowledges that there are many types of other words associated
with names.

MARC components:

$a name (NR), $b numeration (NR), $c titles and other words (R), $q fuller form (NR)

(First indicator determines direct or indirect format of $a.)

```
100 - MAIN ENTRY--PERSONAL NAME (NR)
   Indicators
      First - Type of personal name entry element
         0 - Forename
         1 - Surname
         3 - Family name
      $a - Personal name (NR)
      $b - Numeration (NR) [roman numerals]
      $c - Titles and other words associated with a name (R) [Jr., King of Sweden, Meister, pseud., Sir, (Anglo-Norman poet), Esq., II]
      $d - Dates associated with a name (NR)
      $q - Fuller form of name (NR)
```

#### ArchiveSpace

http://sandbox.archivesspace.org/

ArchiveSpace components:
prefix, title, "primary part of name (required)", rest of name, suffix, fuller form.




