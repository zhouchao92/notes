---
title: SpringBoot中使用Solr作为搜索引擎
tags:
  - spring boot
  - solr
categories:
  - notes
  - 后端
abbrlink: 4499c37a
date: 2020-08-18 13:29:17
---

##### 前言

在Spring Boot中，从数据库中通过关键字查询需要通过 like , equal 等，而且中文分词的操作比较复杂，不利于逻辑实现和代码书写。使用Solr作为Spring Boot项目的全文搜索引擎，效率更高，代码更简易。

以文章为模型，演示Solr的增删改查。

<!---more--->

##### 数据模型

文章：

- id 编号
- title 标题
- content 内容
- labels 标签
- createTime 创建时间

通过关键词对文章的标题、内容、标签中进行搜索。

##### 配置

配置部分包括Solr部分和Spring Boot项目两个部分。

###### Solr配置

修改`Solr\server\solr\example_core\conf\managed-schema`，在\<schema\>标签内添加文章相关配置

- field 为字段名
- \<copyField source="字段名" dest="search_item"/\>  作为搜索项

```xml
<!-- 文章 -->
<!-- 标题 -->
<field name="article_title" type="text_cn" indexed="true" stored="true" required="true" multiValued="false" />
<!-- 内容 -->
<field name="article_content" type="text_cn" indexed="true" stored="true" required="true" multiValued="false" />
<!-- 创建时间 -->
<field name="article_create_time" type="pdate" indexed="true" stored="true" required="true" multiValued="false" />
<!-- 标签 -->
<field name="article_labels" type="string" indexed="true" stored="true" required="true" multiValued="false" />
<!-- 搜索item -->
<field name="search_item" type="text_cn" indexed="true" stored="true" required="true" multiValued="true" />
<copyField source="article_title" dest="search_item"/>
<copyField source="article_content" dest="search_item"/>
<copyField source="article_labels" dest="search_item"/>
```

###### Spring Boot项目配置

1. 创建以maven为依赖管理的spring boot项目

2. 添加web，solr，gson 相关依赖

   ```xml
   <dependency>
       <groupId>org.springframework.boot</groupId>
       <artifactId>spring-boot-starter-web</artifactId>
   </dependency>
   
   <!-- gson -->
   <dependency>
       <groupId>com.google.code.gson</groupId>
       <artifactId>gson</artifactId>
       <version>2.8.6</version>
   </dependency>
   
   <!-- solr -->
   <dependency>
       <groupId>org.springframework.boot</groupId>
       <artifactId>spring-boot-starter-data-solr</artifactId>
   </dependency>
   ```

3. 在 `application.yml` 中配置项目端口号和solr的相关配置

   ```yaml
   # 端口
   server:
     port: 8000
   
   spring:
     data:
       # 配置solr
       solr:
         host: http://localhost:8983/solr/example_core
   ```

##### Java

项目文件的结构

```powershell
├─java
│  └─com
│      └─study
│          └─springbootsolr
│              │  SpringBootSolrApplication.java 
│              │
│              ├─controller
│              │      SolrController.java
│              │
│              ├─pojo
│              │      PageList.java
│              │      SolrSearchResult.java
│              │
│              └─service
│                      SolrService.java
│
└─resources
    │  application.yml
    │
    ├─static
    └─templates
```

###### SolrSearchResult.java

```java
public class SolrSearchResult implements Serializable {

    @Field("id")
    private String id;
    @Field("article_content")
    private String content;
    @Field("article_create_time")
    private Date createTime;
    @Field("article_labels")
    private String labels;
    @Field("article_title")
    private String title;
    
    getters/setters...
    toString...
    
}
```

###### PageList.java

```java
public class PageList<T> implements Serializable {

    private long currentPage;   // 当前页码
    private long totalCount;    // 总共有多少条
    private long pageSize;      // 每页内容量
    private long totalPage;     // 总页数
    private boolean first;  // 是否是第一页
    private boolean last;   // 是否是最后一页
    private List<T> content;   // 内容
    
    getters/setters...
    toString...
}
```

###### SolrController.java

```java
@RestController
@RequestMapping("/test")
public class SolrController {

    @Autowired
    private SolrService solrService;

    @PostMapping("/solr")
    public String testSolrAdd() {
        return solrService.add();
    }

    @PutMapping("/solr")
    public String testSolrUpdate() {
        return solrService.update();
    }

    @DeleteMapping("/solr")
    public String testSolrDelete() {
        return solrService.delete();
    }

    @GetMapping("/solr/{page}/{size}")
    public String testSolrSearch(@PathVariable("page") int page,
                                 @PathVariable("size") int size,
                                 @RequestParam("keyword") String keyword) {
        return solrService.search(keyword, page, size);
    }
}
```

###### SolrService.java

```java
@Service
public class SolrService {

    @Autowired
    private SolrClient solrClient;

    public String add() {
        SolrInputDocument doc = new SolrInputDocument();
        doc.addField("id", "730469770008199168");
        doc.addField("article_title", "SpringBoot中使用Solr作为搜索引擎");
        doc.addField("article_content", "这是测试solr添加的文章内容。");
        doc.addField("article_create_time", new Date());
        doc.addField("article_labels", "后端-测试-solr");
        try {
            solrClient.add(doc);
            solrClient.commit();
        } catch (SolrServerException | IOException e) {
            e.printStackTrace();
            return "文章添加失败";
        }
        return "文章添加成功";
    }

    public String update() {
        SolrInputDocument doc = new SolrInputDocument();
        doc.addField("id", "730469770008199168");
        doc.addField("article_title", "SpringBoot中使用Solr作为搜索引擎");
        doc.addField("article_content", "<p>文章更新，这是测试solr更新的文章内容。</p>");
        doc.addField("article_create_time", new Date());
        doc.addField("article_labels", "后端-测试-solr");
        try {
            solrClient.add(doc);
            solrClient.commit();
        } catch (SolrServerException | IOException e) {
            e.printStackTrace();
            return "文章更新失败";
        }
        return "文章更新成功";
    }

    public String delete() {
        try {
            // 单独删除一条记录
            solrClient.deleteById("730469770008199168");
            // 提交
            solrClient.commit();
        } catch (SolrServerException | IOException e) {
            e.printStackTrace();
            return "文章删除失败";
        }
        return "文章删除成功";
    }


    public String search(String keyword, int page, int size) {
        // 参数检查
        if (page < 1) page = 1;
        if (size < 5) size = 5;

        // 分页设置
        SolrQuery solrQuery = new SolrQuery();
        solrQuery.setRows(size);    // 每页数量
        solrQuery.setStart(size * (page - 1));   // 设置开始位置

        // 设置搜索条件
        solrQuery.set("df", "search_item"); //  设置搜索关键字：标题，内容，标签
        solrQuery.set("q", TextUtils.isEmpty(keyword) ? "*" : keyword); // 条件过滤

        // 时间的降序
        solrQuery.setSort("article_create_time", SolrQuery.ORDER.desc);

        solrQuery.setHighlight(true);   // 开启高亮
        solrQuery.addHighlightField("article_title,article_content"); // 高亮字段
        solrQuery.setHighlightSimplePre("<font color='red'>");  // 高亮前缀
        solrQuery.setHighlightSimplePost("</font>");            // 高亮后缀
        solrQuery.setHighlightFragsize(400);    // 设置字符数

        // 设置返回字段
        solrQuery.addField("id,article_content,article_create_time,article_labels,article_title");

        // 处理搜索结果
        try {
            QueryResponse result = solrClient.query(solrQuery);
            // 获取高亮内容
            Map<String, Map<String, List<String>>> highlighting = result.getHighlighting();
            // 把数据转换成bean类
            List<SolrSearchResult> beans = result.getBeans(SolrSearchResult.class);
            for (SolrSearchResult item : beans) {
                Map<String, List<String>> stringListMap = highlighting.get(item.getId());
                if (stringListMap == null) continue;
                List<String> title = stringListMap.get("article_title");
                // 替换文章标题为高亮
                if (title != null) {
                    item.setTitle(title.get(0));
                }
                List<String> content = stringListMap.get("article_content");
                // 替换文章内容为高亮
                if (content != null) {
                    item.setContent(content.get(0));
                }
            }
            // 封装结果：列表，页面，每页数量
            PageList<SolrSearchResult> pageList = new PageList<>();
            pageList.setContent(beans);
            pageList.setCurrentPage(page);
            pageList.setTotalCount(result.getResults().getNumFound());
            pageList.setPageSize(size);
            pageList.setTotalPage((int) (pageList.getTotalCount() / size + 0.5));
            pageList.setFirst(pageList.getCurrentPage() == 1);
            pageList.setLast(pageList.getCurrentPage() == pageList.getTotalPage());
            // 返回搜索结果
            Gson gson = new Gson();
            return gson.toJson(pageList);
        } catch (SolrServerException | IOException e) {
            e.printStackTrace();
        }
        return "搜索失败，请稍后重试！";
    }
}
```

##### 测试

使用postman测试增删改查，以及solr客户端查询

###### 添加

postman

- 请求url：http://localhost:8000/test/solr
- 请求方式：post

![solr添加文章](https://gitee.com/zyy92/Pictures/raw/master/Solr/solr%E6%96%87%E7%AB%A0%E6%B7%BB%E5%8A%A0.png)

###### 查询

solr网页客户端进行查询（查询所有，不过滤）

![solr网页客户端搜索结果](https://gitee.com/zyy92/Pictures/raw/master/Solr/solr%E5%AE%A2%E6%88%B7%E7%AB%AF%E6%90%9C%E7%B4%A2%E7%BB%93%E6%9E%9C.png)

postman 模拟搜索

- 请求url：http://localhost:8000/test/solr/1/5?keyword=solr
- 请求方式：get

![postman搜索添加文章结果](https://gitee.com/zyy92/Pictures/raw/master/Solr/postman%E6%90%9C%E7%B4%A2%E6%B7%BB%E5%8A%A0%E7%BB%93%E6%9E%9C.png)

###### 修改

postman

- 请求url：http://localhost:8000/test/solr
- 请求方式：put

![solr文章g更新](https://gitee.com/zyy92/Pictures/raw/master/Solr/solr%E6%96%87%E7%AB%A0%E6%9B%B4%E6%96%B0.png)

solr客户端查看更新结果

![solr客户端查看更新结果](https://gitee.com/zyy92/Pictures/raw/master/Solr/solr%E5%AE%A2%E6%88%B7%E7%AB%AF%E6%96%87%E7%AB%A0%E6%9B%B4%E6%96%B0%E7%BB%93%E6%9E%9C.png)

###### 删除

postman

- 请求url：http://localhost:8000/test/solr
- 请求方式：delete

![solr删除文章](https://gitee.com/zyy92/Pictures/raw/master/Solr/solr%E6%96%87%E7%AB%A0%E5%88%A0%E9%99%A4.png)

solr客户端查看删除结果

![solr客户端删除结果](https://gitee.com/zyy92/Pictures/raw/master/Solr/solr%E5%AE%A2%E6%88%B7%E7%AB%AF%E5%88%A0%E9%99%A4%E7%BB%93%E6%9E%9C.png)