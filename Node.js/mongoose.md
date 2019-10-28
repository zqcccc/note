# pipeline

如果你了解`管道`的概念，你可以跳过这一段话。如果你不了解，我在这里打个比方，比如生活中的水管，水(也就是我们的数据源)源源不断的从一节(一个管道)流向另一节(另一个管道)；如果对某一节(一个管道)的水做了一些处理(也就是数据的筛选，排序等)，那么在下一节(另一个管道)接收到的水就是你处理后的，当然，你也可以再次处理，如此反复...最后流到你家的就是经过层层处理的水(也就是我们需要得到的数据)。

而Mongoose的`聚合函数`的原理就是这样，后一个管道得到的数据是上一个管道处理后的数据...。

**管道是可重复的。**

## 表达式

表达式很简单，你可以理解为计算。在Mongoose中主要处理输入文档并输出。表达式是无状态的，只能用于计算当前聚合管道的文档，不能处理其它的文档。

下面开始学习聚合函数。

语法：

```
db.COLLECTION_NAME.aggregate(OPERATION， CALLBACK)
```

OPERATION: Object | Array，可选

CALLBACK: 可选

## 管道

- `$project`：修改输入文档的结构。可以用来重命名、增加或删除域，也可以用于创建计算结果以及嵌套文档。对应`project()`方法
- `$match`：用于过滤数据，只输出符合条件的文档。$match使用MongoDB的标准查询操作。对应`match()`。
- `$limit`：用来限制MongoDB聚合管道返回的文档数。对应`limit()`方法
- `$skip`：在聚合管道中跳过指定数量的文档，并返回余下的文档。对应`skip()`。
- `$unwind`：将文档中的某一个数组类型字段拆分成多条，每条包含数组中的一个值。对应`unwind()`方法
- `$group`：将集合中的文档分组，可用于统计结果。对应`group()`方法
- `$sort`：将输入文档排序后输出。对应`sort()`方法
- `$geoNear`：输出接近某一地理位置的有序文档。对应`near()`。

**v3.2**

- `$sample`：随机选择N个
- `$lookup`：连接操作符，用于连接同一个数据库中另一个集合，并获取指定的文档，类似于populate

更多管道操作符：<https://docs.mongodb.com/manual/reference/operator/aggregation-pipeline/>



对于`$group`的表达式：

- `$sum` 计算总和。
- `$avg` 计算平均值 
- `$min` 获取集合中所有文档对应值得最小值。
- `$max` 获取集合中所有文档对应值得最大值。
- `$push` 在结果文档中插入值到一个数组中。
- `$addToSet` 在结果文档中插入值到一个数组中，但不创建副本。
- `$first` 根据资源文档的排序获取第一个文档数据。
- `$last` 根据资源文档的排序获取最后一个文档数据



更多表达式：<https://docs.mongodb.com/manual/meta/aggregation-quick-reference/#aggregation-expressions>

#### 在mongoose 中aggregate中match id 时出现错误的解决办法:

```js
Product.aggregate(
    {"$unwind": "$products"},
 // 执行成功
    {"$match": {"products.price": 9}},
 // 执行失败
    {"$match": {"products._id": "58b77e509219010a6eac48ed"}},
 // 下边方法成功
    {"$match": {"products._id": new mongoose.Types.ObjectId('58b77e509219010a6eac48ed')}}, 
    function(err, result){
      if (err) {
        console.log(err);
      }else {
        res.send(result);
      }
    }
  );
```

**mongoose**中有`两个地方`定义了`ObjectId`，`mongoose.schema.Types.ObjectId`和`mongoose.Types.ObjectId`，在这里只有**mongoose.Types.ObjectId**才起作用。