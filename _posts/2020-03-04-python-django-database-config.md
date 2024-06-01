---
title: "Django 数据库常用字段类型、选项参数、外键约束"
categories:
  - Python
tags:
  - django
  - database
  - config
last_modified_at: 2020-03-04T19:10:03+08:00
---
  
#### 字段类型

| 类型               | 说明                                                                                                                                                           |
|------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------|
| AutoField        | 自增的IntegerField，通常不用指定，不指定时Django会自动创建属性名为id的自增属性                                                                                                            |
| BooleanField     | 布尔字段，值为True或False                                                                                                                                            |
| NullBooleanField | 支持Null、True、False三种值                                                                                                                                         |
| CharField        | 字符串，参数`max_length`表示最大字符个数                                                                                                                                   |
| TextField        | 大文本字段，一般超过4000个字符时使用                                                                                                                                         |
| IntergerField    | 整数                                                                                                                                                           |
| DecimalField     | 十进制浮点数，参数`max_digits`表示总位数，参数`decimal_places`表示小数位数                                                                                                          |
| FloatField       | 浮点数                                                                                                                                                          |
| DataField        | 日期，参数`auto_now `表示每次保存对象时，自动设置该字段为当前时间，用于“最后一次修改”的时间戳，它总是使用当前时间，默认为False；<br />参数`auto_now_add`表示当对象第一次被创建时自动设置当前时间，用于创建的时间戳，它总是使用当前时间，默认为False；<br />两个参数互斥 |
| TimeField        | 时间，参数同上                                                                                                                                                      |
| DataTimeField    | 日期时间，参数同上                                                                                                                                                    |
| FileField        | 上传文件字段                                                                                                                                                       |
| ImageField       | 继承于FileField，对上传的内容进行校验，确保是有效的图片                                                                                                                             |

#### 字段选项

| 类型          | 说明                                                |
|-------------|---------------------------------------------------|
| null        | 如果为True，表示值允许为空，默认为False                          |
| blank       | 如果为True,表示值允许为空白，默认为False                         |
| db_column   | 字段的的名称，如果未指定，则使用属性的名                              |
| db_index    | 如果为True,则在表中会为此字段创建索引，默认为False                    |
| default     | 默认值                                               |
| primary_key | 若为True，则该字段会成为模型的主键字段，默认为False，一般作为AutoField的选项使用 |
| unique      | 如果为True，这个字段值必须唯一，默认为False                        |

#### 外键

在设置外键时，需要通过on_delete选项指明主表删除数据时，对于外键引用表数据如何处理，在django.db.models中包含了可选常量：

- **CASCADE**    级联，删除主表数据时，连同外键表中数据一起删除
- **PROTECT**    保护，通过抛出ProtectedError异常，来阻止删除主表中被外键应用的数据
- **SET_NULL**    设置为NULL，删除主表时，将外键引用的字段设置为null，仅当null=True时可用
- **SET_DEFAULT**    设置为默认值，仅在该字段设置了默认值时可用
- **SET()**    设置为特定值或调用特定方法
- **DO_NOTHING**    不做任何操作，如果数据库前置指明级联性，此选项会抛出IntergrityError异常