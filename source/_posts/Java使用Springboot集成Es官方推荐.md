---
title: SpringBoot集成ElasticSearch
date: 2021-09-29 22:12:12
tags: ["SpringBoot", "ElasticSearch", "RestHighLevelClient"]
cover: https://z3.ax1x.com/2021/09/26/4cr9L4.jpg
---

### SpringBoot集成ElasticSearch的四种方式(主要讲解ES官方推荐方式)

---

- TransportClient：这种方式即将弃用、官方将在8.0版本彻底去掉这个api
- Data-Es：Spring提供的封装的方式(Springboot-data)，由于是Spring提供的，所以每个SpringBoot版本对应的ElasticSearch，具体这么个对应的版本，[自己去官网看](https://spring.io/projects/spring-data-elasticsearch#learn)
- ElasticSearch SQL：将Elasticsearch的`Query DSL`用`SQL`转换查询，早期有一个第三方的插件Elasticsearch-SQL，后来随着官方也开始做这方面，这个插件好像就没怎么更新了，有兴趣的可以查看[https://www.cnblogs.com/jajian/p/10053504.html](https://links.jianshu.com/go?to=https%3A%2F%2Fwww.cnblogs.com%2Fjajian%2Fp%2F10053504.html)
- Rest Client：官方推荐使用，所以我们采用这个方式，这个分为两个Low Level REST Client和High Level REST Client，Low Level REST Client是早期出的API比较简陋了，还需要自己去拼写`Query DSL`，High Level REST Client使用起来更好用，更符合面向对象的感觉，我们下面使用High Rest Client

**注意：我使用的是ES7.6.1版本，等会pom文件依赖时，版本必须与之对应**

#### 1.创建Maven项目

#### 2.导入pom依赖

```xml
<dependencies>
    <!--SpringBoot集成Es的包-->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-elasticsearch</artifactId>
    </dependency>
    <!--springbootweb包-->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <!--lombok插件-->
    <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
        <optional>true</optional>
    </dependency>
    <!--test测试包-->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
        <scope>test</scope>
    </dependency>
    <!--json序列化包-->
    <dependency>
        <groupId>com.alibaba</groupId>
        <artifactId>fastjson</artifactId>
        <version>1.1.15</version>
    </dependency>
</dependencies>
```

**pom导包完成后、需要点开Maven查看引入的elasticsearch-rest-high-level-client的版本是否与你的ElasticSearch版本一致**

![在这里插入图片描述](https://img-blog.csdnimg.cn/ed778935c688458fa43b4c0ae7ade46f.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA55Wq6IyE5ZGm5ZGm,size_20,color_FFFFFF,t_70,g_se,x_16)

如果不一致需要自己去控制版本（如下代码）

```xml
<properties>
    <java.version>1.8</java.version>
    <!--这里是你的es的版本-->
    <elasticsearch.version>7.6.1</elasticsearch.version>
</properties>
```

#### 3.SpringBoot项目的启动类编写（忽略）

#### 4.编写配置类（RestHighLevelClient）

```java
// 表示这是一个配置类
@Configuration
public class RestElasticSearchClientConfig {
    // 将方法的返回结果交给spring管理
    @Bean
    public RestHighLevelClient restHighLevelClient(){
        // 主机ip和端口号以及协议
        RestHighLevelClient restHighLevelClient = new RestHighLevelClient(RestClient.builder(
                new HttpHost("localhost", 9200, "http")));
        return restHighLevelClient;
    }
}
```

到这里我们RestHighLevelClient的配置就做完了

#### 5.测试（使用RestHighLevelClient的Api）

编写测试类，在类中自动注入RestHighLevelClient对象

![在这里插入图片描述](https://img-blog.csdnimg.cn/31c1315989814903bf65cdc0bc5c9f3b.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA55Wq6IyE5ZGm5ZGm,size_20,color_FFFFFF,t_70,g_se,x_16)

#### 6.RestHighLevelClient的Api使用(关于索引)

##### 6.1 简单索引的创建

```java
// 测试索引的创建（不带mapping，ElasticSearch默认会根据你的添加的文档来创建mapping）
@Test
void testCreateIndex() throws IOException {
    // 创建索引的请求
    CreateIndexRequest nan_index = new CreateIndexRequest("nan_index");
    // client执行请求
    CreateIndexResponse response = restHighLevelClient.indices().create(nan_index, RequestOptions.DEFAULT);
    System.out.println(response);
}
```

##### 6.2自定义mapping创建索引

```java
// 带上自定义的mapping来创建索引
@Test
void testCreateMappingIndex() throws IOException {
    // 创建索引的请求
    CreateIndexRequest indexRequest = new CreateIndexRequest("nanmapping_index");
    // mapping规则去别的地方写好之后，复制粘贴过来，IDEA会自动转义相关符号
    String mapping = "{\n" +
            "    \"properties\": {\n" +
            "      \"name\": {\n" +
            "        \"type\": \"keyword\"\n" +
            "      },\n" +
            "      \"age\": {\n" +
            "        \"type\": \"integer\"\n" +
            "      }\n" +
            "    }\n" +
            "  }";
    // 添加索引的mapping规则
    indexRequest.mapping(mapping,XContentType.JSON);
    // 发送请求
    CreateIndexResponse response = restHighLevelClient.indices().create(indexRequest, RequestOptions.DEFAULT);
    System.out.println(response);
}
```

##### 6.3测试索引是否存在

```java
// 测试索引是否存在
@Test
void testExitIndex() throws IOException {
    // 获取索引的请求
    GetIndexRequest nan_index = new GetIndexRequest("nan_index");
    // 执行请求
    boolean exists = restHighLevelClient.indices().exists(nan_index, RequestOptions.DEFAULT);
    System.out.println(exists);
}
```

##### 6.4索引的删除

```java
// 测试删除索引
@Test
void testDeleteIndex() throws IOException {
    // 删除索引的请求
    DeleteIndexRequest nan_index = new DeleteIndexRequest("nan_index");
    // 执行删除的请求
    AcknowledgedResponse response = restHighLevelClient.indices().delete(nan_index, RequestOptions.DEFAULT);
    System.out.println(response);
}
```

#### 7.RestHighLevelClient的Api使用(关于文档数据）

##### 7.1文档的添加

```java
// 测试文档的添加
@Test
public void testCreateDoc() throws IOException {
    // 准备好数据
    User user = new User("小杰", 23, "你要是这么想我也没办法", new String[]{"md", "渣男"});
    // 创建好index请求
    IndexRequest indexRequest = new IndexRequest("nan_index");
    // 设置索引
    indexRequest.id("1");
    // 设置超时时间（默认）
    indexRequest.timeout(TimeValue.timeValueSeconds(5));
    // 往请求中添加数据
    indexRequest.source(JSON.toJSONString(user), XContentType.JSON);
    //执行添加请求
    IndexResponse indexResponse = restHighLevelClient.index(indexRequest, RequestOptions.DEFAULT);
    System.out.println(indexResponse);
}
```

##### 7.2文档的修改（更新）（全局更新）

```java
// 测试文档的更新(id存在就是更新,id不存在就是添加)(用下面这个方法更新时，是全局更新，就是说里面的字段全部被覆盖
// 新对象里没有的字段就没有了)
@Test
public void testUpdateDoc() throws IOException {
    User user = new User("xiaoer", 18);
    IndexRequest indexRequest = new IndexRequest("nan_index");
    indexRequest.id("2");
    indexRequest.source(JSON.toJSONString(user),XContentType.JSON);
    IndexResponse indexResponse = restHighLevelClient.index(indexRequest, RequestOptions.DEFAULT);
    System.out.println(indexResponse);

}
```

##### 7.3文档数据的更新（局部更新）（推荐）

```java
// 测试文档的更新(id存在就是更新,id不存在就是添加)
// (用下面这个方法更新时，是局部更新，就是说只会覆盖其中有的字段)
@Test
public void testUpdateBetterDoc() throws IOException {
    //准备好修改的数据
    User user = new User("xiaoer", 18);
    // 创建更新请求
    UpdateRequest updateRequest = new UpdateRequest("nan_index", "1");
    // 把要更新的数据装进去
    updateRequest.doc(JSON.toJSONString(user),XContentType.JSON);
    // 执行更新语句
    UpdateResponse updateResponse = restHighLevelClient.update(updateRequest, RequestOptions.DEFAULT);
    System.out.println(updateResponse);
}
```

##### 7.4文档数据的删除

```java
// 测试文档删除
@Test
public void testDelDoc() throws IOException {
    DeleteRequest deleteRequest = new DeleteRequest("nan_index","2");
    DeleteResponse deleteResponse = restHighLevelClient.delete(deleteRequest, RequestOptions.DEFAULT);
    System.out.println(deleteResponse);
}
```

##### 7.5根据id获取文档数据(简单)

```java
//获取简单文档数据
@Test
public void testGetDoc() throws IOException {
    GetRequest getRequest = new GetRequest("nan_index");
    getRequest.id("1");
    GetResponse getResponse = restHighLevelClient.get(getRequest, RequestOptions.DEFAULT);
    System.out.println(getResponse);
}
```

##### 7.6批量添加文档数据

```java
// 测试文档批量添加（添加会了，批量删除、更新、修改是一样的）
@Test
public void testBulkAdd() throws IOException {
    // 准备要添加的数据
    List<User> users = new ArrayList<>();
    users.add(new User("xiaofei",25,"每天都是正能量",new String[]{"加油","早起晚睡"}));
    users.add(new User("xiaohua",22,"xiaohua",new String[]{"html","早起晚睡"}));
    users.add(new User("xiaoer",23,"我是菜鸟",new String[]{"宅男","早起晚睡"}));
    users.add(new User("xiaoge",22,"一看工资2500",new String[]{"加油","渣男"}));
    users.add(new User("xiaomei",23,"给我钱就行",new String[]{"hh","早起晚睡"}));
    // 创建批量请求
    BulkRequest bulkRequest = new BulkRequest();
    // 利用循环将每一个add请求添加到bulkRequest请求中
    for (int i = 0; i < users.size(); i++) {
        IndexRequest indexRequest = new IndexRequest("nan_index").id(""+i);
        indexRequest.source(JSON.toJSONString(users.get(i)),XContentType.JSON);
        bulkRequest.add(indexRequest);
    }
    // 执行批量请求
    BulkResponse bulkResponse = restHighLevelClient.bulk(bulkRequest, RequestOptions.DEFAULT);
    System.out.println(bulkResponse);
}
```

##### 7.7文档数据的查询（带条件）重点(后面的都是重点)

```java
// 测试文档列表的查询（带条件）
// 这是ElasticSearch最重要的地方
@Test
public void testGetListDoc() throws IOException {
    SearchRequest searchRequest = new SearchRequest("nan_index");
    // 构建搜索builder
    SearchSourceBuilder searchSourceBuilder = new SearchSourceBuilder();

    // 构建查询条件(查询所有)
    //  MatchAllQueryBuilder matchAllQueryBuilder = QueryBuilders.matchAllQuery();

    // 构建查询条件(精确匹配)
    TermsQueryBuilder termsQueryBuilder = QueryBuilders.termsQuery("name", "xiaomei");

    // 把查询条件设置给搜索builder
    searchSourceBuilder.query(termsQueryBuilder);

    // 设置分页查询(跟sql语句的limit一样)
    searchSourceBuilder.from(0); // 开始下标(当前页码-1)*每页显示条数
    searchSourceBuilder.size(3); // 要查多少个

    // 设置排序规则
    searchSourceBuilder.sort("age",SortOrder.DESC);

    // 把所有条件设置给查询请求
    searchRequest.source(searchSourceBuilder);

    // 开始查询
    SearchResponse searchResponse =
            restHighLevelClient.search(searchRequest, RequestOptions.DEFAULT);
    // 查看结果(结果很多，所以循环)
    SearchHits hits = searchResponse.getHits();
    for (SearchHit hit: hits) {
        System.out.println(hit.getSourceAsString());
    }

}
```

##### 7.8字段过滤（当我们想要查询的结果中的字段不是所有的，只有表中的部分字段）

```java
// 测试过滤查询出来的字段（也就是当我们不想把表中所有的字段查出来）
@Test
public void testFilterDoc() throws IOException {
    // 构建搜索查询请求
    SearchRequest searchRequest = new SearchRequest().indices("nan_index");

    // 构建查询条件builder
    SearchSourceBuilder searchSourceBuilder = new SearchSourceBuilder();

    // 构建真正的查询条件（这里是全部查询）
    MatchAllQueryBuilder matchAllQueryBuilder = QueryBuilders.matchAllQuery();

    // 把查询条件设置给builder
    searchSourceBuilder.query(matchAllQueryBuilder);

    // 设置过滤字段
    String[] excludes = {};
    String[] includes = {"name"};
    searchSourceBuilder.fetchSource(includes,excludes);

    // 把所有的查询条件builder设置给查询请求
    searchRequest.source(searchSourceBuilder);

    //执行请求
    SearchResponse searchResponse = restHighLevelClient.search(searchRequest, RequestOptions.DEFAULT);

    //打印结果
    SearchHits hits = searchResponse.getHits();
    for (SearchHit hit:hits) {
        System.out.println(hit.getSourceAsString());
    }
}
```

##### 7.9多条件查询（也叫组合查询）

```java
// 多条件查询，也叫组合查询
    @Test
    public void testBoolqueryDoc() throws IOException {
        // 构建查询请求
        SearchRequest searchRequest = new SearchRequest("nan_index");

        // 构建搜索条件builder
        SearchSourceBuilder searchSourceBuilder = new SearchSourceBuilder();
        // 构建多条件builder
        BoolQueryBuilder boolQueryBuilder = new BoolQueryBuilder();
        // 在多条件builder中设置满足条件
        // must 就是必须满足这个条件(相当于mysql中column = 值)
        // mustnot 就是必须不满足这个条件（相当于mysql中column！=值）
        // should 就是或者的意思（相当于mysql中的or）
        boolQueryBuilder.must(QueryBuilders.termsQuery("name","xiaofei"));
//        boolQueryBuilder.must(QueryBuilders.termsQuery("age","24"));
        boolQueryBuilder.should(QueryBuilders.termsQuery("age","24"));
        boolQueryBuilder.should(QueryBuilders.termsQuery("age","28"));
        // 把多条件查询条件放到builder中
        searchSourceBuilder.query(boolQueryBuilder);

        // 把所有搜索条件设置到查询请求中
        searchRequest.source(searchSourceBuilder);
        // 执行请求
        SearchResponse searchResponse = restHighLevelClient.search(searchRequest, RequestOptions.DEFAULT);
        // 打印结果
        SearchHits hits = searchResponse.getHits();
        for(SearchHit hit :hits){
            System.out.println(hit.getSourceAsString());
        }
    }
```

##### 7.10 范围查询(就是某个字段在什么范围内)

```java
// 范围查询
@Test
public void testRangeDoc() throws IOException {
    // 构建查询请求
    SearchRequest searchRequest = new SearchRequest("nan_index");

    SearchSourceBuilder searchSourceBuilder = new SearchSourceBuilder();
    // 设置到范围的字段
    RangeQueryBuilder rangeQueryBuilder = new RangeQueryBuilder("age");
    // 设置范围（）gte就是大于等于
    rangeQueryBuilder.gte(24);
    rangeQueryBuilder.lte(30);
    // 把范围查询设置到条件中
    searchSourceBuilder.query(rangeQueryBuilder);
    searchRequest.source(searchSourceBuilder);
    SearchResponse searchResponse = restHighLevelClient.search(searchRequest, RequestOptions.DEFAULT);
    SearchHits hits = searchResponse.getHits();
    for(SearchHit hit :hits){
        System.out.println(hit.getSourceAsString());
    }
}
```

##### 7.11模糊查询

```java
// 模糊查询
@Test
public void testLikeDoc() throws IOException {
    SearchRequest searchRequest = new SearchRequest("nan_index");
    SearchSourceBuilder searchSourceBuilder = new SearchSourceBuilder();
    FuzzyQueryBuilder fuzzyQueryBuilder = QueryBuilders.fuzzyQuery("name", "xiao").fuzziness(Fuzziness.TWO);
    searchSourceBuilder.query(fuzzyQueryBuilder);
    SearchResponse searchResponse = restHighLevelClient.search(searchRequest, RequestOptions.DEFAULT);
    SearchHits hits = searchResponse.getHits();
    for(SearchHit hit :hits){
        System.out.println(hit.getSourceAsString());
    }
}
```

##### 7.12高亮查询

```java
//高亮查询
@Test
public void testHighLightDoc() throws IOException {
    // 构建搜索请求
    SearchRequest searchRequest = new SearchRequest("nan_index");

    // 构建搜索条件构造器(也就是总的搜索条件)
    SearchSourceBuilder searchSourceBuilder = new SearchSourceBuilder();

    // 构建单独的一个高亮构建器
    HighlightBuilder highlightBuilder = new HighlightBuilder();
    // 设置高亮字段
    highlightBuilder.preTags("<font color='red'>"); //前缀
    highlightBuilder.postTags("</font>");   // 后缀
    highlightBuilder.field("name");
    // 把单独的高亮构建器设置给总构建器
    searchSourceBuilder.highlighter(highlightBuilder);
    searchSourceBuilder.query(QueryBuilders.matchAllQuery());
    // 把总的搜索条件给到搜索请求中
    searchRequest.source(searchSourceBuilder);
    // 执行请求
    SearchResponse searchResponse = restHighLevelClient.search(searchRequest, RequestOptions.DEFAULT);
    // 打印结果
    SearchHits hits = searchResponse.getHits();
    for(SearchHit hit :hits){
        System.out.println(hit.getSourceAsString());
        // 获取对应的高亮域
        Map<String, HighlightField> highlightFields = hit.getHighlightFields();
        System.out.println(highlightFields);
        // 获取对应的高亮字段
        HighlightField highlightField = highlightFields.get("name");
        if(highlightField != null) {
            // 拿到高亮字段的文本域
            Text[] texts = highlightField.getFragments();
            String name = "";
            for (Text text : texts) {
                name += text;
                // 打印高亮字段
                System.out.println(name);
            }
        }
    }
}
```

##### 7.13最大值、平均值、最小值查询

```java
// 最大值、平均值、最小值
@Test
public void testAggraDoc() throws IOException {
    SearchRequest searchRequest = new SearchRequest("nan_index");
    SearchSourceBuilder searchSourceBuilder = new SearchSourceBuilder();
    // 构建一个最大值builder
    MaxAggregationBuilder maxAggregationBuilder = AggregationBuilders.max("MAXAGE").field("age");
    // 把最大值builder设置给总查询条件
    searchSourceBuilder.aggregation(maxAggregationBuilder);

    searchRequest.source(searchSourceBuilder);
    SearchResponse searchResponse = restHighLevelClient.search(searchRequest, RequestOptions.DEFAULT);
    SearchHits hits = searchResponse.getHits();
    for(SearchHit hit : hits){
        System.out.println(hit.getSourceAsString());
    }
}
```

##### 7.14分组查询

```java
// 分组查询
@Test
public void testAggraGroupDoc() throws IOException {
    SearchRequest searchRequest = new SearchRequest("nan_index");
    SearchSourceBuilder searchSourceBuilder = new SearchSourceBuilder();
    // 构建一个分组builder
    // terms里面的参数是给分组取的名字、后面field是要分组的字段
    TermsAggregationBuilder termsAggregationBuilder = AggregationBuilders.terms("AGEGROUP").field("age");
    // 把分组builder设置给总查询条件
    searchSourceBuilder.aggregation(termsAggregationBuilder);

    searchRequest.source(searchSourceBuilder);
    SearchResponse searchResponse = restHighLevelClient.search(searchRequest, RequestOptions.DEFAULT);
    SearchHits hits = searchResponse.getHits();
    for(SearchHit hit : hits){
        System.out.println(hit.getSourceAsString());
    }
}
```

