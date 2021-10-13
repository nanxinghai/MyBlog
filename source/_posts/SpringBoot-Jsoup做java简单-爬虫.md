---
title: 使用SpringBoot+Jsoup做java简单'爬虫'
date: 2021-09-28 22:07:30
tags: "Jsoup 爬虫"
cover: https://z3.ax1x.com/2021/09/26/4crmQO.jpg
---

### 使用SpringBoot+Jsoup做java简单'爬虫'

---

#### Jsoup简介

Jsoup是一款java的Html解析器，可以直接解析Html。针对网页提供了相关Api，可以通过Dom、Css、Jquery等属性来操作和解析网页。

---

#### 主要类介绍：

- Jsoup 类提供了连接，清理和解析HTML文档的方法
- Document 获取HTML文档
- Element 获取、操作HTML节点

#### 实战（爬京东网页数据）

##### 1.创建Maven项目

##### 2.导入pom依赖

​	根据自己需求减少或增加依赖

```xml
<dependencies>
    <!--jsoup相关依赖-->
    <dependency>
        <groupId>org.jsoup</groupId>
        <artifactId>jsoup</artifactId>
        <version>1.10.2</version>
    </dependency>
    <!--springbootweb包-->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <!--springboot测试包-->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
        <scope>test</scope>
    </dependency>
</dependencies>
```

##### 3.编写工具类（方便调用）

我这里写的比较简单，相关复杂设置可以去网上在搜一搜

```java
public class HtmlParseUtil {
    /**
     * 解析出网页的docment文档
     * @param keyword 网页需要变更的关键字
     * @param page  网页需要变更的页码
     * @return
     */
    public static Document getDocment(String keyword,Integer page){
        //拼接好url
        String url = "https://search.jd.com/Search?keyword=" + keyword + "&page=" +page;
        try {
            // jsoup解析url，第二个参数是超时时间
            Document document = Jsoup.parse(new URL(url), 9000);
            return document;
        } catch (IOException e) {
            e.printStackTrace();
        }
        return null;
    }
}
```

##### 4.相关测试

编写测试类，调用工具类

```java
@SpringBootTest
public class HtmlTest {

    @Test
    public void HtmlParseTest(){
		//使用工具类，这里本质就是在京东搜索keyword=java分页page=1
        Document document = HtmlParseUtil.getDocment("java", 1);
		//获取文档中class为gl-warp clearfix的节点集合
        Elements elements = document.getElementsByClass("gl-warp clearfix");
		//拿到上面文档集合中的第一个，并获取到标签名为li的节点集合
        Elements lis = elements.get(0).getElementsByTag("li");
        //循环遍历li的节点集合
        for (int i = 0; i < lis.size(); i++) {
			//拿到节点里的bookname
            Element booknames = lis.get(i).getElementsByClass("p-name").get(0);
            String bookname = booknames.getElementsByTag("em").get(0).text();
            System.out.println(bookname);
			//拿到节点里的img
            Element elementsByClass = lis.get(i).getElementsByClass("p-img").get(0);
            String img = elementsByClass.getElementsByTag("img").get(0).attr("data-lazy-img");
            System.out.println(img);
			//拿到节点里的price
            Element prices = lis.get(i).getElementsByClass("p-price").get(0);
            String price = prices.getElementsByTag("i").get(0).text();
            System.out.println(price);
        }
    }
}
```

后续可以根据自己需求做不同的逻辑（比如把它封装成一个list，批量添加到es中）
