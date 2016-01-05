# Server API

The internal server's API is written and required to be entirely in JSON.  Requests to the server will use HTTP PUT requests to a localhost port (set up as a firewalled virtualhost in Apache).  This allows future scalability, including separating the intenal server onto a separate machine from the front-facing web and rest interfaces.

## Server Requests

Requests can be tested by the command line using curl, in the form:
```bash
curl -XPUT "http://shannonvm.village.virginia.edu:82" -d '{ "JSON" : "Request"}'
```

Requests should be of the form:
```json
{
    "command" : "command_name",
    "constellation" : {
        "...":"..."
    }
}
```

### Request API Definitions

* `command` : the command to issue to the server.  The following commands are allowed:
    * User-level Commands
        * `login` - log in with the given credentials
        * `user_info` - get the user information for the given user
    * Constellation Management Commands
        * `edit` - Edit a constellation.  Read the constellation `"id"` from the given constellation object
        and return all pieces of the constellation object to the client for editing.  Sets the state
        of the server to note that this user is editing this constellation.
        * `insert` - Insert the given constellation in the database.  Returns the full constellation
        out of SNAC with ID and version numbers within the Constellation structure.
        * `update` - Update a constellation.  Read the constellation `"id"` from the given constellation
        object and make the requested changes to the constellation.  In an update **many** of the non-repeating portions of the constellation object are required.  Also, any subcomponent given, such as nameEntry objects, must be given in full.  Except for the outer constellation object, any subcomponents must either be given with all fields or left out completely.  **Partial components will result in data loss.**
        * `search` - Return a list of constellations matching the given constellation.  Read all the data available
        in the given constellation object and return a list of constellations that match (or are similar)
* `user` : the current user's information
* `token` : the current session token
* `constellation` : all available parts of the constellation over which to enact the command.  Certain commands require different amounts of the constellation object to be given.  The full specification for the constellation structure may be viewed [here]().
    * Example: to request a constellation, the client may use the portions of the constellation that are known (`id` or `ark_id`)

            {
                "command" : "get",
                "constellation" : {"id" : 12345}
            }


### Constellation Structure

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
    * Each name Entry must be an object (surrounded by `{}`) and have the following keys:
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
* `occupations` : List of occupations for this entity.
    * Each occupation must be an object (surrounded by `{}`) and have the following keys:
        * `dataType` : The type of this object. It should always be "Occupation" for occupations.
        * `id` : SNAC identity number for this occupation.
        * `version` : SNAC version number for the state of this occupation.
        * `term` : Controlled vocabulary term for this occupation.
        * `vocabularySource` : Source for the controlled vocabulary.
        * `dates` : Dates this person held the occupation. See the SNAC Date Structure section for date format.
        * `note` : Text note about this date.
* `functions` : List of functions for this entity.
    * Each function must be an object (surrounded by `{}`) and have the following keys:
        * `dataType` : The type of this object. It should always be "SNACFunction" for functions.
        * `id` : SNAC identity number for this function.
        * `version` : SNAC version number for the state of this function.
        * `term` : Controlled vocabulary term for this function.
        * `vocabularySource` : Source for the controlled vocabulary.
        * `type` : EAC's localType of this function.
        * `dates` : Dates this person held the occupation. See the SNAC Date Structure section for date format.
        * `note` : Text note about this function.
* `subjects` : List of subjects for this entity.
* `places` : List of places.
* `relations` : List of constellation relations (within SNAC).
    * Each constellation relation must be an object (surrounded by `{}`) and have the following keys:
        * `dataType` : The type of this object. It should always be "ConstellationRelation" for constellation relations.
        * `id` : SNAC identity number for this relation.
        * `version` : SNAC version number for the state of this relation.
        * `sourceConstellation` : SNAC identity number for the source ("from") constellation of this relation.
        * `targetConstellation` : SNAC identity number for the target ("to") constellation of this relation.
        * `sourceArkID` : Ark identifier for the source constellation.
        * `targetArkID` : Ark identifier for the target constellation.
        * `targetEntityType` : Controlled vocabulary entity type of the target constellation.
        * `type` :
        * `altType` :
        * `cpfRelationType` :
        * `content` : Text or XML content of this relation.  This should be what is listed in the EAC-CPF describing the relation.
        * `dates` : Dates these entities were in this relationship. See the SNAC Date Structure section for date format.
        * `note` : Text note about this relation.
* `resourceRelations` : List of resource relations.


*Notes: do we need language, script, and constellationLanguage/Script(Code)?  We can deduce the language and script from the codes (controlled vocab), but do we need to store the language this CPF was written in?*


### SNAC Date Structure

Snac dates

## Server Responses

The Server response API has the following form:
```json
{
    "timing" : 50,
    "request" : {
        "Note" : "Entire original request will be returned to the client."
    },
    "error" : {
        "type" : "Type of error",
        "message" : "Error message"
    },
    "constellation" : {
        "...":"..."
    }
}
```

### Response API Definitions

* `timing` : the time in milliseconds for the server to compute the response
* `request` : the client's entire request to the server, as a sub-element.  This is mainly returned for the client's reference.
* `error` : an error message, if the server had a problem handling the request.  This object has two subcomponents,
    * `type` : the type of the error.  A full list of error types are defined [here](http://shannonvm.village.virginia.edu:83).
    * `message` : a detailed error message (in English prose) stating the exact nature of the problem
* `constellation` : The matching constellation for the client's query.  All portions of the constellation known to the server will be returned to the client.  If there are multiple constellations, this will return a list:

        {
            "constellation" : [
                { "...":"..." },
                { "...":"..." },
                { "...":"..." }
            ]
        }
