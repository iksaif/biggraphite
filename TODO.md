"""
id: ???
path: ???
name: name of the series
tags: keys + values

# find_series

Accepts a list of tag specifiers and returns a list of matching paths.
find_series(tag=value) -> ['path']


# get_series

get_series(path) -> TaggedSeries(name, tags, id)

SELECT path WHERE 
tags.latency;dc=par;env=prod

# list_tags

list_tags(filters) -> [{'tag': 'key'}, ...]

    ==
    --> SELECT value FROM tags WHERE key = 'env'
    !=
    --> SELECT value FROM tags WHERE tag < 'env'  ALLOW FILTERING;
    --> SELECT value FROM tags WHERE tag > 'env'  ALLOW FILTERING;
    ~= , !~
    --> manual / slow / unsupported

# get_tag

get_tag(key) ->  'key': [values]

    --> SELECT key, value from tags WHERE key = 'env';

# list_values

list_values(key, filter)

    --> SELECT key, value from tags WHERE key = 'env';
    --> filter values by hand

# tag_series

tag_series(series):

    ---> INSERT INTO tags (key, tag, value) VALUES ('env', 'env', 'canary');
    ---> series???

# del_series

del_series(series):

    --->
    --->

# Tables
tags - key, value - index key and value.
series -

"""

CREATE TABLE biggraphite_metadata.tags (
    key text,
    # We can't put secondaries indexes on the primary key.
    tag text,
    value text,
    created_on timeuuid,
    updated_on timeuuid,
    PRIMARY KEY(key)
) WITH CLUSTERING ORDER BY (value ASC);

CREATE CUSTOM INDEX tags_tag_idx ON biggraphite_metadata.tags (tag) USING 'org.apache.cassandra.index.sasi.SASIIndex' WITH OPTIONS = {'mode': 'CONTAINS', 'analyzer_class': 'org.apache.cassandra.index.sasi.analyzer.NonTokenizingAnalyzer', 'case_sensitive': 'true'};
# Optional
CREATE CUSTOM INDEX tags_value_idx ON biggraphite_metadata.tags (value) USING 'org.apache.cassandra.index.sasi.SASIIndex' WITH OPTIONS = {'mode': 'CONTAINS', 'analyzer_class': 'org.apache.cassandra.index.sasi.analyzer.NonTokenizingAnalyzer', 'case_sensitive': 'true'};
CREATE CUSTOM INDEX tags_updated_on_idx ON biggraphite_metadata.tags (updated_on) USING 'org.apache.cassandra.index.sasi.SASIIndex' WITH OPTIONS = {'mode': 'SPARSE'};


ALTER TABLE biggraphite_metadata.metrics_metadata ADD tags map<text, text>;




CREATE TABLE biggraphite_metadata.tags_to_series (
    tag text,
    value text,
    metric_id uuid,
    created_on timeuuid,
    updated_on timeuuid,
    PRIMARY KEY(tag, value)
) WITH CLUSTERING ORDER BY (metric_id ASC);


"""
Demo sqlite

sqlite> SELECT * FROM tags_series;
id|hash|path
1|9018f52fff2bac66e27ed6b898a310747e62f36eb77d3b4cd7f3958d32dea4e6|tags.latency;dc=par;env=prod
sqlite> SELECT * FROM tags_seriestag;
id|series_id|tag_id|value_id
1|1|1|1
2|1|2|2
3|1|3|3
sqlite> SELECT * FROM tags_tag;
id|tag
1|dc
2|env
3|name
sqlite> SELECT * FROM tags_tagvalue;
id|value
1|par
2|prod
3|tags.latency