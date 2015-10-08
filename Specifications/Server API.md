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

* `command` : the request command, such as `search`.  A full list of available options is available [here]()
* `constellation` : all available parts of the constellation to enact the command over.  If the interaction does not utilize a constellation, this may be omitted.  The full specification for the constellation structure may be viewed [here]().
    * Example: to request a constellation, the client may use the portions of the constellation that are known (`id` or `ark_id`)
            ```json
            {
                "command" : "get",
                "constellation" : {"id" : 12345}
            }
            ```


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
        ```json
        {
            "constellation" : [
                { "...":"..." },
                { "...":"..." },
                { "...":"..." }
            ]
        }
        ```
