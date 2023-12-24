##  字段定义

ORM (Object Relational Mapping，对象  关系映射）。  
“对象关系映射” 昕起来有点学术化，不太好理解 。 用大白话解释一下就很容易明白，那就  是把我们定义的对象（类）映射到对应的数据库的表上 。 所以 ORM 就是代码（软件）层面对于  数据库表和关系 的一种抽象。  
Django 的 Model 就是 ORM的一个具体实现 。
主要包含两部分：数据定义，SQL的CRUD操作

****

**一、数值型**

- AutoField对应int(11)。自增主键，Django Model默认提供，可以被重写。
- BooleanField对应tinyint(1)。布尔类型字段，一般用于记录状态标记。
- DecimalField对应decimal。开发对数据精准要求较高大的业务时考虑使用。比如：cash=models.DecimalField(max_length, decimal_places=2, default=0, verbose_name=“消费金额”)，就是定义长度为8位、精度位2位的数字，例如数字：666666.66。
- IntergerField对应**int(11) **。同AutoField一样，唯一的差别就是不自增。
- PositiveIntegerField。同IntegerField，只包含正整数。
- SmallIntegerField对应smallint。小整数时一般会用到。

**二、字符型**

django对应到Mysql中有两种类型：longtext和varchar。

 除了TextField是longtest类型外，其他属于varchar类型。

- CharField对应varchar。基础的varchar类型。
- URLField.继承自CharField，但是实现了对URL特特殊处理。用来存储URL数据，非URL数据可以在业务层就拒绝掉，不会存入数据库中。
- UUIDField对应char(32)。除了在PostgreSQL中使用的是uuid类型外，在其他数据库中均是固定长度char(32)，用来存放生成的唯一id。
- EmailField。同URLfield一样继承自CharField，多了对email的特殊处理。
- FileField。同URLField一样，它继承自CharField，对了对文件的特殊处理。
- TextField对应longtext。一般用于存放大量文本内容，比如新闻正文、博客正文。
- ImageField。继承自FileField，用来处理图片相关的数据，在展示上会有所不同。

**三、日期类型**

django中有3中日期类型，分别对应Mysql的date、datetime和time

- DateField对应date
- DateTimeField对应datetime
- TimeField对应time

**四、关系类型**

- ForeignKey,外键
- OneToOneField，一对一
- ManyToManyField，多对多

**五、参数**

上面django所提供的字段都是类。

比如models.CharField的定义就是：class CharField：。

每个字段里面都提供了参数可以选择：

- null。可以同blank进行对比。null用于设定在数据库层面是否允许为空
- blank。针对业务层面，该值是否允许为空。
- choices。配置字段的choices后，在admin页面上就可以看到对应的可选项展示。
- db_column。默认情况下，定义的field就是对应数据库中的字段名称，通过这个参数可以指定Model中的某个字段对应数据库中的哪个字段。
- db_index。数据库索引配置。
- default。默认值配置。
- editable。是否可编辑，默认是True。如果不想这个字段显示在页面上，可以配置为False。
- error_messages。用来自定义字段值校验失败时的异常提示，它是字典格式。key的值可选项为null、blank、invalid、invalid_choice、unique和unique_for_date。
- help_text。字段提示语，配置这一项后，在页面对应字段的下方会展示此配置。
- primary_key。主键，一个Model只允许设置一个字段为primary_key。
- unique。唯一约束，当需要配置唯一值时，设置unique=True，设置此项后，不需要设置db_index。
- unique_for_date。针对date（日期）的联合约束。比如说一天只能写一篇博文，即：unique_for_date=“博文”
- unique_for_month。针对月份的联合约束 。
- unique_for_month。针对年份的联合约束。
- verbose_name。字段对应的展示文案。
- validators。自定义校验逻辑，同form类似。

**关于Django字段类型中 blank和null的区别**

**blank**

设置为True时，字段可以为空。设置为False时，字段是必须填写的。字符型字段CharField和TextField是用空字符串来存储空值的。

如果为True，字段允许为空，默认不允许。

**null**

设置为True时，django用Null来存储空值。日期型、时间型和数字型字段不接受空字符串。所以设置IntegerField，DateTimeField型字段可以为空时，需要将blank，null均设为True。

如果为True，空值将会被存储为NULL，默认为False。

如果想设置BooleanField为空时可以选用NullBooleanField型字段。

**一句话概括**

- null 是针对数据库而言，如果 null=True, 表示数据库的该字段可以为空。
- blank 是针对表单的，如果 blank=True，表示你的表单填写该字段的时候可以不填，比如 admin 界面下增加 model 一条记录的时候。直观的看到就是该字段不是粗体
- 通俗点说，该字段null=true后，你进行插入，修改操作时可以为空，然后Django把空值转换成null存在数据库中，而blank只是在表单验证的时候会检测你是否可以为空

***

## CRUD 

Queryset的特性

- 支持链式查询

- 本质上是个懒加载对象

  ```
  posts= Post.objects.all() ＃返回一个 QuerySet 对象并赋位给 posts
  available_posts = posts.filter(status=l) ＃继续返回一个 Query Set 对 象并赋位给available_posts
  print(available_posts) ＃ 此时会根据上面的两个条件执行数据查询操作，对应的 SQL 语句为 SELECT * FROM blog_post where #status =1;
  ```

### 常用的Queryset接口

- 支持链式调用的接口即 返回 QuerySet 对象的接口，具体如下 ：

  - all 接口，相当于 SELECT * FROM table name 语句，用于查询所有数据。

  - filter 接口，顾名思义，根据条件过滤数据，常用的条件基本上是

    字段等于、不等于、大于、小于。 当然，还有其他的，比如能改成产生 LIKE 查询的 ： Model . objects . filter
    (content_contains ＝ ” 条件 ” ） 

  -  exclude 接口 。同 filter ，只是相反的逻辑 。

  - reverse 接口 。把 QuerySet 中的结果倒序排列

  - distinct 接口 。 用来进行去重查询 ， 产生 SELECT DISTINCT 这样的 SQL 查询 。

  - none 接口 。 返回空 的 Query Set 。  

- 支持链式调用的接口

  - get 接口 。 比如 Post.objects . get(id=1 ） 用于查询 id 为 1 的文章 : 如果存在 ， 则直接返 回对应的 Post 实例；如果不存在， 则抛出 DoesNotEx 工 st 异常 。 所 以 一般情况下 ，我们会这么用 ：
    ``` 
    try :
    	post = Post.objects .get(id=l)
    except Post.DoesNotExist:
    	＃做异常情况处理
    ```

  -  create 接口 。 用来直接创建一个 Model 对象， 比如 post = Post.objects.create(title＝”一起学习 Django实战吧 ” ） 。

  - get_or_create 接口 。 根据条件查找， 如果没查找到， 就调用 create 创建 。

  - update_or_create 接口 。 同 get_or_create ，只是用来做更新操作 。

  - count 接口 。 用于返回 QuerySet 有多少条记录， 相当于 SELECT COUNT ( * ) FROM table_arne 。

  - latest 接口 。 用于返回最新的一条记录， 但是需要在 Model 的 Meta 中定义 ： get_latest_by = ＜用来排序的 字段〉。

  -  earliest 接口 。 同上，返回最早的一条记录 。

  - first 接口 。 从当前 Query Set 记录中获取第一条 。

  -  last 接口 。 同上，获取最后一条。

  - exists 接口 。 返回 True 或者 False ，在数据库层面执行 SELECT (1 ) AS ” a ” FROM table_ narne LIMIT 1 的查询，如果只是需要判断 QuerySet 是否有数据，用这个接口是最合适的方式 。 不要用 count 或者 len(queryset ）这样的操作来判断是否存在 。 相反， 如果可以预期接下来会用到 QuerySet 中的数据 ，可以考虑使用len（ queryset)的方式来做判断 ，这样可以减少一次 DB 查询请求 。

  -  bulk_create 接口 。 同 create ，用来批量创建记录 。

  -  in_bulk 接口 。 批量查询， 接收两个参数 id_list 和 filed_narne 。 可 以通过Pos .ob] ects.i n_ bulk ( [1, 2 , 3] ）查询出 id 为 1 、2 、3 的数据，返回结果是字典类型，字典类型的 key 为查询条件。 返回结果示例 :｛ 1: <Post 实例1] l> , 2 : <Post
    实例 2> , 3 : <Po s t 实 171) 3> ｝ 。

  - update 接口 。 用来根据条件批量更新记录，比如 ： Post.obj ects.filter(owner_narne=
    ’ sxl' ) .update(title ＝ ’ 测试更新 ’ ）

  -  delete 接口 。 同 update ，这个接口是用来根据条件批量删除记录。 需要注意的是 ，update
    和 delete 都会触发 Django 的 signal 。

  -  values 接口 。 当我们明确知道只需要返回某个字段的值 ，不需要 Model 实例时，可以使
    用它，用法如下：

    ```
     title_list= Post.objects filter(category_id=1).values('title' )＃返回的结果包含 dict的QuerySet ，类似这样 #＜QuerySet [{ ' title ’: xxx} ,) >
    ```

  - values_list 接口 。 同 values ，但是直接返回的是包含 tuple 的 QuerySet :
    ```
    titles_list = Post . objects . fi lter(categoryr=l) . values_list ( 'title ' )
    ```

    ＃返回结果类似：＜QuerySet [ （ ’ 标题 ’， ）］〉
    如果只是一个字段的话，可以通过增加 flat=True 参数， 便于我们后续处理 ：

    ```
    title_list =Post objects . filter(category=1) .values_list ( 'title', flat=True)
    for title in title list :
    	print(t itle)
    ```

    ***

### 进阶接口

- defer 接口 。 把不需要展示的字段做延迟加载 。 比如说， 需要获取到文章中除正文外的其他字段，就可以通过

  posts = Post.objects.all ().defer ( ' content ’ ），这样拿到的记录中就不会包含 content 部分。 但是当我们需要用到这个字段时，在使用时会去加载。 下面还是通过代码演示 ：

  ```
  posts=Post.objects.all ().defer( 'content ’ )
  for post in posts : ＃ 此时会执行数据库查询
  	print(post.content) ＃此时会执行数据查询，获取到 content
  ```

- only 接口 。 同 defer 接口 刚好相反，如果只想获取到所有的 title 记录，就可以使用only ，只获取 title 的内容 ， 其他值在获取时会产生额外的查询 。

- select_related 接口 。 这就是用来解决外键产生的 N+I 问题的方案 。 

  我们先来看看什么情况下会产生这个问题 ：

  ```
  posts =Post objects.all()
  for post in posts : ＃产生数据库查询
  	print(post.owner) ＃产生额外的数据库查询
  ```

  代码同上面类似，只是这里用的是 owner （关联表）。它的解决方法就是用 select_related 接口 ：

  ```
  post = Post.objects.all().select_related ('category')
  for post 工n posts ＃产生数据库查询， category 数据也会一次性查询出未
  print(post category)
  ```

  当然 ，这个接口只能用来解决一对多的关联关系 。 对于多对多的关系 ，还得使用下面的接口 。

- prefetch related 接口 。 针对多对多关系的数据，可 以通过这个接口来避免 N+ l 查询 。
  比如， post 和 tag 的关系可以通过这种方式来避免 ：

  ```
  posts=Post.objects.all().prefetch_related ('tag')
  for post in posts: ＃产生两条查询语句，分别查询 post 和 tag
  	print (post.tag.all () )
  ```

  ***

	### 常用的字段查询

- contains ：包含， 用来进行相似查询 。

- icontains ：同 contains ，只是忽略大小写 。

-  exact ： 精确匹配 。

- iexact ：同 exact ，忽略大小写 。

- in ：指定某个集合 ，比如 Post.objects.filter(id_in＝［1 ,2 ,3）相当于 SELECTnFROM blog_post WHERE IN (1, 2, 3 ）； 

- gt ：大于某个值 。

- gte ： 大于等于某个值 。

- lt ：小于某个值 。

-  lte ：小于等于某个值。

- startswith ：以某个字符串开头，与 contains 类似，只是会产生 LIKE ’ 〈关键词 〉 ’这样的 SQL。

- istartswith ：同 startswith ，忽略大小写 。

   endswith ：以某个字符串结尾 。

   iendswith ：同 endswith ，忽略大小写 。

   range ：范围查询，多用于时间范围，如 Post.objects. filter(created_time range=( ' 2018 一 05 一 01 ’，＇ 2018 一 06 一 01 ’ ））会产生这样的查询 ： SELECT ... WHERE created_ time BETWEEN ' 2018-05 - 0 1 ’ AND ’ 2018 06 一 0 1 ’； 。
  关于 日期类的查询还有很多 ， 比如 date 、 year 和 month 等，具体等需要时查文档 即可 

### 进阶查询

除了上面基础的查询表达式外， Django 还提供了其他封装，用来满足更复杂的查询，比如 SELECT . . . WHERE id = 1 OR id = 2 这样的查询，用上面的基础查询就无法满足 。

- F 。 F 表达式常用来执行数据库层面的计算，从而避免出现竞争状态 。 比如需要处理每篇文章的访问量，假设存在 post . pv 这样的字段，当有用户访问时，我们对其加 1 :

```
post = Post.objects.get(id=1)
post.pv = post.pv + 1
pos.save ()
```

这在多线程的情况下会出现问题，其执行逻辑是先获取到当前的 pv 值，然后将其加 1后赋值给 post . pv，最后保存。 如果多个线程同时执行了 post= Post . objects . get(id=1),那么每个线程里的 post . pv 值都是一样的，执行完加 1 和保存之后，相当于只执行了一个加 1，而不是多个 。
其原因在于我们把数据拿到 Python 中转了一圈，然后再保存到数据库中 。 这时通过 F 表达式就可以方便地解决这个问题 ：

```
from django.db.models import F
post = Post.objects.get(id=l)
post.pv = F ('pv' ) + 1
post.save ()
```

这种方式最终会产生类似这样的 SQL 语句 ： UPDATE blog_post SET pv = pv + 1 WHEREID = 1 。 它在数据库层面执行原子性操作 。

- Q。 Q 表达式就是用来解决前面提到的那个 OR 查询的，可以这么用 ：

```
from django .db . models import Q
Post.objects.filter(Q(id=l) | Q(id=2))
```

或者进行 AND 查询 ：
```
Post.objects.filter(Q(id=l) & Q(id=2))
```

- cout。 用来做聚合查询，比如想要得到某个分类下有多少篇文章，怎么做呢？简单的做法就是 ：

```
category = Category.objects.get(id=l)
posts_count = category.post_set.count()
```

但是如果想要把这个结果放到 category 上呢？通过 category.post_count 可以访问到 ：
```
from django.db.mdoels import Count
categories= Category.objects.annotate(posts_count=Count ('post'))
	print(categories[OJ .posts_count)  
```

这相当于给 category 动态增加了属性 posts_count ，而这个属性的值来源于 Count ( 'post ' ） 。

- Sum。 同 Count 类似 ，只是它是用来做合计的 。 比如想要统计 目 前所有文章加起来的访问量有多少，可以这么做 ：
  ```
  from django db.models import Sum
  Post.objects aggregate(all_pv=Sum ('pv'))
  ```

  ＃输出类似结采： { 'all_pv’: 487)
  上面演示了 QuerySet 的 annotate 和 aggregate 的用法，其中

  前者用来给 QuerySet 结果增加属性 ， 后者只用来直接计算结果 ，

   这些聚合表达式都可以与它们结合使用 。
  除了 Count 和 Sum 外 ，还有 Avg 、 Min 和 Max 等表达式， 均用来满足我们对 SQL 查询的需求 。  



### on_delete的参数意义

```
on_delete=None,               # 删除关联表中的数据时,当前表与其关联的field的行为
on_delete=models.CASCADE,     # 删除关联数据,与之关联也删除
on_delete=models.DO_NOTHING,  # 删除关联数据,什么也不做
on_delete=models.PROTECT,     # 删除关联数据,引发错误ProtectedError
# models.ForeignKey('关联表', on_delete=models.SET_NULL, blank=True, null=True)
on_delete=models.SET_NULL,    # 删除关联数据,与之关联的值设置为null（前提FK字段需要设置为可空,一对一同理）
# models.ForeignKey('关联表', on_delete=models.SET_DEFAULT, default='默认值')
on_delete=models.SET_DEFAULT, # 删除关联数据,与之关联的值设置为默认值（前提FK字段需要设置默认值,一对一同理）
on_delete=models.SET,         # 删除关联数据,
 a. 与之关联的值设置为指定值,设置：models.SET(值)
 b. 与之关联的值设置为可执行对象的返回值,设置：models.SET(可执行对象)
————————————————
版权声明：本文为CSDN博主「buxianghejiu」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/buxianghejiu/article/details/79086011
```

### 外键的设置

```
related_name： related_name将是相关对象的属性，它允许您“向后”到带有外键的模型。例如，如果ModelA有这样的字段：
author = models.ForeignKey(Author, verbose_name=‘作者’，related_name=‘bs’)，这将使您能够通过 bs 访问与您的 Author 实例相关的实例bs_instance.model_as.all(), 但是需要注意一点的是这一般是用复数表示外键， 因为外键是一对多的关系， 该方式其实是声明外键字段的模型， 如果没有声明则是表明加下划线_set, 如：author_set。
related_query_name： related_query_name用于 Django 查询集。它允许您过滤外键相关字段的反向关系。如：有一个字段 author = models.ForeignKey(Author, verbose_name=‘作者’, related_name=‘bs’, related_query_name=‘b’) 将使您能够b在查询集中用作查找参数，例如：Author.objects.filter(b__name=‘whatever’)。它将使用单数形式 related_query_name。
原文链接：https://blog.csdn.net/qq_39253370/article/details/119984347
```











