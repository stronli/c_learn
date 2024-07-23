# sqlite expert 教程

## 1、增、删、查、改

数据库的主要作用通常可以概括为四个操作：增（Create）、删（Delete）、查（Select）、改（Update）。

- **增（Create）**：

  创建新记录。

  例如，在用户数据库中添加一个新的用户账户。

- **删（Delete）**：

  删除现有记录。

  例如，从用户数据库中删除一个不再使用的账户。

- **查（Select）**：

  检索数据库中的数据。

  例如，从用户数据库中查询所有活跃的用户。

- **改（Update）**：

  修改现有记录。

  例如，更新用户数据库中某个用户的电子邮件地址。

- 

## 2、DataTypes

### 1、1SQLite支持的数据类型有很多，但他们总共可以分为以下几类:

- desc <table> //查看表结构 
- select * from <table> //查询所有更
- select , from table ;//查看指定列
- select distinct , from table ;//非重复查询
- insert into users(_id,username,password) select * from users;//复制
- select username from users where username like 'S%' ;//非重名字首字母为大写S的用户
- select username from users where username like '__S%' ;//非重名字第三个字母为大写S的用户
- select * from users where _id in(001,220,230);
- select * from user order by _id;//以id的顺序排列
- select * from user order by _id desc;//以id反的顺序排

### 2、常用Select语句

- desc <table> //查看表结构 
- select * from <table> //查询所有更
- select , from table ;//查看指定列
- select distinct , from table ;//非重复查询
- insert into users(_id,username,password) select * from users;//复制
- select username from users where username like 'S%' ;//非重名字首字母为大写S的用户
- select username from users where username like '__S%' ;//非重名字第三个字母为大写S的用户
- select * from users where _id in(001,220,230);
- select * from user order by _id;//以id的顺序排列
- select * from user order by _id desc;//以id反的顺序排

### 3、创建表流程

#### 1、create首先创建表格appointment

```
CREATE TABLE [appointment](
  [appointmentid] INTEGER PRIMARY KEY AUTOINCREMENT NOT NULL, 
  [name] TEXT, 
  [IDNumber] TEXT, 
  [phoneNumber] TEXT, 
  [appointmentDate] TEXT, 
  [roomNumber] TEXT);
```

创建了一个名为appointment的表

其中包含六个字段

- `appointmentid`：一个整数类型的字段，作为主键，并且设置为自动递增，不能为空。

主键（Primary Key）是关系型数据库中的一种约束，用于唯一标识表中的每一行记录。每个表只能有一个主键，并且主键的值必须是唯一的，不能重复。主键的作用是确保表中的数据是唯一的，并且可以快速定位到特定记录。

主键的特点包括：

1. **唯一性**：主键的值必须是唯一的，不能有重复的值。
2. **非空性**：主键字段不能为空，必须有值。
3. **常量性**：主键的值在表的生命周期内通常是不变的。

- `name`：一个文本类型的字段，用于存储预约者的姓名。
- `IDNumber`：一个文本类型的字段，用于存储预约者的身份证号码。
- `phoneNumber`：一个文本类型的字段，用于存储预约者的电话号码。
- `appointmentDate`：一个文本类型的字段，用于存储预约的日期。
- `roomNumber`：一个文本类型的字段，用于存储预约的房间号码。

#### 2、insert 增加用户

```
INSERT INTO appointment (name, IDNumber, phoneNumber, appointmentDate, roomNumber) VALUES ('张三', '123456789012345678', '13800138000', '2023-05-20', 'A101');
```

向上面的表中插入数据

#### 3、delete from删除数据

```
DELETE FROM appointment WHERE name = '张三';
```

#### 4、update修改数据

```
UPDATE appointment SET appointmentDate = '2023-05-21' WHERE name = '张三';
```

#### 5、select 查询数据

查询所有记录

```
SELECT * FROM appointment;
```

查询特定条件的记录，可以使用 `WHERE` 子句来指定条件

```
SELECT * FROM appointment WHERE name = '张三';
```

#### 5、内连接查询（INNER JOIN）

用于从两个表中选择数据，并基于两个表之间进行字段匹配

```
FROM   [customer]
       INNER JOIN [room] ON [customer].[roomNumber] = [room].[roomNumber];
```

```
CONSTRAINT "roomNumber" FOREIGN KEY ("roomNumber") REFERENCES "room" ("roomNumber")
```

在 `customer` 表中，任何 `roomNumber` 字段的值都必须在 `room` 表中存在。如果 `customer` 表中有一个 `roomNumber` 值不在 `room` 表中，那么这个约束将阻止对该行的插入，从而保持数据的一致性。