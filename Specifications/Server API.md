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

* `dataType` : the type of this object.  It should always be "Constellation" for constellations.
    * *required for ALL commands*
* `id` : snac identity number for this constellation
    * *required for the update command*
* `version` : snac version number for the state of this constellation
    * *required for the update command*
* `ark` : ark identifier for this constellation
    * *required for the update command*
* `entityType` : type of entity described, must be "person", "corporateBody", or "family"
    * *required for the update command*
* `nationality` : nationality of the entity
    * *required for the update command*
* `gender` : gender of the entity, if applicable
    * *required for the update command*
* `language` : human readable language name
    * *required for the update command*
* `languageCode` : 3-digit language code
    * *required for the update command*
* `script` : human readable script name
    * *required for the update command*
* `scriptCode` : 4-digit script code
    * *required for the update command*
* `biogHists` : *note, should be singular and store only one (nrd)*
    * *required for the update command*
* `existDates` : exist dates for this entity
    * *required for the update command*
* `generalContext` : xml-formatted text from the general context tags
    * *required for the update command*
* `structureOrGenealogy` : xml-formatted text from the structure or genealogy tags
    * *required for the update command*
* `conventionDeclaration` : xml-formatted text from the convention declaration tag
    * *required for the update command*
* `mandate` : xml-formatted text from the mandate tags
    * *required for the update command*
* `otherRecordIDs` : list of URIs that also describe this identity
* `legalStatuses` : xml-formatted text from the legal status tags
* `sources` : list of sources for this constellation
* `nameEntries` : list of name entries of this entity
* `occupations` : list of occupations for this entity
* `functions` : list of functions for this entity
* `subjects` : list of subjects for this entity
* `places` : list of places
* `relations` : list of constellation relations (within SNAC)
* `resourceRelations` : list of resource relations


*Notes: do we need language, script, and constellationLanguage/Script(Code)?  We can deduce the language and script from the codes (controlled vocab), but do we need to store the language this CPF was written in?*


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
