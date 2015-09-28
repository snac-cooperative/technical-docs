Elastic search is quite powerful, and there are a few nice search features we can take advantage of:

1. Wildcard and regex queries work: http://www.elastic.co/guide/en/elasticsearch/guide/current/_wildcard_and_regexp_queries.html
2. Prefix searching for fast autocomplete: http://www.elastic.co/guide/en/elasticsearch/guide/current/_query_time_search_as_you_type.html

**Note** the full listing of query types can be found [here](http://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl-queries.html)

Indexing might help us, too, if the default inverted indices are not enough.  The inverted index allows for regex, wildcard, and even edit-distance searching.  However, other methods of indexing exist:

1. N-grams: http://www.elastic.co/guide/en/elasticsearch/guide/current/ngrams-compound-words.html

Also, it has a great scoring mechanism, as seen [here](http://www.elastic.co/guide/en/elasticsearch/guide/current/scoring-theory.html) and [here](http://www.elastic.co/guide/en/elasticsearch/guide/current/practical-scoring-function.html)