# ElasticSearch概述

## 1、地址

狂神笔记地址

```
https://www.kuangstudy.com/bbs/1354069127022583809
```

```
https://www.elastic.co/cn/what-is/elasticsearch
```

## 2、熟悉目录

```
bin 启动文件
config 配置文件
	log4j2	日志配置文件
	jvm.options	java	虚拟机相关设置
	elasticsearch.yml	elasticsearch配置文件	默认9200的端口！跨域！
lib相关jar包
modules	功能模块
logs	日志！
plugins	插件！
```

## 3、启动！访问9200！

## 4、访问测试   		

安装可视化插件

```
1、下载地址 
git clone https://github.com/mobz/elasticsearch-head
2、启动
cd elasticsearch-head
npm install
npm run start
3、配置elasticSearch跨域
http.cors.enabled: true
http.cors.allow-origin: "*"
4、重启es服务器，然后再次连接
```

把索引当作一个数据库（可以建立索引）！文档（库中的数据！）

**这个head我们就把他当作是数据展示工具**

## 5、了解ELK

收集清洗数据-》搜索，存储-》kibana

# 安装Kibana

Kibana 是一款开源的数据分析和可视化平台，它是 Elastic Stack 成员之一，设计用于和 Elasticsearch 协作。您可以使用 Kibana 对 Elasticsearch 索引中的数据进行搜索、查看、交互操作。您可以很方便的利用图表、表格及地图对数据进行多元化的分析和呈现。

官网 https://www.elastic.co/cn/downloads/kibana

Kibana版本要和ES一致

**启动测试**

端口： localhost:5601

汉化：config中  i18n.locale: "zh-CN"

# ES核心概念

1、索引

2、字段类型（mapping）

3、文档（documents）

| Relational DBElasticSearch | ElasticSearch |
| -------------------------- | ------------- |
| 数据库                     | 索引          |
| 表                         | types         |
| 行                         | documents     |
| 字段                       | field         |

## 1、物理设计：

ElasticSearch在后台把每个索引划分成多个分片，每个分片可以在集群中的不同服务器间迁移。默认集群名就是ElasticSearch

## 2、文档

就是我们一条条数据

```
user
1 zhangsan 10
2 zjz 	3
```

- 自我包含，一篇文档同时包含字段和对应的值，同时包含key:value

# IK分词插件

> **IK分词器：中文分词器**

分词：即把一段中文或者别的划分成一个个的关键字，我们在搜索时候会把自己的信息进行分词，会把数据库中或者索引库中的数据进行分词，然后进行一一个匹配操作，**默认的中文分词是将每个字看成一个词**（<mark>不使用用IK分词器的情况下</mark>），比如“我爱狂神”会被分为”我”，”爱”，”狂”，”神” ，这显然是不符合要求的，所以我们需要安装中文分词器ik来解决这个问题。

> **IK提供了两个分词算法**: `ik_smart`和`ik_max_word` ,其中`ik_smart`为**最少切分**, `ik_max_word`为**最细粒度划分**!

>**安装**(版本要与ElasticSearch版本对应)

​		1,下载地址：https://github.com/medcl/elasticsearch-analysis-ik/releases

​		2,下载完毕之后,放入到我们的elasticSearch

>**重启观察ES**

> **4、使用 `ElasticSearch安装补录/bin/elasticsearch-plugin` 可以查看插件**

​		E:\ElasticSearch\elasticsearch-7.6.1\bin>elasticsearch-plugin list

>**使用kibana测试**

ik_smart最少切分和,ik_max_word最细粒度划分

```
GET _analyze
{
	"analyzer": "ik_smart",
	"text": "中国共产党"
}
GET _analyze
{
	"analyzer": "ik_max_word",
	"text": "中国共产党"
}
```

 发现问题：狂神说被拆开了

这种自己需要的词，需要自己加到我们的分词器的字典当中！

> ik分词器增加自己的配置 E:\ElasticSearch\elasticsearch-7.16.2\plugins\ik\configkuang.dic
>
> E:\ElasticSearch\elasticsearch-7.16.2\plugins\ik\IKAnalyzer.cfg.xml 增加自己的字典

重启ES ！

# REST风格说明

**一种软件架构风格**,而不是标准,只是提供了一组设计原则和约束条件。它主要用于客户端和服务器交互类的软件。基于这个风格设计的软件可以**更简洁**，**更有层次**，**更易于实现缓存**等机制。

## 1、**基本Rest命令说明：**

|      method      |                     url地址                     |          描述          |
| :--------------: | :---------------------------------------------: | :--------------------: |
| PUT（创建,修改） |     localhost:9200/索引名称/类型名称/文档id     | 创建文档（指定文档id） |
|   POST（创建）   |        localhost:9200/索引名称/类型名称         | 创建文档（随机文档id） |
|   POST（修改）   | localhost:9200/索引名称/类型名称/文档id/_update |        修改文档        |
|  DELETE（删除）  |     localhost:9200/索引名称/类型名称/文档id     |        删除文档        |
|   GET（查询）    |     localhost:9200/索引名称/类型名称/文档id     |   查询文档通过文档ID   |
|   POST（查询）   | localhost:9200/索引名称/类型名称/文档id/_search |      查询所有数据      |

测试

创建一个索引！

```
PUT /索引名/~类型名/文档id
{	
	请求体
}
```



```
PUT /test1/type1/1
{
  "name" : "流柚",
  "age" : 18
}
```

kibana自带的库

```
.apm-agent-configuration
.kibana_task_manager_7.16.2_001
.kibana_7.16.2_001
```

## 2、字段数据类型

- 字符串类型

  - text、

    keyword

    - text：支持分词，全文检索,支持模糊、精确查询,不支持聚合,排序操作;text类型的最大支持的字符长度无限制,适合大字段存储；
    - keyword：不进行分词，直接索引、支持模糊、支持精确匹配，支持聚合、排序操作。keyword类型的最大支持的长度为——32766个UTF-8类型的字符,可以通过设置ignore_above指定自持字符长度，超过给定长度后的数据将不被索引，无法通过term精确匹配检索返回结果。

- 数值型

  - long、Integer、short、byte、double、float、**half float**、**scaled float**

- 日期类型

  - date

- te布尔类型

  - boolean

- 二进制类型

  - binary

- 等等… 

## 3、指定字段的类型PUT

```shell
PUT /test2
{
  "mappings": {
    "properties": {
      "name": {
        "type": "text"
      },
      "age": {
        "type": "long"
      },
      "birthday": {
        "type": "date"
      }
    }
  }
}
```

## 4、获取3建立的规则

```
GET test2	# 根据get信息获取建立的规则
```

## 5、获取默认信息

`_doc` 默认类型（default type），type 在未来的版本中会逐渐弃用，因此产生一个默认类型进行代替

```
PUT /test3/_doc/1
{
  "name": "流柚",
  "age": 18,
  "birth": "1999-10-10"
}
GET test3 
```

如果自己的文档字段没有指定，那么ES就会给我们默认配置字段类型！

扩展：通过get _cat/命令elasticsearch索引情况

```
GET _cat/indices
GET _cat/aliases
GET _cat/allocation
GET _cat/count
GET _cat/fielddata
GET _cat/health			#健康状态
GET _cat/indices
GET _cat/master
GET _cat/nodeattrs
GET _cat/nodes
GET _cat/pending_tasks
GET _cat/plugins
GET _cat/recovery
GET _cat/repositories
GET _cat/segments
GET _cat/shards
GET _cat/snapshots
GET _cat/tasks
GET _cat/templates
GET _cat/thread_pool
```

## 6、修改

### 两种方案

**①旧的（使用put覆盖原来的值）**

- 版本+1（_version）
- 但是如果漏掉某个字段没有写，那么更新是没有写的字段 ，会消失

```
PUT /test3/_doc/1
{
  "name" : "流柚是我的大哥",
  "age" : 18,
  "birth" : "1999-10-10"
}
GET /test3/_doc/1
// 修改会有字段丢失
PUT /test3/_doc/1
{
  "name" : "流柚"
}
GET /test3/_doc/1
```

**②新的（使用post的update）**

- version不会改变
- 需要注意doc
- 不会丢失字段

```
POST /test3/_doc/1/_update
{
  "doc":{
    "name" : "post修改，version不会加一",
    "age" : 2
  }
}
GET /test3/_doc/1
```

## 7、删除索引

```
GET /test1
DELETE /test1
```

通过DELETE命令实现删除，根据你的请求来判断是删除索引还是删除文档！

使用RESTFUL风格，推荐！！！

# 关于文档的基本操作！（重点）

## **基本操作**

1、添加数据 PUT

3、更新数据

4、POST _update ,推荐使用这种更新方式！

简单搜索

```
GET kuangshen/user/_search?q=name:狂神说java
```

根据默认的映射规则，产生基本的查询

## 复杂搜索  select(排序、分页、高亮 、模糊查询、精准查询)

```
GET kuangshen/user/_search
{
  "query": {
    "match": {
      "name": "狂神"
    }
  },
  "_source": ["name","desc"]
}
```

我们之后使用java操作操作es，所有的方法和对象就是这里面的key!

> 排序

```
GET kuangshen/user/_search
{
  "query": {
    "match": {
      "name": "狂神"
    }
  },
  //排序
  "sort": [
      {
        "age": {
          "order": "desc"
        }
      }
    ]
}
```

>分页

```
GET kuangshen/user/_search
{
  "query": {
    "match": {
      "name": "狂神"
    }
  },
  //分页
    "from": 0,
    "size": 1
}
```

数据索引下标还是从0开始的，和学的所有数据结构是一样的！

/search/{current}/{pagesize}

>布尔值查询

must(and),所有的操作都要符合 where id = 1 and name = xxx

should(or) 所有的操作都要符合 where id = 1 or name = xxx

must_not(not) , 非操作

```
GET kuangshen/user/_search
{
  "query": {
    "bool": {
      "must": [
        {
          "match": {
           "name": "狂神说java"
          }
        }
      ],
      //对多个条件过滤
      "filter": {
        "range": {
         "age": {
           "gt": 1,
           "lt": 30
          }
        }
      }
    }
  }
}
```

>精确查询

term查询是直接通过倒排索引指定的词条进行精确查找的！

关于分词

term,直接查询精确的；分为两种情况：一个分析了一个没有被分析

```
GET testdb/_search
{
  "query": {
    "bool": {
      "should": [
        {
          "term": {
            "t1": "22"
          }
        },
        {
          "term": {
            "t1": "33"
          }
        }
      ]
    }
  }
} 精确查询多个值
```



match,会使用分词器解析！（先分析文档，然后再通过分析文档进行查询！

**两个类型text keyword** 

```
GET _analyze
{
  "analyzer": "keyword",	//keyword字段类型不会被分词器解析
  "text": "狂神说Java name"
}

GET _analyze
{
  "analyzer": "standard",
  "text": "狂神说Java name"
}
```



**高亮查询**

```
GET kuangshen/user/_search
{
  "query": {
    "match": {
      "name": "狂神"
    }
  },
  "highlight": {
   //	"pre_tags": "<p class = 'key' style='color:red'>", 自定义
   // "post_tags": "</p>",  自定义
    "fields": {
      "name": {}
    }
  }
}
```

- 匹配
- 按照条件匹配
- 精确匹配
- 区间范围匹配
- 匹配字段过滤
- 多条件查询
- 高亮查询

# 集成SpringBoot

 ```
 https://www.elastic.co/guide/en/elasticsearch/client/java-rest/current/index.html
 ```

1.找到原生的依赖

```xml
<dependency>
    <groupId>org.elasticsearch.client</groupId>
    <artifactId>elasticsearch-rest-high-level-client</artifactId>
    <version>7.15.2</version>
</dependency>
```

2.找对象

```
https://www.elastic.co/guide/en/elasticsearch/client/java-rest/current/java-rest-high-getting-started-initialization.html
```

3.分析Api的方法

配置基本的项目! **一定要保证我们导入的依赖和我们es版本一致**

源码中提供的对象

## 具体api测试

引入配置类

```java
//找对象
//放到spring中
//如果是springboot就分析源码
// AutoConfiguration xxxProperties
@Configuration
public class ElasticSearchClientConfig {
    @Bean
    public RestHighLevelClient restHighLevelClient() {
        RestHighLevelClient client = new RestHighLevelClient(
                RestClient.builder(
                        new HttpHost("127.0.0.1", 9200, "http")));
                return client;
    }
}
```

### 1.创建索引

```java
 @Test
    void testCreateIndex() throws IOException {
        // 1,创建索引请求
        CreateIndexRequest request = new CreateIndexRequest("kuang_index");
        // 2、客户端执行创建请求 IndicesClient
        CreateIndexResponse createIndexResponse = restHighLevelClient.indices().create(request, RequestOptions.DEFAULT);
        System.out.println(createIndexResponse);
    }
```

### 2.判断索引是否存在

```java
// 测试获取索引
    @Test
    void testExitIndex() throws IOException {
        GetIndexRequest request = new GetIndexRequest("kuang_index");
        boolean exists = restHighLevelClient.indices().exists(request, RequestOptions.DEFAULT);
        System.out.println(exists);
    }
```

### 3.删除索引

```java
// 测试删除索引
    @Test
    void testDeleteIndex() throws IOException {
        DeleteIndexRequest request = new DeleteIndexRequest("kuang_index");
        AcknowledgedResponse delete = restHighLevelClient.indices().delete(request, RequestOptions.DEFAULT);
        System.out.println(delete);
    }
```

### 4.创建文档

```java
// 测试添加文档
    @Test
    void testAddDocument() throws IOException {
        // 创建对象
        User user = new User("狂神说",3);
        // 创建请求
        IndexRequest request = new IndexRequest("kuang_index");
        // 规则 put /kuang_index/_doc/1
        request.id("1");
        request.timeout(TimeValue.timeValueSeconds(1));
        request.timeout("1s");
        //将我们的数据放入请求 json
        request.source(JSON.toJSONString(user), XContentType.JSON);
        // 客户端发送请求,获取响应结果
        IndexResponse indexResponse = restHighLevelClient.index(request, RequestOptions.DEFAULT);
        System.out.println(indexResponse.toString());
    }
```

### 5.crud文档

```java
 // 获取文档 ，判断是否存在 get /index/doc/1
    @Test
    void testExists() throws IOException {
        GetRequest getRequest = new GetRequest("kuang_index", "1");
        // 不获取返回的 _source 的上下文了
        getRequest.fetchSourceContext(new FetchSourceContext(false));
        boolean exists = restHighLevelClient.exists(getRequest, RequestOptions.DEFAULT);
        System.out.println(exists);
    }
```

```java
 // 获取文档信息
    @Test
    void testGetDocument() throws IOException {
        GetRequest getRequest = new GetRequest("kuang_index", "1");
        GetResponse getResponse = restHighLevelClient.get(getRequest, RequestOptions.DEFAULT);
        System.out.println(getResponse.getSourceAsString());    //打印文档的内容
        System.out.println(getResponse);    //返回额全部内容和命令是一样的
    }
```

```java
 // 更新文档的信息
    @Test
    void testUpdateDocument() throws IOException {
        UpdateRequest updateRequest = new UpdateRequest("kuang_index", "1");
        updateRequest.timeout("1s");    //超时时间
        User user = new User("狂神说Java", 18);
        updateRequest.doc(JSON.toJSONString(user),XContentType.JSON);   //json类型
        UpdateResponse update = restHighLevelClient.update(updateRequest, RequestOptions.DEFAULT);
        System.out.println(update);
    }
```

```java
// 删除文档记录
    @Test
    void testDeleteRequest() throws IOException {
        DeleteRequest request = new DeleteRequest("kuang_index", "1");
        request.timeout("1s");
        DeleteResponse delete = restHighLevelClient.delete(request, RequestOptions.DEFAULT);
        System.out.println(delete.status());
    }
```

### 6.文档批量插入

```java
// 大批量的插入
    @Test
    void testBulkRequest() throws IOException {
        BulkRequest bulkRequest = new BulkRequest();
        bulkRequest.timeout("10s");
        ArrayList<User> list = new ArrayList<>();
        list.add(new User("zjz1",3));
        list.add(new User("zjz2",4));
        list.add(new User("zjz3",5));
        list.add(new User("zjz4",6));
        list.add(new User("zjz5",7));
        list.add(new User("zjz6",8));
        for (int i =0; i < list.size(); i++ ) {
            bulkRequest.add(
                    new IndexRequest("kuang_index")
                            .id(""+(i+1))
                            .source(JSON.toJSONString(list.get(i)),XContentType.JSON));
        }
        BulkResponse bulkResponse = restHighLevelClient.bulk(bulkRequest, RequestOptions.DEFAULT);
        System.out.println(bulkResponse.hasFailures()); //是否失败，返回false是成果
    }
    // 查询
    // SearchRequest 搜索请求
    // SearchSourceBuilder 条件构造
    // HighlightBuilder 构建高亮
    // TermQueryBuilder 精确查询
    // xxxQueryBuilder 对应的功能
    @Test
    void testSearch() throws IOException {
        SearchRequest searchRequest = new SearchRequest(ESconst.ES_INDEX);
        //构建搜索条件
        SearchSourceBuilder sourceBuilder = new SearchSourceBuilder();
        //查询条件 ，我们可以使用QueryBuilders工具来实现
        //QueryBuilders.termQuery 精确
        // QueryBuilders.matchAllQuery() 匹配所有
        TermQueryBuilder termQueryBuilder = QueryBuilders.termQuery("name", "zjz1");
        sourceBuilder.query(termQueryBuilder);
        sourceBuilder.timeout(new TimeValue(60, TimeUnit.SECONDS));
		//sourceBuilder.from(1);
		//sourceBuilder.size(2);
        searchRequest.source(sourceBuilder);
        SearchResponse searchResponse = restHighLevelClient.search(searchRequest, RequestOptions.DEFAULT);
        System.out.println(JSON.toJSONString(searchResponse.getHits()));    //所有结果都封装到HITS
        System.out.println("==================");
        for (SearchHit documentFields : searchResponse.getHits().getHits()) {
            System.out.println(documentFields.getSourceAsMap());
        }
    }
```

# 实战



## 爬虫

数据问题？数据库获取，消息队列中获取，都可以成为数据源

Luence是一套信息检索工具包！jar包！不包含搜索引擎系统

包含的：索引结构！读写的索引工具



## 前后端分离

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">

<head>
    <meta charset="utf-8"/>
    <title>狂神说Java-ES仿京东实战</title>
    <link rel="stylesheet" th:href="@{/css/style.css}"/>
    <script th:src="@{/js/jquery.min.js}"></script>
<!--    <script th:src="@{/js/axios.min.js}"></script>-->
<!--    <script th:src="@{/js/vue.min.js}"></script>-->
</head>

<body class="pg">
<div class="page" id="app">
    <div id="mallPage" class=" mallist tmall- page-not-market ">

        <!-- 头部搜索 -->
        <div id="header" class=" header-list-app">
            <div class="headerLayout">
                <div class="headerCon ">
                    <!-- Logo-->
                    <h1 id="mallLogo">
                        <img th:src="@{/images/jdlogo.png}" alt="">
                    </h1>

                    <div class="header-extra">

                        <!--搜索-->
                        <div id="mallSearch" class="mall-search">
                            <form name="searchTop" class="mallSearch-form clearfix">
                                <fieldset>
                                    <legend>天猫搜索</legend>
                                    <div class="mallSearch-input clearfix">
                                        <div class="s-combobox" id="s-combobox-685">
                                            <div class="s-combobox-input-wrap">
                                                <input v-model="keyword" type="text" autocomplete="off" id="mq"
                                                       class="s-combobox-input" aria-haspopup="true">
                                            </div>
                                        </div>
                                        <button type="submit" id="searchbtn" @click.prevent="searchKey" >搜索</button>
                                    </div>
                                </fieldset>
                            </form>
                            <ul class="relKeyTop">
                                <li><a>狂神说Java</a></li>
                                <li><a>狂神说前端</a></li>
                                <li><a>狂神说Linux</a></li>
                                <li><a>狂神说大数据</a></li>
                                <li><a>狂神聊理财</a></li>
                            </ul>
                        </div>
                    </div>
                </div>
            </div>
        </div>

        <!-- 商品详情页面 -->
        <div id="content">
            <div class="main">
                <!-- 品牌分类 -->
                <form class="navAttrsForm">
                    <div class="attrs j_NavAttrs" style="display:block">
                        <div class="brandAttr j_nav_brand">
                            <div class="j_Brand attr">
                                <div class="attrKey">
                                    品牌
                                </div>
                                <div class="attrValues">
                                    <ul class="av-collapse row-2">
                                        <li><a href="#"> 狂神说 </a></li>
                                        <li><a href="#"> Java </a></li>
                                    </ul>
                                </div>
                            </div>
                        </div>
                    </div>
                </form>

                <!-- 排序规则 -->
                <div class="filter clearfix">
                    <a class="fSort fSort-cur">综合<i class="f-ico-arrow-d"></i></a>
                    <a class="fSort">人气<i class="f-ico-arrow-d"></i></a>
                    <a class="fSort">新品<i class="f-ico-arrow-d"></i></a>
                    <a class="fSort">销量<i class="f-ico-arrow-d"></i></a>
                    <a class="fSort">价格<i class="f-ico-triangle-mt"></i><i class="f-ico-triangle-mb"></i></a>
                </div>

                <!-- 商品详情 -->
                <div class="view grid-nosku">

                    <div class="product" v-for="result in results">
                        <div class="product-iWrap">
                            <!--商品封面-->
                            <div class="productImg-wrap">
                                <a class="productImg">
                                    <img src="https://img.alicdn.com/bao/uploaded/i1/3899981502/O1CN01q1uVx21MxxSZs8TVn_!!0-item_pic.jpg">
                                </a>
                            </div>
                            <!--价格-->
                            <p class="productPrice">
                                <em>{{result.price}}</em>
                            </p>
                            <!--标题-->
                            <p class="productTitle">
                                <a v-html="result.title"> </a>
                            </p>
                            <!-- 店铺名 -->
                            <div class="productShop">
                                <span>店铺： 狂神说Java </span>
                            </div>
                            <!-- 成交信息 -->
                            <p class="productStatus">
                                <span>月成交<em>999笔</em></span>
                                <span>评价 <a>3</a></span>
                            </p>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</div>
<!--前端使用vue，实现前后端分离-->
<script src="https://cdn.jsdelivr.net/npm/vue@2/dist/vue.js"></script>
<script src="https://unpkg.com/axios/dist/axios.min.js"></script>

<script>
    new Vue({
        el: '#app',
        data: {
            keyword: '',    // 搜索的关键字
            results: [] //搜索的结果
        },
        methods: {
            searchKey() {
                var keyword = this.keyword;
                axios.get('search/'+keyword+'/1/10').then(response=>{
                    this.results = response.data
                })
            }
        },
    })
</script>

</body>
</html>
```



## 搜索高亮

```java
// 实现搜索高亮功能
    public List<Map<String,Object>> searchHighLightPage(String keyword,int pageNo,int pageSize) throws IOException {
        if (pageNo < 1) {
            pageNo = 1;
        }
        // 条件搜索
        SearchRequest searchRequest = new SearchRequest("jd_goods");
        SearchSourceBuilder sourceBuilder = new SearchSourceBuilder();

        // 分页
        sourceBuilder.from(pageNo);
        sourceBuilder.size(pageSize);

        //高亮
        HighlightBuilder highlightBuilder = new HighlightBuilder();
        highlightBuilder.field("title");
        highlightBuilder.requireFieldMatch(false);  //多个高亮显示只显示一个vue
        highlightBuilder.preTags("<span style='color:red'>");
        highlightBuilder.postTags("</span>");
        sourceBuilder.highlighter(highlightBuilder);

        // 精准匹配
        TermQueryBuilder termQueryBuilder = QueryBuilders.termQuery("title", keyword);
        sourceBuilder.query(termQueryBuilder);
        sourceBuilder.timeout(new TimeValue(60, TimeUnit.SECONDS));

        // 客户端执行搜索
        searchRequest.source(sourceBuilder);
        SearchResponse searchResponse = restHighLevelClient.search(searchRequest, RequestOptions.DEFAULT);

        // 解析结果
        ArrayList<Map<String,Object>> list = new ArrayList<>();
        for (SearchHit documentFields : searchResponse.getHits().getHits()) {

            Map<String, HighlightField> highlightFields = documentFields.getHighlightFields();
            HighlightField title = highlightFields.get("title");
            Map<String, Object> sourceAsMap = documentFields.getSourceAsMap();  //原来的结果
            //解析高亮的字段
            if (title!=null){
                Text[] fragments = title.fragments();
                String n_title = "";
                for (Text text : fragments){
                    n_title += text;
                }
                sourceAsMap.put("title",n_title);   //高亮字段替换掉原来的内容即可
            }
            list.add(sourceAsMap);
        }
        return list;
    }
```



# 总结

**启动elasticSearch**  端口9200

**启动可视化界面 elasticSearch-head**  端口9100

**启动kibana**    端口5601



















