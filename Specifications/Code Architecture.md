# Codebase Organization

## Option One

Each component in its own Git repository, completely independent of others.  In this case, interaction must be through JSON API calls between components.

## Option Two

Consider the following PHP namespace organization, with mirroring directory structure.

```
\snac
    \client
        \webui
            index.php
            \workflow
        \rest
            index.php
        \testui
    \server
        index.php
        \workflow
        \identityReconciliation
        \dateParser
        \nameEntryParser
        \dataValidation
        \reporting                  % Wrapper
        \authentication             % Wrapper for OAuth
        \authorization
        \database                   % Database interaction (mainly wrappers)
        % possible wrappers for Neo4J and Elastic Search
```

In this organization, the codebase will be kept together.  The `index.php` markers are simply for readability.  Each of those files will be set up as main script for a service that the server will provide.  For example:

```
\snac\client\webui\index.php    at http://www.snaccooperative.org/
                                serving HTML, JavaScript, and JSON
\snac\client\rest\index.php     at http://api.snaccooperative.org/
                                serving JSON, file downloads
\snac\server\index.php          at http://localhost:xxxx/
                                serving JSON and internal server data
```

A key note here is that the client applications (web ui and rest api) will then need to make internal server API calls over the local server API to handle any back-end requests.  This addresses a separation of concerns and keeps the server more secure.
