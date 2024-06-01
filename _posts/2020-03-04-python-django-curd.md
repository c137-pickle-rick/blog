---
title: "Django 数据库增删改"
categories:
  - Python
tags:
  - django
  - database
  - create
  - update
  - delete
last_modified_at: 2020-03-04T19:19:03+08:00
---
  
**人物表info**

| 字段          | 说明           |
|-------------|--------------|
| id          | 主键自增         |
| name        | 不能为null      |
| age         | default为0    |
| update_time | 更新为最后一次修改的时间 |

#### 新增数据

**第一种：通过实例化模型类，调用save方法**

```python
# views.py中的模型类
# 给info表新增一条name为Miles，age为18的数据(注意如果指定已存在的id字段，会覆盖那一条数据)
Info(name='Miles', age=18).save()
```

**第二种：通过objects的create方法**

```python
# views.py中的模型类
# 给info表新增一条name为Miles，age为18的数据
Info.objects.create(name='Miles', age=18)
```

#### 修改数据

**第一种：通过objects的get方法取得一条数据对象，修改并save保存**

```python
# 获得name为Miles，age为18的数据对象，注意不能获得多条数据
data = Info.objects.get(name='Miles', age=18)
# name字段改为Mary，age字段改为20
data.name = 'Mary'
data.age = 20
# 保存修改
data.save()
```

**第二种：通过objects的filter方法获得多条数据，使用update方法批量修改**

```python
# 将所有20岁的数据，name改为abc，age改为100，返回修改的条数
Info.objects.filter(age=20).update(name='abc', age=100)
```

**第三种：通过objects的all方法获得所有数据，使用update方法批量修改**

```python
# 将所有人的年龄都改为99
Info.objects.all().update(age=99)
```

#### 删除数据

**第一种：通过objects的get方法取得一条数据对象，调用delete方法删除**

```python
# 删除id为1的数据
Info.objects.get(id=1).delete()
```

**第二种：通过objects的filter方法获得多条数据，使用delete方法批量删除**

```python
# 删除所有age为20的数据
Info.objects.filter(age=20).delete()
```

**第三种：通过objects的all方法获得所有数据，使用delete方法批量删除**

```python
# 删除表中所有数据
Info.objects.all().delete()
```