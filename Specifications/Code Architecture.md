# Codebase Organization

We define the following PHP namespace organization, with mirroring directory structure.

```
\snac
    \client
        \webui
            \workflow
        \rest
        \testui
    \server
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
    \exceptions                     % SNAC exceptions to be thrown on error
    \interfaces                     % interfaces used throughout the codebase
```


A key note here is that the client applications (web ui and rest api) will then need to make internal server API calls over the local server API to handle any back-end requests.  This addresses a separation of concerns, keeps the server more secure, and allows for a higher level of scalability.

All endpoints (server, webui, and rest) will share the same codebase, but with an `index.php` that includes the codebase and instantiates and executes the appropriate handler class.  Then, we may have:

```
/var/www/snac-www        instantiates \snac\client\webui\WebUI
/var/www/snac-api        instantiates \snac\client\rest\RestAPI
/var/www/snac-internal   instantiates \snac\server\Server
```

### Repo Organization

```
/
    /src                % containing all snac sourcecode
        /snac           % ...
        /virtualhosts
            /www        % landing for /var/www/snac-www
            /rest       % landing for /var/www/snac-api
            /internal   % landing for /var/www/snac-internal
    /test               % containing all unit tests (mirrors /src)
        /snac           % ...
    LICENSE             % code license
    README.md           % readme file for the repo
```
