# Constellation Structure

The constellation structure has the following components:

* `dataType` : The type of this object.  It should always be "Constellation" for constellations.
    * *required for ALL commands*
* `id` : SNAC identity number for this constellation. Returned after a successful `insert` command.
    * *required for the update and edit commands*
* `version` : SNAC version number for the state of this constellation.
    * *required for the update command*
* `ark` : Ark identifier for this constellation.
    * *required for the update command*
* `entityType` : Type of entity described, must be "person", "corporateBody", or "family".
    * *required for the update command*
* `nationality` : Nationality of the entity.
    * *required for the update command*
* `gender` : Gender of the entity, if applicable.
    * *required for the update command*
* `language` : Human readable language name.
    * *required for the update command*
* `languageCode` : 3-digit language code.
    * *required for the update command*
* `script` : Human readable script name.
    * *required for the update command*
* `scriptCode` : 4-digit script code.
    * *required for the update command*
* `biogHists` : *note, should be singular and store only one (nrd)*
    * *required for the update command*
* `existDates` : Exist dates for this entity.  See the SNAC Date Structure section for date format.
    * *required for the update command*
* `generalContext` : XML-formatted text from the general context tags.
    * *required for the update command*
* `structureOrGenealogy` : XML-formatted text from the structure or genealogy tags.
    * *required for the update command*
* `conventionDeclaration` : XML-formatted text from the convention declaration tag.
    * *required for the update command*
* `mandate` : XML-formatted text from the mandate tags.
    * *required for the update command*
* `otherRecordIDs` : List of URIs that also describe this identity.
* `legalStatuses` : XML-formatted text from the legal status tags.
* `sources` : List of sources for this constellation.
    * Each source must be an array (surrounded by `[]`) and have the following two keys:
        * `type` : Type of the source.
        * `href` : URI to the source.
* `nameEntries` : List of name entries of this entity.
* `occupations` : List of occupations for this entity.
* `functions` : List of functions for this entity.
* `subjects` : List of subjects for this entity.
* `places` : List of places.
* `relations` : List of constellation relations (within SNAC).
* `resourceRelations` : List of resource relations.


*Notes: do we need language, script, and constellationLanguage/Script(Code)?  We can deduce the language and script from the codes (controlled vocab), but do we need to store the language this CPF was written in?*

# Subcomponent Structures

The following are optional objects that may be included in the constellation structure above.  Each object itself, if included in one of the lists above, must be completely specified.

## Name Entries

Each name Entry must be an object (surrounded by `{}`) and have the following keys:

* `dataType` : The type of this object. It should always be "NameEntry" for name entries.
    * *required for ALL commands using a name entry*
* `id` : SNAC identity number for this name entry.
* `version` : SNAC version number for the state of this name entry.
* `original` : The original name as it was typed.
* `preferenceScore` : SNAC preference score for this name entry (0-100).
* `contributors` : List of contributors.
* `language` : Controlled vocabulary term for the language of this name entry.
* `scriptCode` : Controlled vocabulary term for this name entry's script.
* `useDates` : Use dates of this name.  See the SNAC Date Structure section for date format.

## Occupations

Each occupation must be an object (surrounded by `{}`) and have the following keys:

* `dataType` : The type of this object. It should always be "Occupation" for occupations.
* `id` : SNAC identity number for this occupation.
* `version` : SNAC version number for the state of this occupation.
* `term` : Controlled vocabulary term for this occupation.
* `vocabularySource` : Source for the controlled vocabulary.
* `dates` : Dates this person held the occupation. See the SNAC Date Structure section for date format.
* `note` : Text note about this date.

## Functions

Each function must be an object (surrounded by `{}`) and have the following keys:

* `dataType` : The type of this object. It should always be "SNACFunction" for functions.
* `id` : SNAC identity number for this function.
* `version` : SNAC version number for the state of this function.
* `term` : Controlled vocabulary term for this function.
* `vocabularySource` : Source for the controlled vocabulary.
* `type` : EAC's localType of this function.
* `dates` : Dates this entity held the occupation. See the SNAC Date Structure section for date format.
* `note` : Text note about this function.

## Places

Each place must be an object (surrounded by `{}`) and have the following keys:

* `dataType` : The type of this object. It should always be "Place" for place objects.
* `id` : SNAC identity number for this place.
* `version` : SNAC version number for the state of this place.
* `dates` : Dates this entity was associated with this place.  See the SNAC Date Structure section for date format.
* `type` : Controlled vocabulary term type of the place.
* `role` : Role of the place, from EAC-CPF's placeRole tag.
* `entries` : List of PlaceEntry objects for this place.
* `note` : Text descriptive note about this place.

## Place Entries

Each place entry must be an object (surrounded by `{}`) and have the following keys:

* `dataType` : The type of this object. It should always be "PlaceEntry" for place entry objects.
* `id` : SNAC identity number for this place entry.
* `version` : SNAC version number for the state of this place entry.
* `latitude` : Latitude of this place entry.
* `longitude` : Longitude of this place entry.
* `administrationCode` : Controlled vocabulary term of the administration code for this place entry.
* `countryCode` : Controlled vocabulary term of the country code for this place entry.
* `vocabularySource` : URI to the vocabulary source.
* `certaintyScore` : Certainty score for this place entry.  Usually used only when this place entry is a candidate for bestMatch or maybeSame for another place entry.
* `original` : The original string defining this place entry.
* `bestMatch` : A complete place entry object that is the best match for this place entry object.
* `maybeSame` : A list of place entry objects that are match candidates for this place entry object.
* `type` : Controlled vocabulary term for the type of this place entry.


## Constellation Relations

Each constellation relation must be an object (surrounded by `{}`) and have the following keys:

* `dataType` : The type of this object. It should always be "ConstellationRelation" for constellation relations.
* `id` : SNAC identity number for this relation.
* `version` : SNAC version number for the state of this relation.
* `sourceConstellation` : SNAC identity number for the source ("from") constellation of this relation.
* `targetConstellation` : SNAC identity number for the target ("to") constellation of this relation.
* `sourceArkID` : Ark identifier for the source constellation.
* `targetArkID` : Ark identifier for the target constellation.
* `targetEntityType` : Controlled vocabulary entity type of the target constellation.
* `type` : Controlled vocabulary for the type of the constellation relation, such as "associatedWith."
* `altType` : *This should always be ignored.  It is the xlink:type from the EAC-CPF cpfRelation tag.*
* `cpfRelationType` : The cpfRelation type, from the EAC-CPF cpfRelation tag's cpfRelationType attribute.
* `content` : Text or XML content of this relation.  This should be what is listed in the EAC-CPF describing the relation.
* `dates` : Dates these entities were in this relationship. See the SNAC Date Structure section for date format.
* `note` : Text note about this relation.


## Resource Relations

Each resource relation must be an object (surrounded by `{}`) and have the following keys:

* `dataType` : The type of this object. It should always be "ResourceRelation" for resource relations.
* `id` : SNAC identity number for this relation.
* `version` : SNAC version number for the state of this relation.
* `documentType` : Controlled vocabulary term for document type of this resource.
* `linkType` : *This should always be ignored.  It is the xlink:type from the EAC-CPF resourceRelation tag.*
* `entryType` : Controlled vocabulary term for the type of the resource entry, such as "archival."
* `link` : URI link to the resource.
* `role` : Controlled vocabulary term for the role this entity played in the relation, such as "authorOf."
* `content` : Text or XML content of this relation. This should be what is listed in the EAC-CPF describing the relation.
* `source` : XML source note from this resource relation.
* `note` : Text descriptive note about this relation.


## Date Structure

Each date or date range given must be an object (surrounded by `{}`) and have the following keys:

* `dataType` : The type of this object. It should always be "SNACDate" for resource relations.
* `id` : SNAC identity number for this relation.
* `version` : SNAC version number for the state of this relation.
* `isRange` : Boolean value, `true` or `false`, stating whether this date structure defines a single date or a date range.
* `fromDate` : Machine-actionable date in the form `YYYY-MM-DD`. This is either the first date in a date range or the sole date value.
* `fromDateOriginal` : Original string for the date.  This is either for the first date in a date range or the sole date value.
* `fromType` : Controlled vocabulary term for the type of date, such as "Birth." This is either for the first date in a date range or the sole date type.
* `fromBC` : Boolean value, `true` or `false`, whether the date in `fromDate` should be taken as BC/BCE (true) or AD/CE (false). 
* `fromRange` : An array of fuzzy date values for the date in `fromDate`.  It requires two fields:
    * `notBefore` : Machine-actionable date in the form `YYYY-MM-DD` for the beginning of the fuzzy range
    * `notAfter` : Machine-actionable date in the form `YYYY-MM-DD` for the end of the fuzzy range
* `toDate` : Machine-actionable date in the form `YYYY-MM-DD`. This is the second date in a date range or ignored from the structure.
* `toDateOriginal` : Original string for the date.  This is for the second date in a date range or ignored from the structure.
* `toType` : Controlled vocabulary term for the type of date, such as "Birth." This is for the second date in a date range or ignored from the structure.
* `toBC` : Boolean value, `true` or `false`, whether the date in `fromDate` should be taken as BC/BCE (true) or AD/CE (false).
* `toRange` : An array of fuzzy date values for the date in `toDate`, if it is given in the structure.  It requires two fields:
    * `notBefore` : Machine-actionable date in the form `YYYY-MM-DD` for the beginning of the fuzzy range
    * `notAfter` : Machine-actionable date in the form `YYYY-MM-DD` for the end of the fuzzy range
* `note` : Text descriptive note for this date or date range.



