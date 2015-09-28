# Elastic Search Notes and Installation

Installing Elastic Search (ES) for SNAC use requires a few stages, as outlined below.  Elastic Search uses, by default, the REST API on port 9200 for all communications.

## Background

When you need full text search for a SQL database, Elastic Search is fast and feature rich. We use it with PostgreSQL on Linux, and (will) integrate ES with Apache httpd in a LAMP web application.

## Elastic Search

The download site for elastic search is [https://www.elastic.co/downloads/elasticsearch](https://www.elastic.co/downloads/elasticsearch).  Once downloaded and unzipped, the server can be started by executing
```
bin/elasticsearch
```

The server can be tested by issuing the following command
```
curl -X GET http://localhost:9200/
```

## Elastic Search River Plugin

The River plugin allows Elastic Search to connect to a database using any standard JDBC connector.   The full source code and documentation is available at [https://github.com/jprante/elasticsearch-river-jdbc](https://github.com/jprante/elasticsearch-river-jdbc).  To install the River plugin, execute the following in the elastic search directory:
```
./bin/plugin --install jdbc --url \
  http://xbib.org/repository/org/xbib/elasticsearch/plugin/
    elasticsearch-river-jdbc/1.5.0.0/elasticsearch-river-jdbc-1.5.0.0.zip
```

Since we are using PostgreSQL, we need to install the appropriate JDBC connector.  Postgres makes this available at [https://jdbc.postgresql.org/download.html](https://jdbc.postgresql.org/download.html).  Download the jar file to `./plugins/jdbc/` in the elastic search directory.

Once the River plugin and PostgreSQL JDBC connector have been installed, restart elastic search.

## Instructions to Index Postgres

To link postgres using the river plugin, a great tutorial can be found at [http://studiofrenetic.com/blog/a-river-flowing-from-postgresql-to-elasticsearch/](http://studiofrenetic.com/blog/a-river-flowing-from-postgresql-to-elasticsearch/).

We can add an index to our database using the Postgres JDBC driver by issuing the following command:
```
curl -XPUT "localhost:9200/_river/index_type/_meta" -d ' {  
    "type" : "jdbc",
    "jdbc" : {
        "url" : "jdbc:postgresql://localhost:5432/eaccpf",
        "user" : "snac",
        "password" : "snacsnac",
        "sql" : "full SQL statement to index",
        "index" : "index_name",
        "type" : "index_type",
        "strategy" : "oneshot"
    }
}'
```
The `index_type` is what will normally be considered the index name.  The `index_name` is the super type.  To access the full index later on, a user would query `localhost:9200/index_name/index_type`.  For our uses, we have defined `index_name` as `snac` and each index to be the type associated with it.  Therefore, searching all of the snac data can be handled by:
```
curl -X GET "localhost:9200/snac/_search?pretty&q=search term"
```

### SNAC Indices

For reference, the two indices already created were defined as follows:
```
curl -XPUT "localhost:9200/_river/original_name/_meta" -d ' {  
    "type" : "jdbc",
    "jdbc" : {
        "url" : "jdbc:postgresql://localhost:5432/eaccpf",
        "user" : "snac",
        "password" : "snacsnac",
        "sql" : "select id,cpf_id,original from name;",
        "index" : "snac",
        "type" : "original_name",
        "strategy" : "oneshot"
    }
}'

curl -XPUT "localhost:9200/_river/vocabulary/_meta" -d ' {  
    "type" : "jdbc",
    "jdbc" : {
        "url" : "jdbc:postgresql://localhost:5432/eaccpf",
        "user" : "snac",
        "password" : "snacsnac",
        "sql" : "select id,type,value from vocabulary;",
        "index" : "snac",
        "type" : "vocabulary",
        "strategy" : "oneshot"
    }
}'
```

# Other References

Tutorial: [http://okfnlabs.org/blog/2013/07/01/elasticsearch-query-tutorial.html](http://okfnlabs.org/blog/2013/07/01/elasticsearch-query-tutorial.html)
