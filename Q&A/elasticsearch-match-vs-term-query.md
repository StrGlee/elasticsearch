# elasticsearch match vs term query

## Answer

#### term query

* 在给定的字段里查询词或者词组；
* 提供的查询词是不分词的(not analyzed)，即只有完全包含才算匹配；
* 支持boost属性，boost可以提高field和document的相关性；

#### match query

* 与term query不同，match query的查询词是被分词处理的（analyzed），即首先分词，然后构造相应的查询，所以应该确保查询的分词器和索引的分词器是一致的；
* 与terms query相似，提供的查询词之间默认是or的关系，可以通过operator属性指定；
* match query有两种形式，一种是简单形式，一种是bool形式；

#### term用于精确查询，match用于全文检索
* The term query looks for the exact term in the field’s inverted index — it doesn’t know anything about the field’s analyzer. This makes it useful for looking up values in not_analyzed string fields, or in numeric or date fields. When querying full text fields, use the match query instead, which understands how the field has been analyzed.
* The match query will apply the same standard analyzer to the search term and will therefore match what is stored in the index. The term query does not apply any analyzers to the search term, so will only look for that exact term in the index.

## 参考
* https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl-term-query.html
* http://nkcoder.github.io/2014/03/20/elasticsearch-terms-query/
* http://nkcoder.github.io/2014/03/23/elasticsearch-match-query/
* http://elasticsearch.cn/question/445
* http://stackoverflow.com/questions/23150670/elasticsearch-match-vs-term-query
