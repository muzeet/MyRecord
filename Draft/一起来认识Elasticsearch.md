### 一起来认识Elasticsearch

#### 1.介绍

#### 2.安装

下载最新的es版本elasticsearch-7.6.2-linux-x86_64.tar.gz，解压到elasticsearch目录

执行如下命令启动es

```
./bin/elasticsearch
./bin/elasticsearch -d // 以守护进程运行
```

Elasticsearch在前台运行，可以使用用`Ctrl-C`快捷键来关闭elasticsearch，或者你可以调shutdown API来关闭

```
curl -XPOST 'http://localhost:9200/_shutdown'
```

执行命令后发现有报错

```
ERROR: [3] bootstrap checks failed
[1]: max file descriptors [4096] for elasticsearch process is too low, increase to at least [65535]
[2]: max virtual memory areas vm.max_map_count [65530] is too low, increase to at least [262144]
[3]: the default discovery settings are unsuitable for production use; at least one of [discovery.seed_hosts, discovery.seed_providers, cluster.initial_master_nodes] must be configured
ERROR: Elasticsearch did not exit normally - check the logs at /home/mindspore/software/elasticsearch/elasticsearch-7.6.2/logs/elasticsearch.log
```

根据提示信息，可以发现是本机，`fd` 虚拟内存等配置问题，解决办法如下:

1. max file descriptors [4096] for elasticsearch process is too low, increase to at least [65535]

   每个进程最大打开的文件句柄个数，可以通过命令查看当前设置情况

   ```
   ulimit -Hn // 最大个数
   ulimit -Sn // 最小个数
   ```

   修改`/etc/security/limits.conf`文件可以配置最大最下文件句柄个数，修改后用户重新登录后生效。增加如下2行配置

   ```
   * soft nofile 65536
   * hard nofile 65536
   ```

2. max virtual memory areas vm.max_map_count [65530] is too low, increase to at least [262144]

   修改/etc/sysctl.conf文件，增加配置vm.max_map_count=262144

   ```
   vi /etc/sysctl.conf
   sysctl -p
   ```

3. the default discovery settings are unsuitable for production use; at least one of [discovery.seed_hosts, discovery.seed_providers, cluster.initial_master_nodes] must be configured

   修改配置文件`elasticsearch.yml`，修改如下配置

   ```
   vim config/elasticsearch.yml
   node.name: node-1 前面的#打开
   network.host: 0.0.0.0 // 任何ip都可以访问
   cluster.initial_master_nodes: ["node-1"] // 为node.name对应值
   // 如下这2项设置，可使外表浏览器顺利访问
   http.cors.enabled: true
   http.cors.allow-origin: "*"
   ```

   

   https://www.cnblogs.com/zhi-leaf/p/8484337.html

   

#### 3.认识ES

向Elasticsearch发出的请求的组成部分与其它普通的HTTP请求是一样的

```
curl -H -X<VERB> '<PROTOCOL>://<HOST>:<PORT>/<PATH>?<QUERY_STRING>' -d '<BODY>'
```

VERB HTTP方法:`GET`,`POST`,`PUT`,`HEAD`,`DELETE`
PROTOCOL http或者https协议(只有在Elasticsearch前面有https代理的时候可用)
HOST Elasticsearch集群中的任何一个节点的主机名,如果是在本地的节点,那么就叫localhost
PORT Elasticsearch HTTP服务所在的端口,默认为9200
PATH API路径(例如_count将返回集群中文档的数量),PATH可以包含多个组件,例如_cluster/stats或者_nodes/stats/jvm
QUERY_STRING 一些可选的查询请求参数,例如?pretty
参数将使请求返回更加美观
易读的JSON数据
BODY 一个JSON格式的请求主体(如果请求需要的话)

查询计算集群中的文档数量：

```
curl -XGET 'http://localhost:9200/_count?pretty' -d '
{
"query": {
"match_all": {}
}
}
'
```

直接使用上面命令查询会报如下错误，查询得知现在ES需要制定Header。

```
{
  "error" : "Content-Type header [application/x-www-form-urlencoded] is not supported",
  "status" : 406
}

```

在curl命令前加上`-H "Content-Type: application/json"`

```
curl -H "Content-Type: application/json" -XGET 'http://localhost:9200/_count?pretty' -d '
{
"query": {
"match_all": {}
}
}
'
```

返回结果：

```
{
  "count" : 0,
  "_shards" : {
    "total" : 0,
    "successful" : 0,
    "skipped" : 0,
    "failed" : 0
  }
}
```

```
curl -H "Content-Type: application/json" -i -XGET 'localhost:9200/_count?pretty' -d '
{
"query": {
"match_all": {}
}
}
'
```

```
HTTP/1.1 200 OK
content-type: application/json; charset=UTF-8
content-length: 114

{
  "count" : 0,
  "_shards" : {
    "total" : 0,
    "successful" : 0,
    "skipped" : 0,
    "failed" : 0
  }
}
```

类比传统关系型数据库

```
Relational DB -> Databases -> Tables -> Rows -> Columns
Elasticsearch -> Indices -> Types -> Documents -> Fields
```

Elasticsearch集群可以包含多个索引(indices)(数据库),每一个索引可以包含多个类型(types)(表),每一个类型包含多个文档(documents)(行),然后每个文档包含多个字段(Fields)(列)。

```
curl -H "Content-Type: application/json" -i -XPUT 'localhost:9200/megacorp/employee/1' -d '
{
"first_name" : "John",
"last_name" : "Smith",
"age" : 25,
"about" : "I love to go rock climbing",
"interests": [ "sports", "music" ]
}
'

HTTP/1.1 201 Created
Location: /megacorp/employee/1
Warning: 299 Elasticsearch-7.6.2-ef48eb35cf30adf4db14086e8aabd07ef6fb113f "[types removal] Specifying types in document index requests is deprecated, use the typeless endpoints instead (/{index}/_doc/{id}, /{index}/_doc, or /{index}/_create/{id})."
content-type: application/json; charset=UTF-8
content-length: 160

{"_index":"megacorp","_type":"employee","_id":"1","_version":1,"result":"created","_shards":{"total":2,"successful":1,"failed":0},"_seq_no":0,"_primary_term":1}
```



| 名字     | 说明         |
| :------- | ------------ |
| megacorp | 索引名       |
| employee | 类型名       |
| 1        | 这个员工的ID |



```
curl -H "Content-Type: application/json" -i -XGET 'localhost:9200/megacorp/employee/1'
```

```
GET操作新在换成了"[types removal] Specifying types in document get requests is deprecated, use the /{index}/_doc/{id} endpoint instead."

curl -H "Content-Type: application/json" -i -XGET 'localhost:9200/megacrp/_doc/1'
HTTP/1.1 200 OK
content-type: application/json; charset=UTF-8
content-length: 251

{"_index":"megacorp","_type":"_doc","_id":"1","_version":1,"_seq_no":0,"_primary_term":1,"found":true,"_source":
{
"first_name" : "John",
"last_name" : "Smith",
"age" : 25,
"about" : "I love to go rock climbing",
"interests": [ "sports", "music" ]
}

```



```
curl -H "Content-Type: application/json" -i -XGET 'localhost:9200/_search' 
HTTP/1.1 200 OK
content-type: application/json; charset=UTF-8
content-length: 782

{"took":26,"timed_out":false,"_shards":{"total":1,"successful":1,"skipped":0,"failed":0},"hits":{"total":{"value":3,"relation":"eq"},"max_score":1.0,"hits":[{"_index":"megacorp","_type":"employee","_id":"1","_score":1.0,"_source":
{
"first_name" : "John",
"last_name" : "Smith",
"age" : 25,
"about" : "I love to go rock climbing",
"interests": [ "sports", "music" ]
}
},{"_index":"megacorp","_type":"employee","_id":"2","_score":1.0,"_source":
{
"first_name" : "Jane",
"last_name" : "Smith",
"age" : 32,
"about" : "I like to collect rock albums",
"interests":
[ "music" ]
}
},{"_index":"megacorp","_type":"employee","_id":"3","_score":1.0,"_source":
{
"first_name" : "Douglas",
"last_name" : "Fir",
"age" : 35,
"about": "I like to build cabinets",
"interests":
[ "forestry" ]
}
}]}}
```

用_search来取代doc_id, 响应内容默认返回前10个内容



搜索姓氏中包含“Smith”的员工，采用查询字符串(query string)搜索， `_search?q=key:value`

```
curl -H "Content-Type: application/json" -i -XGET 'localhost:9200/_search?q=last_name:smith' 
HTTP/1.1 200 OK
content-type: application/json; charset=UTF-8
content-length: 596

{"took":22,"timed_out":false,"_shards":{"total":1,"successful":1,"skipped":0,"failed":0},"hits":{"total":{"value":2,"relation":"eq"},"max_score":0.4700036,"hits":[{"_index":"megacorp","_type":"employee","_id":"1","_score":0.4700036,"_source":
{
"first_name" : "John",
"last_name" : "Smith",
"age" : 25,
"about" : "I love to go rock climbing",
"interests": [ "sports", "music" ]
}
},{"_index":"megacorp","_type":"employee","_id":"2","_score":0.4700036,"_source":
{
"first_name" : "Jane",
"last_name" : "Smith",
"age" : 32,
"about" : "I like to collect rock albums",
"interests":
[ "music" ]
}
}]}}
```



DSL(Domain Specific Language特定领域语言)查询（Query DSL）以JSON请求体的形式出现

```
curl -H "Content-Type: application/json" -i -XGET 'localhost:9200/_search' -d '
{
"query" : {
"match" : {
"last_name" : "Smith"
}
}
}
'

HTTP/1.1 200 OK
content-type: application/json; charset=UTF-8
content-length: 595

{"took":1,"timed_out":false,"_shards":{"total":1,"successful":1,"skipped":0,"failed":0},"hits":{"total":{"value":2,"relation":"eq"},"max_score":0.4700036,"hits":[{"_index":"megacorp","_type":"employee","_id":"1","_score":0.4700036,"_source":
{
"first_name" : "John",
"last_name" : "Smith",
"age" : 25,
"about" : "I love to go rock climbing",
"interests": [ "sports", "music" ]
}
},{"_index":"megacorp","_type":"employee","_id":"2","_score":0.4700036,"_source":
{
"first_name" : "Jane",
"last_name" : "Smith",
"age" : 32,
"about" : "I like to collect rock albums",
"interests":
[ "music" ]
}
}]}}
```



全文检索所有about和'rock climbing'相关的文档

```
curl -H "Content-Type: application/json" -i -XGET 'localhost:9200/_search
{
 "query" : {
 "match" : {
 "about" : "rock climbing"
 }
 }
 }
'
HTTP/1.1 200 OK
content-type: application/json; charset=UTF-8
content-length: 596

{"took":16,"timed_out":false,"_shards":{"total":1,"successful":1,"skipped":0,"failed":0},"hits":{"total":{"value":2,"relation":"eq"},"max_score":1.4167401,"hits":[{"_index":"megacorp","_type":"employee","_id":"1","_score":1.4167401,"_source":
{
"first_name" : "John",
"last_name" : "Smith",
"age" : 25,
"about" : "I love to go rock climbing",
"interests": [ "sports", "music" ]
}
},{"_index":"megacorp","_type":"employee","_id":"2","_score":0.4589591,"_source":
{
"first_name" : "Jane",
"last_name" : "Smith",
"age" : 32,
"about" : "I like to collect rock albums",
"interests":
[ "music" ]
}
}]}}
```



高亮搜索结果，highlight

```
curl -H "Content-Type: application/json" -i -XGET 'localhost:9200/_search' -d '
{
"query" : {
"match" : {
"about" : "rock climbing"
}
},
"highlight": {
"fields" : {
"about" : {}
}
}
}
'

HTTP/1.1 200 OK
content-type: application/json; charset=UTF-8
content-length: 732

{"took":40,"timed_out":false,"_shards":{"total":1,"successful":1,"skipped":0,"failed":0},"hits":{"total":{"value":2,"relation":"eq"},"max_score":1.4167401,"hits":[{"_index":"megacorp","_type":"employee","_id":"1","_score":1.4167401,"_source":
{
"first_name" : "John",
"last_name" : "Smith",
"age" : 25,
"about" : "I love to go rock climbing",
"interests": [ "sports", "music" ]
}
,"highlight":{"about":["I love to go <em>rock</em> <em>climbing</em>"]}},{"_index":"megacorp","_type":"employee","_id":"2","_score":0.4589591,"_source":
{
"first_name" : "Jane",
"last_name" : "Smith",
"age" : 32,
"about" : "I like to collect rock albums",
"interests":
[ "music" ]
}
,"highlight":{"about":["I like to collect <em>rock</em> albums"]}}]}}
```



聚合(aggregations)允许在数据上生成复杂的分析统计。它很像SQL中的GROUP BY但是功能更强大--执行失败

```
curl -H "Content-Type: application/json" -i -XGET 'localhost:9200/_search' -d '
{
"aggs": {
"all_interests": {
"terms": { "field": "interests" }
}
}
}
'
```

