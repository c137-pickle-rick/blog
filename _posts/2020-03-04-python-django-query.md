---
title: "Django 数据库基本查询方法"
categories:
  - Python
tags:
  - django
  - database
  - query
last_modified_at: 2020-03-04T20:28:03+08:00
---
  
**人物表info**

| 字段          | 说明           |
|-------------|--------------|
| id          | 主键自增         |
| name        | 不能为null      |
| age         | default为0    |
| update_time | 更新为最后一次修改的时间 |

#### 基础查询方法

- **get**  查询单一结果，模型类实例，如果不存在会抛出模型类 DoesNotExist 异常
- **filter**  过滤出多个结果，返回 QuerySet 类型对象
- **exclude**  排除掉符合条件剩下的结果，返回 QuerySet 类型对象
- **all**  查询所有结果，返回 QuerySet 类型对象
- **count**  查询结果数量

#### 过滤条件

表达语法如下：

​	`属性名称__运算符=值`

| 语法                                     | 条件                    |
|----------------------------------------|-----------------------|
| id__exact=3    (省略写法: id=3)            | 查询id=3的数据             |
| name__contains='e'                     | 查询name包含e的数据          |
| name__startswith='M'                   | 查询name以M开头的数据         |
| name__endswith='s'                     | 查询name以s结尾的数据         |
| date__isnull=True                      | 查询date为空的数据           |
| id__in=[1,2,3]                         | 查询id为1或2或3的数据         |
| id__gt=3    (gt: greater than)         | 查询id大于3的数据            |
| id__gte=3    (gte: greater than equal) | 查询id大于等于3的数据          |
| id__lt=2    (lt: less than)            | 查询id小于2的数据            |
| id__lte=2    (lte: less than equal)    | 查询id小于等于2的数据          |
| date__month=2                          | 查询日期为二月的数据            |
| date__gt = '1999-01-01'                | 查询date1999-01-01之后的数据 |

#### F对象

用于属性间对比，以及一些算术运算，语法规则如下：

​	`F('属性名')`		# F参为属性名，即表的字段

```python
from demo.models import Info
from django.db.models import F

# 查询条件：id大于2倍age的数据
Info.objects.filter(id__gt=F('age')*2)
```

#### Q对象

用于逻辑运算，与&、或|、非~ ，语法规则如下：

​	`Q(过滤条件)`

```python
from demo.models import Info
from django.db.models import Q

# 查询id大于3且age小于20的数据
(id__gt=3, age_lt=20)	# 基础写法
Q(id__gt=3) & Q(age_lt=20)
# 查询id大于3或age小于20的数据
Q(id__gt=3) | Q(age_lt=20)
# 查询age不等于20的数据
~Q(age=20)
```

#### 聚合函数

QuerySet 和 Model.objects 都有 aggregate() 函数，可以进行统计计算

- aggregate()的参数是django.db.models.Aggregate 类型的对象
- 返回值是字典，包含聚合计算后的结果
- 格式是`{'属性名_聚合类型小写': 值}`，比如 `{'name_sum': 10}`

Aggregate 类型的子类：

- **Avg** 平均值
- **Count** 数量
- **Max** 最大值
- **Min** 最小值
- **Sum** 求和
- **StdDev** 标准差
- **Variance** 方差

```python
from demo.models import Info
from django.db.models import Avg, Count, Sum

# 查询id大于2的人的数量和年龄的总和
Info.objects.filter(id__gt=2).aggregate(Count('id'), Sum('age'))
# 返回值： {'id_count':20, 'age_sum': 300}

# 查询年龄不等于20岁的人的平均年龄
Info.objects.exclude(age=20).aggregate(Avg('age'))
# 返回值： {'age_avg': 20}
```

> 注：计算count时一般不使用aggregate()，对于QuerySet类型可直接使用count()，返回值是一个数字

```python
# 查询年龄大于20岁的人数
Info.objects.filter(age__gt=20).count()
```

#### 排序函数

QuerySet 和 objects 都有 order_by()方法对查询结果进行排序

```python
# 对id字段升序
Info.objects.all().order_by('id')
# 对id字段降序，前加个 -
Info.objects.all().order_by('-id')
```

#### 关联查询

两个模型类，对应数据库中一个主表一个从表

**班级模型类ClassTable**

| 属性   | 说明   |
|------|------|
| id   | 主键自增 |
| name | 班名   |

**学生模型类StudentTable**

| 属性   | 说明       |
|------|----------|
| id   | 主键自增     |
| name | 学生名      |
| age  | 学生年龄     |
| clas | 外键，关联班级类 |

##### 主对从的查询

语法： 主的模型类对象.从的模型类小写_set

```python
# 查询班名为一班的所有学生信息
ClassTale.objects.get(class='一班').studenttable_set.all()
```

##### 从对主的查询

语法： 从的模型类对象.所关联主类的属性名

```python
# 查询id为20的学生所属的班级信息
StudentTable.objects.get(id=20).clas
# 查询id为20的学生所属的班级id
StudentTable.objects.get(id=20).clas_id
```

#### 关联过滤查询

##### 由从的模型类条件查询主模型类的数据

语法： 从的模型类名小写\_\_属性名\_\_条件运算符=值

> 注： 如果没有"__运算符"部分，表示等于。
>
> 如果一个主类的多个从类都满足条件，会返回多个该主类

```python
# 查询学生姓名中包含'张三'的班级信息
ClassTable.objects.filter(studenttable__name__contains='张三')
```

##### 由主的模型类条件查询从模型类的数据

语法： 从的模型类的关联属性\_\_主模型类的属性名\_\_条件运算符=值

> 注： 如果没有"__运算符"部分，表示等于。

```python
# 查询班级名包含'一班'的所有学生信息
StudentTable.objects.filter(clas__name__contains='一班')
```