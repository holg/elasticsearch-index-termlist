Elasticsearch Index Termlist Plugin
-----------------------------------

This plugin extends Elasticsearch with a term list capability. Term lists can be generated from indexes, or even of all of the indexes in the cluster.

Installation
------------

In order to install the plugin, simply run: `bin/plugin -install holg/elasticsearch-termlist/1.2.0`.

    ---------------------------------------
    | Termlist Plugin | ElasticSearch     |
    ---------------------------------------
    | 1.2.0            | 0.20             |
    ---------------------------------------
    | 1.1.0            | 0.19             |
    ---------------------------------------

Introduction
------------

Getting the list of all terms indexed is useful for variuos purposes, for example

- building dictionaries
- controlling the overall effects of analyzers on the indexed terms
- automatic query building on indexed terms, e.g. for load tests
- input to linguistic analysis tools
- for other post-processing of the indexed terms outside of Elasticsearch

Example of getting term lists

Consider the following example index

	curl -XPUT 'http://localhost:9200/test/'
	curl -XPUT 'http://localhost:9200/test/test/1' -d '{ "test": "Hello World" }'
	curl -XPUT 'http://localhost:9200/test/test/2' -d '{ "test": "Hello Jörg Prante" }'
	curl -XPUT 'http://localhost:9200/test/test/3' -d '{ "message": "elastic search" }'

Get term list of index `test`

	curl -XGET 'http://localhost:9200/test/_termlist'
	{"ok":true,"_shards":{"total":5,"successful":5,"failed":0},"terms":["hello","prant","world","elastic","search","jorg"]}

Get term list of index `test` of field `message`

	curl -XGET 'http://localhost:9200/test/_termlist/message'
	{"ok":true,"_shards":{"total":5,"successful":5,"failed":0},"terms":["elastic","search"]}

Optionally, the term list can be narrowed down to a field name. The field name is the Lucene field name as found in the Lucene index.

Only terms of field names not starting with underscore are listed. Terms of internal fields like `_uid`, `_all`, or `_type` are always skipped.

If you want a sorted term list, you have to sort the obtained list at client side.

WARNING
-------
The term list is built internally into an unsorted, compact set of strings which is not streamed to the client. You should be aware that if you have lots of unique terms in the index, this procedure consumes a lot of heap memory and may result in out of memory situations that can render your Elasticsearch cluster unusable until it is restarted.

