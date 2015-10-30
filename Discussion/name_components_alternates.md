
#### Name and alternate name

There is no consensus on the canonical name. There probably can be no single, always preferred name. What is
preferred depends on context, and will vary for different purposes. In the data we can capture a reasonable
amount of context, but only the users know what is preferred. Computationally, we should treat all name as
alternates. We can offer names in one (or more) of several agreed-upon formats, leaving the choice up to the
user.

Name and alternate has no effect on identity matching because the match is done on all alternates, and uses
all available data from the identity constellation. 

#### Name components

Give the variety of components in names, it is not possible to create a canonical, single set of components
for many names and their alternates. Even the concept of "preferred" name is debatable.

It is (mostly) possible to parse out the components for each name and each alternate name. Thus we have as many
sets of name components as we have names for a single identity constellation. 

Suggest: we not become dogmatic about component labels. We should avoid "family name" vs surname even though
family name is perhaps more culturally relevant. Ditto givenname vs forename. Goal: be culturally agnostic
in the name_component vocabulary, and capture cultural practice in some other place/table, such as table
name_format. 

If we want language specific name components then we need to add field "language" to name_components. Due
to components being extracted from possibly several name strings, we probably will not join table name to
table name_component. It would be a many-to-one join, and given that name component relates to cpf, and not
to one or more name strings, a join between table name and table name component is not logical.

Do we need information about how each name component was derived? If so we are probably better off saving a
log of the name parser than trying to use the database as part of the historical record of name
parsing. One aspect of database-centric component derivation would be a join table to handle the
many-to-many relation between table name and table name component. Complete info about derivation also
requires the name parsing version number and any configuration at the time the parsing was done.

Field nc_label (table name_component) must come from a controlled vocabulary in order for dynamic
formatting to work.

We should not allow our technology to be defined by the "minimal existing implementation" of name. We will
cripple SNAC if we only meet the minimal definition of name. Additionally, SNAC has needs beyond that of
our archives and authority stake holders.

#### Minimal list of components labels

The least flexible system (of the 3 or 4 we have reviewed) for name components is probably MARC. Even in MARC
the $c is repeatable, allowing for large number of (unlabeled) components. This system is probably too
restrictive, although it allows us to capture middle name when possible. However, lack of flexible labels
makes MARC a weak standard for names.

surname, forename, additions, numeration, expansion

#### Larger list of components

This list comes from a combination of MARC, Unimarc, ISNI, ArchiveSpace, and British Library. We might be wise
to include others as well (VIAF, BnF, AnF, Archives Hub UK).

surname, middle, forename, prefix, suffix, epithet, title, pretitle, numeration, additional

Unfortunately, current name format guidelines are ambiguous. The most common problem is that middle name could
be a second forename or the second of several additional name components. 

There is general agreement on "name" and "non-name" parts, although no guides explicitly talk about this. Name
parts are surname, forename, and middle name. Many other non-name parts are often found in names. Both the
name and non-name parts have ambiguous rules within most systems, and the various systems and cultures have
incomplete agreement. 

The database is improved by labeling the compoents where possible, but our algorithms and user
interface can (fairly) easily remaing agnostic about compoent labels while processing anddisplaying the
compoents as well as formatting the components into names.

In the past, failure to create names by (re-) formatting components has led to inconsistent names. While is it
not possible to 100% parse or format names, it is also true that humans who did the data entry were not 100%
accurate in their formats. The computer can be more consistent, and probably nearly as accurate as the human
editors (especially where the editors cannot agree or where they have ambiguous rules). While it is
technically feasible to format names from components, it is also feasible to keep and (carefully) display the
names as originally entered.

#### Overview

ISNI: prefix (NR), surname (NR), forename (additional forenames) (R-ish), middle name (second and subsequent
forenames) (R-ish), suffix (NR)

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




