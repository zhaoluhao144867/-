

# 安装

```shell
sudo docker run -itd --name mongo -p 27017:27017 registry.cn-hangzhou.aliyuncs.com/ykd_project/mongo:7.0.12
```

终端查询结果

```shell
mongo 192.168.0.1:27017/practice index.js
```

进入

```shell
sudo docker exec -it mongo mongosh admin
```

验证是否成功

```shell
sudo docker exec -it mongo mongosh admin
```

创建和切换数据库

```shell
use practice
```

创建表

```
db.createCollection("TableName")
```

查看创建好的表

```
show collections
```

删除表

```
db.表名.drop()
```

# 文档操作

## 插入

插入数据

```js
db.collection.save(
   <document>
)
collection指的是表名
```

```js
db.ykd_product.save({
  _id: '1',
  name: '夏季气质碎花裙子夏装',
  description: '2020夏天连衣裙',
  crossedPrice: 699,
  currentPrice: 198,
  productTag: ['NEW', 'WOMEN'],
  productType: 'CLOTHING',
  gmtCreated: ISODate('2020-05-12T22:50:43.744+08:00'),
  gmtModified: ISODate('2020-05-12T22:50:43.744+08:00'),
});
```

批量插入

```
db.ykd_product.save(
{},
{}
);
```

查询数据

```js
db.ykd_product.find({});
```

```shell
mongo 192.168.0.1:27017/practice --eval 'db.ykd_user_info.find({});'
```

主键:如果不指定主键，类型是ObjectId类型，指定是String类型

覆盖式插入：后面插入的实现更新，类似redis

```js
{
  _id: '1',
  name: '夏季气质碎花裙子夏装',
  description: '2020夏天连衣裙',
  crossedPrice: 699,
  currentPrice: 198,
  productTag: ['NEW', 'WOMEN'],
  productType: 'CLOTHING',
  gmtCreated: ISODate('2020-05-12T22:50:43.744+08:00'),
  gmtModified: ISODate('2020-05-12T22:50:43.744+08:00')
};

{
  _id: '1',
  name: '夏季气质碎花裙子夏装',
  crossedPrice: 1299,
  currentPrice: 699
};
```

## 更新

```js
db.ykd_product.update(
  {
    name: '杰克琼斯运动寸衫',
  },
  {
    $set: {
      crossedPrice: 999,
      currentPrice: 599,
    },
  }
);
```

如果值不存在，将upsert的值设为true

```js
db.ykd_product.update(
  {
    name: '最新潮流帆布鞋',
  },
  {
    $set: {
      crossedPrice: 999,
      currentPrice: 599,
    },
  },
  true
);
```

## 删除

```
db.表名.remove({})大括号是删除条件
```

# 文档查询

## 比较运算符

```js
db.ykd_product.find({
  productType: 'SHOE',查询条件
  crossedPrice: { $gt: 1000 },大于1000
});

db.ykd_product.find({
  productType: 'SHOE',
  crossedPrice: { $gte: 1000 },大于等于
});

db.ykd_product.find({
  crossedPrice: { $in: [1299, 1599, 1199] },查询特定价格，数组
});


$eq: equal 缩写，表示和条件值相等。
$ne: not equal 缩写，表示和条件值不相等或者不存在。
$gt: greater than 缩写，表示大于条件值。
$gte: greater than or equal 缩写，表示大于或等于条件值。
$lt: less than 缩写，表示小于条件值。
$lte: less than or equal 缩写，表示小于或等于条件值。
$in: 表示筛选出字段在数组中。
$nin: not in 缩写，表示筛选出字段不在数组中。
```

## 逻辑操作符

```js
db.ykd_product.find({
  $and: [
    { productType: 'SHOE' },
    {
      currentPrice: 999,
    },
  ],
});
```

```js
不等于999
db.ykd_product.find({
  currentPrice: {
    $not: {
      $eq: 999,
    },
  },
});

$and: 用于连接多个查询条件，表示查询的文档必须符合所有条件。
$nor: 用于连接多个查询条件，表示查询的文档必须不符合所有条件。
$or: 用于连接多个查询条件，表示查询的文档只满足其中一个条件即可。
$not: 只可用于一个查询条件，表示查询的文档必须不符合该条件。
```

## limit操作符

```
db.ykd_user_info.find({}).limit(4);
```

## 正则表达式

```js
db.ykd_product.find({
  name: {
    $regex: '运动鞋',
  },
});
```

## 内嵌文档查询

```
db.ykd_order.save([
  {
    _id: 'YKD_0001',
    payAmount: 299,
    payNickName: '致远之道',
    product: {
      productName: '海澜之家夏季最新款寸衫',
      currentPrice: 198.0,
      productTag: ['NEW', 'WOMEN'],
      productType: 'CLOTHING',
    },
    gmtCreated: new Date(),
    gmtModified: new Date(),
  },
  {
    _id: 'YKD_0002',
    payAmount: 799,
    payNickName: '远大之恋',
    product: {
      productName: '安踏最新运动款跑步鞋',
      currentPrice: 799.0,
      productTag: ['NEW'],
      productType: 'SHOE',
    },
    gmtCreated: new Date(),
    gmtModified: new Date(),
  }
]);

```

```js
db.ykd_order.find({
  'product.productName': '海澜之家夏季最新款寸衫',
  'product.productType': 'CLOTHING',
});
```

# 性能优化

## 单字段索引

索引：快速定位，打标签

```sql
// 按age字段创建升序索引(createIndex为创建索引
//参数为（1或-1）1代表按年龄升序排序，-1代表降序)
db.ykd_bank_accounts.createIndex( {age: 1} )
```

```js
db.ykd_bank_accounts.find( {creditCard: false} )
```

查看索引

```sql
db.ykd_bank_accounts.getIndexes()
```

删除索引

创建索引时默认名称是age_1

```sql
db.ykd_bank_accounts.dropIndex("name_1_age_1")
db.ykd_bank_accounts.dropIndex( { name: 1, age: 1 } )
```

## 复合索引

```sql
db.ykd_bank_accounts.createIndex( {age: 1, city: 1} )
```

创建复合索引时，查询会优先查询第一个索引，根据实际情况创建索引

## 全文本索引

创建全文本索引，一个文本只存在一个

1.Mongodb 的写入速度会变慢
2.一个文档最多只能创建一个全文本索引(该索引可以包含多
个字段)
3.在使用全文本索引查找时，查找条件是不区分大小写的
4.全文本索引可以设置每个字段的査找权重

```sql
//创建content字段的全文本索引
db.ykd_blog.createIndex( {'content': 'text'} )
//查询索引
db.ykd_bank_accounts.getIndexes()
```

```sql
db.ykd_blog.find( {$text:{$search:'国家'}} )查询
```

```sql
//删除之前创建的全文本索引,不删会报错
db.ykd_blog.dropIndex( "content_text" )
//建立新的全文本索引，多个字段
db.ykd_blog.createIndex({
    "content": "text", "name": "text"
})
```

创建多字段全文本索引

```sql
db.ykd_blog.dropIndex( "content_text" )
```

设置权重，2>1先查询name

```sql
db.ykd_blog.createIndex(
    {
        "content": "text", "name": "text"
    },
    {
        "weights": { "content": 1, "name": 2 }
    }
)
```

## 查询诊断优化

判断索引是否被启用

使用explain()

```sql
db.ykd_bank_accounts.find(
    {
        age: { $lt: 45 }
    }
).explain('executionStats')
```

```js
{
    "queryPlanner": {
        "namespace": "draftdb.ykd_bank_accounts",//代表的是该查询所扫描的表
        "indexFilterSet": false,//表示MongoDB在查询中是否使用索引过滤

    "executionStats": {
        "executionSuccess": true,
        "nReturned": 6330,//返回当前查询的文档数量
        "executionTimeMillis": 39,//整体执行的时间
        "totalKeysExamined": 6330,//索引扫描的次数
        "totalDocsExamined": 6330,//document扫描次数
        "executionStages": {
            "stage": "FETCH",
            ------------------
            "nReturned": 6330,//返回当前查询的文档数量
             "indexName": "age_1",//使用的索引的名字,
              "inputStage": {
                "stage": "IXSCAN",//stage的子 stage，此处是IXSCAN，表示进行的是索引扫描
                -------------------
                }
            }
        }
    },

    "ok": 1
}

stage的值与描述

COLLSCAN：全表扫描--------------表示没有优化
IXSCAN*：索引扫描
FETCH*：根据索引去检索指定 document
SHARD_MERGE：将各个分片返回数据进行 merge
SORT：在内存中进行排序------------表示没有优化
LIMIT：使用 limit 限制返回数
SKIP：使用 skip 进行跳过
TEXT：全文索引进行查询
PROJECTION：限定返回字段
IDHACK：针对 _id 进行查询
```

查看磁盘空间占用情况，mongodb删除数据不释放空间

```sql
db.serverStatus().mem
```

```json
{
  "bits": 64,
  "resident": 485,
  "virtual": 1860,
  "supported": true
}

resident 代表物理内存使用情况单位为 M
virtual 代表虚拟内存使用情况单位为 M
```

释放空间命令

```sql
db.runCommand({closeAllDatabases:1})
```

# 地理空间数据介绍

定位功能开发

我们拿到这些数据(某个地方的经纬度)怎么用到 mongodb 中的地理空间数据呢
mongodb 为存储地理数据提供了特殊的存书格式(GeoJSON 对
象，和传统坐标对)



###  GeoJSON 对象

要计算球体上的几何形状，我们要将数据存储为 GeoJSON 对象

指定GeoJSON 对象

```js
location: {
      type: "Point",
      coordinates: [120.212599, 30.290846]经度、纬度（范围）
}
经度的正数表示东经，负数表示西经:纬度的正数表示北纬，负数表示南纬。
```

- `Point`：coordinates 字段只有一个坐标
- `LineString`：coordinates 字段有两个坐标
- `Polygon`：coordinates 字段会有两个以上坐标

1.Point(点对点，只需要一个坐标就可以定位(经纬度))
2.Linestring(线 需要两个坐标来确定位置(两队经纬度))
3.Polygon(多边形 需要多个坐标来确定位置)

### 传统坐标

二维平面，只需要一个字段

1.通过数组指定

```js
location: {
      [<经度>, <纬度> ]
}
```

2.通过嵌入式文档指定

```js
location: {
     <field1>: <经度>, <field2>: <纬度>
}
```

### 2dsphere 索引

2dsphere 索引支持球面上的几何计算的查询。2dsphere 索引支持所
有 MongoDB 地理空间查询 :包含查询(在一个指定多边形内的位置
进行查询)、 交集查询(查询指定几何相交的位置)和临近查询(如查询
离另一个点最近的点)。地理空间查询操作
创建 2dsphere 索引必须存储几何坐标对形式的数据或 GeoJSON 数据



创建2dsphere 索引的语法

```sql
db.表名.createIndex({location:"2dsphere"})
```

查询

```sql
db.表名.getIndexes()
```

### 应用

#### 1.初始化数据(从高德地图爬取的数据)GeoJSON 文档

```js
db.ykd_gd_map.save({"_id":ObjectId("5ec8d53e6b9e7a03e87c60d2"),"name":"杭州东站","address":"杭州","location":{"type":"Point","coordinates":[120.212599,30.290846]}});
db.ykd_gd_map.save({"_id":ObjectId("5ec8d57a6b9e7a03e87c60d3"),"name":"西广场","address":"杭州","location":{"type":"Point","coordinates":[120.210411,30.289484]}});
db.ykd_gd_map.save({"_id":ObjectId("5ec8d5916b9e7a03e87c60d4"),"name":"派出所","address":"杭州","location":{"type":"Point","coordinates":[120.212331,30.288854]}});
db.ykd_gd_map.save({"_id":ObjectId("5ec8d5a86b9e7a03e87c60d5"),"name":"锦江都城酒店","address":"杭州","location":{"type":"Point","coordinates":[120.217932,30.290124]}});
db.ykd_gd_map.save({"_id":ObjectId("5ec8d5c06b9e7a03e87c60d6"),"name":"新凤尚街","address":"杭州","location":{"type":"Point","coordinates":[120.209794,30.291439]}});
db.ykd_gd_map.save({"_id":ObjectId("5ec8d5e76b9e7a03e87c60d7"),"name":"东赞球馆","address":"杭州","location":{"type":"Point","coordinates":[120.216885,30.291624]}});
db.ykd_gd_map.save({"_id":ObjectId("5ec8d6036b9e7a03e87c60d8"),"name":"职工文化中心","address":"杭州","location":{"type":"Point","coordinates":[120.21886,30.287669]}});
db.ykd_gd_map.save({"_id":ObjectId("5ec8d62b6b9e7a03e87c60d9"),"name":"维也纳国际酒店","address":"杭州","location":{"type":"Point","coordinates":[120.21739,30.290682]}});
db.ykd_gd_map.save({"_id":ObjectId("5ec8d6446b9e7a03e87c60da"),"name":"中豪国际大酒店","address":"杭州","location":{"type":"Point","coordinates":[120.223076,30.289848]}});
db.ykd_gd_map.save({"_id":ObjectId("5ec8d65f6b9e7a03e87c60db"),"name":"东汇大厦","address":"杭州","location":{"type":"Point","coordinates":[120.22048,30.293165]}});
db.ykd_gd_map.save({"_id":ObjectId("5ec8d67e6b9e7a03e87c60dc"),"name":"新国利花园酒店","address":"杭州","location":{"type":"Point","coordinates":[120.219879,30.284697]}});
db.ykd_gd_map.save({"_id":ObjectId("5ec8d69a6b9e7a03e87c60dd"),"name":"钱威商务楼","address":"杭州","location":{"type":"Point","coordinates":[120.215222,30.281306]}});
db.ykd_gd_map.save({"_id":ObjectId("5ec8d6c56b9e7a03e87c60de"),"name":"红街天城","address":"杭州","location":{"type":"Point","coordinates":[120.20812,30.292275]}});
db.ykd_gd_map.save({"_id":ObjectId("5ec8d6dd6b9e7a03e87c60df"),"name":"迈达商业中心","address":"杭州","location":{"type":"Point","coordinates":[120.206897,30.2897]}});
db.ykd_gd_map.save({"_id":ObjectId("5ec8d6ef6b9e7a03e87c60e0"),"name":"如家酒店","address":"杭州","location":{"type":"Point","coordinates":[120.203077,30.286865]}});
db.ykd_gd_map.save({"_id":ObjectId("5ec8d7446b9e7a03e87c60e1"),"name":"东恒大厦","address":"杭州","location":{"type":"Point","coordinates":[120.203077,30.286865]}});
db.ykd_gd_map.save({"_id":ObjectId("5ec8d75f6b9e7a03e87c60e2"),"name":"太平洋银座","address":"杭州","location":{"type":"Point","coordinates":[120.211875,30.296499]}});
db.ykd_gd_map.save({"_id":ObjectId("5ec8d7756b9e7a03e87c60e3"),"name":"十足","address":"杭州","location":{"type":"Point","coordinates":[120.214697,30.294869]}});
db.ykd_gd_map.save({"_id":ObjectId("5ec8d7926b9e7a03e87c60e4"),"name":"中国移动","address":"杭州","location":{"type":"Point","coordinates":[120.206811,30.292433]}});
```

#### 2.在 GeoJSON 文档中创建 2dsphere 地理空间索引

```sql
// 创建索引
db.ykd_gd_map.createIndex({location:"2dsphere"})
//查询创建的索引
db.ykd_gd_map.getIndexes()
```

#### 3.查询杭州东站周围最少 100 米 最多 500000 米 以内的数据按从最近到最远的顺序排序

$near操作符，地理位置的查询的操作符之一

```sql
db.ykd_gd_map.find(
   {
     location:
       { $near:
          {
            $geometry: { type: "Point",  coordinates: [ 120.212599, 30.290846 ] },
            $minDistance: 100,最小距离
            $maxDistance: 500000最大距离
          }
       }
   }
)

$geometry：用于指定地理空间对象的类型和坐标。在这里，类型为"Point"表示指定的是一个点，坐标以经度和纬度的形式表示。
```

#### 4.查询杭州东站到东恒大厦的距离

```sql
db.ykd_gd_map.aggregate(这部分表示对名为 ykd_gd_map 的集合进行聚合操作
[
   {
      $geoNear: {这里定义了一个聚合管道阶段，使用 $geoNear 操作符进行地理位置附近的搜索。
         near: { type: "Point", coordinates: [ 120.212599, 30.290846 ] },定了搜索附近的地理位置，这里是经度和纬度坐         标为 [120.212599, 30.290846] 的点。
         spherical: true,表示启用了球面几何计算，用于处理地球表面的地理位置。
         query: { name: "东恒大厦" },通过这个参数，指定了额外的查询条件，只返回名称为 "东恒大厦" 的文档。
         distanceField: "dist.location"//这个参数可以计算两者之间的距离
      }
   }
]

)
```

# 数据聚合

聚合操作

1.单一的聚合命令

2.聚合管道

3.map-reduce 函数。

## 单一的聚合命令

count用法

count实现

```js
db.ykd_action_data.count({
  date: ISODate('2020-05-08T00:00:00.000+08:00'),
  process: { $gte: 100 }
});
```

find实现

```js
db.ykd_action_data
  .find({
    date: ISODate('2020-05-08T00:00:00.000+08:00'),
    process: { $gte: 100 }
  })
  .count();
```

distinct用法

先查询再去除重复的

```js
db.ykd_accounts.distinct('info.college', {
  'info.project': '电子与通信工程'
});
去除重复的学校
```

## 聚合管道





## 常用的聚合操作符

#### $sort   $skip   $limit

第一页数据

```js
db.ykd_action_data.aggregate([{ $skip: 0 }, { $limit: 5 }]);
```

第二页数据

```js
db.ykd_action_data.aggregate([{ $skip: 5 }, { $limit: 5 }]);
```

第三页数据

```js
db.ykd_action_data.aggregate([{ $skip: 10 }, { $limit: 5 }]);
```

降序排序第一页数据

```js
聚合管道

db.ykd_action_data.aggregate([
  { $sort: { data: -1 } },----------  1升序 -1降序
  { $skip: 0 },跳过多少个，从0开始
  { $limit: 5 }限制多少条数据
]);
```

#### $project

改变原来的文档数据，处理成需要的数据

```json
{ $project: { <specification(s)> } }
```

- `<字段名>: <1 或者 true>`：指定的字段显示

- `_id(主键，默认显示): <0 或者 false>`：隐藏 `_id` 字段

- `<字段名>: 表达式`：使用表达式可以重命名字段，或对其值进行操作

  

#### 加减乘除

`$add(+)`，`$subtract(-)`，`$multiply(*)`，`$divide(/)`， 四个运算符用法都差不多。代码格式为

![img](https://style.youkeda.com/newcoursep4/d3/aggregate_foo.svg)

具体代码实现为

```js
db.ykd_action_data.aggregate([
  {
    $project: {
      studyTime: { $subtract: ['$endTime', '$startTime'] },
      _id: 0,
      accountId: 1
    }
  }
]);
```

#### $match

```js
db.collection.find(<query>)
                   
{ $march : { <query> } }  两种用法一样
```

```js
db.ykd_action_data.aggregate([
  {
    $match: { date: ISODate('2020-05-08T00:00:00+08:00') }
  }
]);
```

某天学生学习的总时长

```js
db.ykd_action_data.aggregate([
  {
    $match: { date: ISODate('2020-05-08T00:00:00+08:00') }
  },
  {
    $project: {
      studyTime: { $subtract: ['$endTime', '$startTime'] },
      _id: 0,
      accountId: 1,
      date: 1
    }
  }
]);
```

#### $group的使用



![group_op2.svg](https://style.youkeda.com/newcoursep4/d3/group_op2.svg)

常用表达式操作符

- `$sum`：计算总和，`{$sum:1}`表示返回每组对应多少个数据，如果是`{$sum:'数据库字段'}`，则返回该字段的累加求和值
- `$avg`：求平均值
- `$min`：求最小值
- `$max`：求最大值
- `$push`：将结果放入到一个自定义的数组中
- `$first`：根据查询的文档顺序返回第一个数据
- `$last`：根据查询的文档顺序返回最后一个数据

和$max配合使用

```js
db.ykd_product.aggregate([
  {
    $group: {
      _id: '$productType',
      mostExpensivePrice: { $max: '$currentPrice' }
    }
  }
]);
```

![group_max.svg](https://style.youkeda.com/newcoursep4/d3/group_max.svg)

和$push配合使用

```js
db.ykd_product.aggregate([
  {
    $group: {
      _id: '$productType',
      names: { $push: '$name' }将指定字段的值添加到一个数组中$push
    }
  }
]);
```

![product.svg](https://style.youkeda.com/newcoursep4/d3/product.svg)

这里说明下:![img](https://style.youkeda.com/newcoursep4/d3/group4.svg)

#### $group 的_id 使用拼接字符串

```js
db.ykd_product.aggregate([
  { $sort: { currentPrice: 1 } },
  {
    $group: {
      _id: { $concat: ['$productType', 'TEST'] }, 语义化
      cheapest: { $first: '$currentPrice' }
    }
  }
]);
```

#### $group 的_id 使用多个属性

```js
db.ykd_action_data.aggregate([
  {
    $group: {
      _id: { date: '$date', accountId: '$accountId' },
      process: { $push: '$process' }
    }
  }
]);
```

#### $group 的_id 为null

id为null表示都在一个组里，

```js
db.ykd_product.aggregate([
  {
    $group: {
      _id: null,
      amount: { $sum: 1 }计算每个分组有多少数据
    }
  }
]);
等同于
db.collection.find().count()
```



 $sum 的用法，如果后面的参数跟的是表中的参数则会计算分组中这个字段值的总和，

例如查看商品分类中各个商品价格总和是多少

```js
db.ykd_product.aggregate([
  { $group: { _id: '$productType', total: { $sum: '$currentPrice' } } }
]);

//大家可以尝试把 $sum 换成 $avg 表示求当前分类价格的平均值（记得将 total 字段名也换掉哦，只要是表示平均值就行了）
```

#### $count的使用

聚合实现价格大于200的商品

```js
db.ykd_product.aggregate([
  {
    $match: { currentPrice: { $gt: 200 } }
  },
  { $count: 'number' }number存储结果
]);
//number为自定义变量，自己可以自定义


相当于db.ykd_product.count({ currentPrice: { $gt: 200 } });
```

#### 多表关联查询

##### $lookup

用法

```js
{
   $lookup:
     {
       from: <被关联的表>,   //右集合
       localField: <当前表的关联属性>,  //左集合 join字段
       foreignField: <被关联表的关联属性>, //右集合 join字段
       as: <输出属性名>   //新生成字段（类型array）
     }
}
```

| $lookup 属性 | 对应待查表的属性          |
| ------------ | ------------------------- |
| from         | 'ykd_accounts'            |
| localField   | 'accountId'               |
| foreignField | '_id'                     |
| as           | 'account'(可以自己自定义) |

两张表关联后的结果为：

![img](https://style.youkeda.com/newcoursep4/d3/action_account.svg)

具体代码为：

```js
db.ykd_action_data.aggregate([
  {
    $lookup: {
      from: 'ykd_accouts',
      localField: 'accountId',
      foreignField: '_id',
      as: 'account'
    }
  }
]);
```





##### $unwind

将数组分片

![watermark,image_d2F0ZXJtYXNrLnBuZz94LW9zcy1wcm9jZXNzPWltYWdlL3Jlc2l6ZSx3XzEwMA==,t_60,g_se,x_10,y_10](https://style.youkeda.com/newcoursep4/d3/unwind_account.png?x-oss-process=image/resize,w_800/watermark,image_d2F0ZXJtYXNrLnBuZz94LW9zcy1wcm9jZXNzPWltYWdlL3Jlc2l6ZSx3XzEwMA==,t_60,g_se,x_10,y_10)

```js
db.ykd_action_data.aggregate([
  {
    $lookup: {
      from: 'ykd_accounts',
      localField: 'accountId',
      foreignField: '_id',
      as: 'account'
    }
  },
  { $unwind: '$account' }
]);
```

# MapReduce

MapReduce 是一种分布式的数据处理方式，以一种可靠的方式 并发
处理 TB 级别的数据，用来解决海量数据的计算问题。
MapReduce 的工作过程

1. MapReduce 是由 Map 和 Reduce 组成。
2. Map 负责把数据集进行切片
3. Reduce 负责把切片的数据汇总、计算

# MapReduce在MongoDB中的应用

MapReduce基本语法

```js
db.collection.mapReduce(
   function() {emit(key,value);}, //map 函数
   function(key,values) {return reduceFunction}, //reduce 函数
   {
      out: <输出的临时表>,
      query: <查询条件>,
      sort: <排序条件>,
      limit: <限制数量>,
      finalize: <最后执行的函数>
   }
)
```

**参数说明：**

| 常用表达式操作符 | 简述                                                         |
| ---------------- | ------------------------------------------------------------ |
| map              | JavaScript 函数，将输入的文档通过 key 进行分组，生成键值对，是 reduce 函数的入参 |
| reduce           | JavaScript 函数，对 map 的输出结果进行操作（将 key-values 中的 values 做操作）。 **注意 reduce 在当前 v4.2.6 不能返回数组** |
| out              | 统计结果存放集合（不指定的话会存储在临时表中，与客户端断开连接后数据会丢失） |
| query            | 查询条件（**执行在 map 函数之前**，可以不写）                |
| sort             | 根据查询的文档顺序返回最后一个数据 （**执行在 map 函数之前**，可以不写） |
| limit            | 限制向 map 函数输入值得数量 （**执行在 map 函数之前**，可以不写） |
| finalize         | 可以在 reduce 函数数组结果进一步修改 (可以不写）)            |

```js
db.ykd_action_data.mapReduce(
  function() {
    //this代表数据库中单个文档
    emit(this.accountId, { startTime: this.startTime, endTime: this.endTime });
  },
  //下面的value就相当于上图中的duration字段
  function(key, value) {
    let total = 0;
    for (let i = 0; i < value.length; i++) {
      total += value[i].endTime - value[i].startTime;
    }
    return total;
  },
  {
    out: 'ykd_study_duration'
  }
);
```

返回数据如下图

![img](https://style.youkeda.com/newcoursep4/d3/map_reduce_back.png?x-oss-process=image/resize,w_800/watermark,image_d2F0ZXJtYXNrLnBuZz94LW9zcy1wcm9jZXNzPWltYWdlL3Jlc2l6ZSx3XzEwMA==,t_60,g_se,x_10,y_10)

下面是对比较重要的返回属性进行解释

| 返回属性   | 解释                                                         |
| ---------- | ------------------------------------------------------------ |
| result     | 存储处理结果的临时表格，当连接关闭时就自动删除了             |
| timeMillis | 执行花费时间，毫秒为单位                                     |
| input      | 满足条件被发送到 map 函数的文档个数                          |
| emit       | 结果集合中的文档个数                                         |
| ok         | 是否成功，成功为 1                                           |
| err        | 如果失败，这里可以有失败原因，不过从经验上来看，原因比较模糊，作用不大 |

现在我们来查询刚刚我们处理的数据，

```js
db.ykd_study_duration.find();
```

在最后加上find,一步到位

```js
db.ykd_action_data
  .mapReduce(
    function() {
      emit(this.accountId, {
        startTime: this.startTime,
        endTime: this.endTime
      });
    },
    function(key, value) {
      let total = 0;
      for (let i = 0; i < value.length; i++) {
        total += value[i].endTime - value[i].startTime;
      }
      return total;
    },
    {
      out: 'ykd_study_duration'
    }
  ).find();
```
