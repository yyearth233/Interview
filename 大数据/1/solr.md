# Solr

#### 1. 介绍
Solr 是 Apache 基金会下的一个开源全文搜索平台。它基于 Lucene，采用反向索引，从关键词映射到文档。

以 document 对象进行存储，一个 document 包含多个 field，每个 field 表示一个属性。通过对 field 建立索引，查询到 document。

每个 document 都包含以一个能唯一表示该文档的 id field。

#### 2. 倒排索引

倒排索引是从文档内容到文档的映射。通过 value 找 key。
首先通过分词器对文档内容进行分词。然后建立索引链。

#### 3. 为什么使用 solr
全文搜索，搜索引擎一样。

#### 4. solr 与 elasticsearch 的区别

相同点：都是基于 lucene 实现的。

不同点：
1) solr 利用 zookeeper 进行分布式管理，elasticsearch 使用自带的分布式组件。
2) solr 更加功能全面，官方功能多，elasticsearch 注重核心功能，高级功能需要第三方开发。
3) solr 在传统搜索中表现较好，elasticsearch 在实时搜索中表现较好。

传统搜索：数据是静态的，从静态数据中搜索符合条件的结果。solr 更新索引时阻塞所以实时性能差，但搜静态数据性能好与 elasticsearch。
实时搜索：搜索结果是变化的。elasticsearch 可以实时建立索引。
