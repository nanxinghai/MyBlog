---
title: ElasticSearch-Springboot-Data使用
date: 2021-10-15 19:44:56
tags: ["spring-boot-starter-data-elasticsearch"]
cover: 
---

### ElasticSearch-Springboot-Data使用

之前说过ElasticSearch一共支持4种Api，本篇文章主要讲解Springboot数据层集成ElasticSearch的Api简单使用。先看看下面这个Spring集成数据层框架图

![Spring集成数据层框架图](https://img-blog.csdnimg.cn/7cf95889f65b49d08f002a701d5b7bdc.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA55Wq6IyE5ZGm5ZGm,size_20,color_FFFFFF,t_70,g_se,x_16)

可以看到Spring Data系列集成了非常多数据层框架，JDBC，Redis，ElasticSearch，Hadoop，Solr，MongoDB等，非常多啊，感觉spring是要一统天下啊，多的不说了，看看我们这个es怎么做

**注意：我这里es的版本是5.6.2，由于es官方更新较快（现在已经7.15），相关api的使用，内部方法也在变，所以别的版本可能会有些变化**

#### 1.创建springboot项目，导入相关依赖

首先parent父工程，我这里是2.0.5版本，对应里面的es版本刚好是5.x版本，如果不一样，需改变版本

```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.0.5.RELEASE</version>
</parent>
```

```xml
<dependencies>
    <!--springboot集成elasticsearch包-->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-elasticsearch</artifactId>
    </dependency>
    <!--springbootweb包-->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <!--测试包-->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
    </dependency>
    <!--lombok包-->
    <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
    </dependency>
    <!--elasticsearch不能存储对象，需要转换成json字符串-->
    <dependency>
        <groupId>com.alibaba</groupId>
        <artifactId>fastjson</artifactId>
        <version>1.1.15</version>
    </dependency>
</dependencies>
```

#### 2.创建好启动类（不做演示），以及yaml配置文件

```yml
spring:
    data:
      elasticsearch:
        cluster-name: elasticsearch
        cluster-nodes: 127.0.0.1:9300
```

#### 3.创建实体类，做es的文档对象

```java
@Data
@AllArgsConstructor
@NoArgsConstructor
@Document(indexName = "student",type = "infro")
public class Student {
    @Id
    private Long id;

    @Field(type = FieldType.Text,analyzer = "ik_max_word",searchAnalyzer = "ik_max_word")
    private String sname;

    @Field(type = FieldType.Keyword)
    private int sage;

    @Field(type = FieldType.Text,analyzer = "ik_max_word",searchAnalyzer = "ik_max_word")
    private String intro;
}
```

@Document(indexName = "student",type = "infro")

indexName:指定索引名

type：指定类型名

@Id：代表文档id

 @Field(type = FieldType.Text,analyzer = "ik_max_word",searchAnalyzer = "ik_max_word")

type：自定该字段的类型（Text和KeyWord都是String类型，Text代表分词，KeyWord代表不分词）

analyzer:指定存储时使用什么分词策略

searchAnalyzer:指定搜索时使用什么分词策略

#### 4.创建索引测试类

```java
@RunWith(SpringRunner.class)
@SpringBootTest(classes = ESApp.class)
public class EsIndexTest {
    @Autowired
    private ElasticsearchTemplate elasticsearchTemplate;

    @Test
    public void testCreateIndex(){
        // 创建索引
        elasticsearchTemplate.createIndex(Student.class);
        // 做文档映射
        elasticsearchTemplate.putMapping(Student.class);
    }
}
```

#### 5.创建文档测试类

```java
@RunWith(SpringRunner.class)
@SpringBootTest(classes = ESApp.class)
public class EsSimpleDocTest {

    @Autowired
    private EsRepository esRepository;

    // 添加文档
    @Test
    public void testAddDoc(){
        Student student = new Student(1L, "zhangsan", 18, "法外狂徒张三");
        esRepository.save(student);
    }
    // 修改文档:和添加文档一样，只不过id必须相同
    @Test
    public void testUpdateDoc(){
        Student student = new Student(1L, "lisi", 20, "法外狂徒李四");
        esRepository.save(student);
    }
    // 删除文档数据
    @Test
    public void testDelDoc(){
        esRepository.deleteById(1L);
    }
    // 查询数据
    @Test
    public void testSearchDoc(){
        // 获取全部数据
        Iterable<Student> students = esRepository.findAll();
        students.forEach(student -> {
            System.out.println(student);
        });
        System.out.println("=============");
        // 根据id获取
        Optional<Student> studentOptional = esRepository.findById(1L);
        Student student = studentOptional.get();
        System.out.println(student);
    }

    // 保存一堆数据
    @Test
    public void testAddMutilDoc(){
        ArrayList<Student> students = new ArrayList<>();
        students.add(new Student(1L,"zhangsan",18,"法外狂徒张三"));
        students.add(new Student(2L,"lisi",20,"那真是绝了"));
        students.add(new Student(3L,"王五",28,"法外狂徒张三"));
        students.add(new Student(4L,"周勇",30,"不会吧"));
        students.add(new Student(5L,"何总",25,"可恶"));
        students.add(new Student(6L,"lisi",18,"秀儿"));
        esRepository.saveAll(students);
    }
}
```

#### 6.文档高级搜索

```java
@RunWith(SpringRunner.class)
@SpringBootTest(classes = ESApp.class)
public class EsMutilSearchDocTest {
    @Autowired
    private EsRepository esRepository;

    // 高级查询
    @Test
    public void testMutilSearch(){
        // 创建一个查询构造器
        NativeSearchQueryBuilder builder = new NativeSearchQueryBuilder();

        // 查询条件(组合查询)
        BoolQueryBuilder boolQueryBuilder = QueryBuilders.boolQuery();
        // 必须满足sname匹配lisi字符
        boolQueryBuilder.must(QueryBuilders.matchQuery("sname","lisi"));
        // 过滤条件
        boolQueryBuilder.filter(QueryBuilders.rangeQuery("sage").gte(18).lte(30));
        builder.withQuery(boolQueryBuilder);

        //按照年龄排序
        builder.withSort(new FieldSortBuilder("sage").order(SortOrder.ASC));

        // 分页
        builder.withPageable(PageRequest.of(0,10));

        // 通过构造器构建查询对象
        NativeSearchQuery searchQuery = builder.build();

        // 查询获取到page对象
        Page<Student> studentPage = esRepository.search(searchQuery);
        // 总条数
        System.out.println(studentPage.getTotalElements());
        // 总分页
        System.out.println(studentPage.getTotalPages());

        // 获取到真正需要的对象
        List<Student> students = studentPage.getContent();
        // 循环打印
        students.forEach(student -> {
            System.out.println(student);
        });
    }
}
```

#### 7.文档高亮搜素

##### 7.1SpringbootData中没有对高亮结果处理，所以要自己写结果处理器，并交给spring管理

```java
@Component
public class HighlightResultMapper implements SearchResultMapper {

    @Override
    public <T> AggregatedPage<T> mapResults(SearchResponse response, Class<T> aClass, Pageable pageable) {
        // 记录总条数
        long totalHits = response.getHits().getTotalHits();
        // 记录列表(泛型) - 构建Aggregate使用
        List<T> list = Lists.newArrayList();
        // 获取搜索结果(真正的的记录)
        SearchHits hits = response.getHits();
        for (SearchHit hit : hits) {
            if(hits.getHits().length <= 0){
                return null;
            }
            // 将原本的JSON对象转换成Map对象
            Map<String, Object> map = hit.getSourceAsMap();
            // 获取高亮的字段Map
            Map<String, HighlightField> highlightFields = hit.getHighlightFields();
            for (Map.Entry<String, HighlightField> highlightField : highlightFields.entrySet()) {
                // 获取高亮的Key
                String key = highlightField.getKey();
                // 获取高亮的Value
                HighlightField value = highlightField.getValue();
                // 实际fragments[0]就是高亮的结果，无需遍历拼接
                Text[] fragments = value.getFragments();
                StringBuilder sb = new StringBuilder();
                for (Text text : fragments) {
                    sb.append(text);
                }
                // 因为高亮的字段必然存在于Map中，就是key值
                // 可能有一种情况，就是高亮的字段是嵌套Map，也就是说在Map里面还有Map的这种情况，这里没有考虑
                map.put(key, sb.toString());
            }
            // 把Map转换成对象
            T item = JSON.parseObject(JSONObject.toJSONString(map),aClass);
            list.add(item);
        }
        // 返回的是带分页的结果
        return new AggregatedPageImpl<>(list, pageable, totalHits);
    }

}
```

##### 7.2文档高亮测试

```java
@RunWith(SpringRunner.class)
@SpringBootTest(classes = ESApp.class)
public class EsHighlightMutilSearchTest {
    @Autowired
    private ElasticsearchTemplate elasticsearchTemplate;

    @Autowired
    private HighlightResultMapper highlightResultMapper;

    // 测试高亮
    @Test
    public void testHighlightSearch(){
        // 构建查询构造器
        NativeSearchQueryBuilder searchQueryBuilder = new NativeSearchQueryBuilder();
        // 设置查询条件(全部查询)
        searchQueryBuilder.withQuery(QueryBuilders.matchAllQuery());
        // 分页、排序那些就不写了，直接写高亮
        // 构建高亮查询(分别设置高亮字段以及其前缀后缀)
        HighlightBuilder.Field highlightBuilder = new HighlightBuilder.Field("sname").preTags("<font color='red'>").postTags("</font>");
        // 将高亮设置进去
        searchQueryBuilder.withHighlightFields(highlightBuilder);
        // 构建出具体条件
        NativeSearchQuery searchQuery = searchQueryBuilder.build();

        //开始查询(以高亮的方式查询)
        AggregatedPage<Student> page = elasticsearchTemplate.queryForPage(searchQuery, Student.class, highlightResultMapper);

        // 打印结果
        System.out.println(page.getTotalElements());
        System.out.println(page.getTotalPages());
        List<Student> students = page.getContent();
        students.forEach(student -> {
            System.out.println(student);
        });
    }

}
```

#### 8.聚合查询

##### 8.1聚合查询也是一样，springboot没有处理器需要自己创建

其实跟高亮那个处理器一样，只不过在返回值时多加了一个聚合

```java
@Component
public class AggregationResultMapper implements SearchResultMapper {

    @Override
    public <T> AggregatedPage<T> mapResults(SearchResponse response, Class<T> aClass, Pageable pageable) {
        // 记录总条数
        long totalHits = response.getHits().getTotalHits();
        // 记录列表(泛型) - 构建Aggregate使用
        List<T> list = Lists.newArrayList();
        // 获取搜索结果(真正的的记录)
        SearchHits hits = response.getHits();
        for (SearchHit hit : hits) {
            if(hits.getHits().length <= 0){
                return null;
            }
            // 将原本的JSON对象转换成Map对象
            Map<String, Object> map = hit.getSourceAsMap();
            // 获取高亮的字段Map
            Map<String, HighlightField> highlightFields = hit.getHighlightFields();
            for (Map.Entry<String, HighlightField> highlightField : highlightFields.entrySet()) {
                // 获取高亮的Key
                String key = highlightField.getKey();
                // 获取高亮的Value
                HighlightField value = highlightField.getValue();
                // 实际fragments[0]就是高亮的结果，无需遍历拼接
                Text[] fragments = value.getFragments();
                StringBuilder sb = new StringBuilder();
                for (Text text : fragments) {
                    sb.append(text);
                }
                // 因为高亮的字段必然存在于Map中，就是key值
                // 可能有一种情况，就是高亮的字段是嵌套Map，也就是说在Map里面还有Map的这种情况，这里没有考虑
                map.put(key, sb.toString());
            }
            // 把Map转换成对象
            T item = JSON.parseObject(JSONObject.toJSONString(map),aClass);
            list.add(item);
        }
        // 返回的是带分页的结果
        return new AggregatedPageImpl<>(list, pageable, totalHits,response.getAggregations());
    }

}
```

##### 8.2聚合查询测试

```java
@RunWith(SpringRunner.class)
@SpringBootTest(classes = ESApp.class)
public class EsAggregationSearchTest {
    @Autowired
    private ElasticsearchTemplate elasticsearchTemplate;
    @Autowired
    private AggregationResultMapper aggregationResultMapper;
    // 聚合查询
    @Test
    public void testAggregationSearch(){
        NativeSearchQueryBuilder searchQueryBuilder = new NativeSearchQueryBuilder();

        searchQueryBuilder.withQuery(QueryBuilders.matchAllQuery());
        // 聚合查询
        TermsAggregationBuilder termsAggregationBuilder = AggregationBuilders.terms("paixu").field("sage").order(Terms.Order.count(false));
        // 添加聚合查询
        searchQueryBuilder.addAggregation(termsAggregationBuilder);

        NativeSearchQuery searchQuery = searchQueryBuilder.build();
        // 开始查询
        AggregatedPage<Student> page = elasticsearchTemplate.queryForPage(searchQuery, Student.class, aggregationResultMapper);


        StringTerms terms = (StringTerms) page.getAggregation("paixu");
        List<StringTerms.Bucket> buckets = terms.getBuckets();
        //把机构名字聚合，搜集成字符串里集合
        List<String> sages = buckets.stream().map(bucket -> bucket.getKeyAsString()).collect(Collectors.toList());
        System.out.println(sages);

    }
}
```

