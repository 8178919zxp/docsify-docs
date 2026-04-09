# MySQL数据库_最终完整版

> 注意：`MySQL命令`不区分大小写！每一个命令结尾必须加**分号；**！！！
> 规范：表名、字段名建议用反引号 ``` 包裹，避免与关键字冲突。
> 
> 

---

## 一、数据库命令

### 创建数据库

```sql
CREATE DATABASE IF NOT EXISTS bookstore; -- 补充IF NOT EXISTS：避免重复创建报错
```

### 使用数据库

```sql
USE bookstore;
```

### 查询所有数据库

```sql
SHOW DATABASES;
```

### 修改数据库

```sql
ALTER DATABASE bookstore 
DEFAULT CHARACTER SET utf8mb4 -- 补充：utf8mb4兼容emoji，比latin1/utf8更常用
DEFAULT COLLATE utf8mb4_unicode_ci; -- 通用校对规则，适配中文/英文
```

### 删除数据库

```sql
DROP DATABASE IF EXISTS bookstore; -- 补充IF EXISTS：避免删除不存在的库报错
```

---

## 二、数据类型

### 数值类型（Numeric Types）

|类型|字节|说明|取值范围|使用场景|备注|
|---|---|---|---|---|---|
|⭐ TINYINT|1|很小的整数|-128 ~ 127（有符号）|状态值、布尔值|常用于 0/1；可加 UNSIGNED（0~255）|
|⭐ SMALLINT|2|小整数|-32768 ~ 32767|小范围计数|可加 UNSIGNED（0~65535）|
|⭐ INT / INTEGER|4|标准整数|-2147483648 ~ 2147483647|主键、自增 ID|最常用；UNSIGNED（0~4294967295）|
|⭐ BIGINT|8|大整数|-9e18 ~ 9e18|订单号、雪花 ID|自增主键推荐 BIGINT（避免 INT 不够用）|
|MEDIUMINT|3|中等整数|-8388608 ~ 8388607|较少用||
|⭐ FLOAT|4|单精度浮点|精度约 6-7 位|科学计算|有精度丢失，禁止用于金额|
|⭐ DOUBLE|8|双精度浮点|精度约 15-16 位|金融以外计算|仍有精度丢失|
|⭐ DECIMAL(M,D)|可变|精确小数（定点数）|精确存储|金额、价格|M = 总位数，D = 小数位；推荐 DECIMAL (10,2)|
|BIT|1 字节起|位字段|0/1（多位数支持 0~2^n-1）|标志位|BIT (1) 等价布尔值|

👉 **重点说明**：

- ⭐ 金额必须用 `DECIMAL`，禁止用 FLOAT/DOUBLE（精度丢失问题会导致金额错误）

- ⭐ `INT` 是最常用整数类型，但主键自增推荐 `BIGINT`（避免数据量增大后溢出）

- ⭐ `TINYINT` 常用作布尔（0/1），推荐加 `UNSIGNED` 限定范围

---

### 字符串类型（String Types）

|类型|最大长度|说明|使用场景|备注|
|---|---|---|---|---|
|⭐ CHAR(n)|255|固定长度字符串|性别、状态码、手机号|快但浪费空间；n<5 时推荐|
|⭐ VARCHAR(n)|65535|可变长度字符串|用户名、邮箱、地址|最常用；n 按实际需求设置（避免过大）|
|TINYTEXT|255B|短文本|简短描述|无长度指定，按实际存储|
|TEXT|64KB|文本|文章内容、备注|常用；查询速度略慢于 VARCHAR|
|MEDIUMTEXT|16MB|大文本|长文章、富文本|超过 64KB 时使用|
|LONGTEXT|4GB|超大文本|日志、全文内容|极少用，建议存文件系统|
|⭐ ENUM|列表|枚举值（单选）|性别、订单状态|限定值；扩展性差，新增值需改表|
|SET|集合|多选枚举|标签、权限|最多 64 个值；查询需用 FIND_IN_SET|
👉 **重点说明**：

- ⭐ `VARCHAR` 最常用（必须掌握），注意：InnoDB 引擎中，VARCHAR 最大有效长度受行大小限制（约 65535 字节）

- ⭐ `TEXT` 用于大文本，无法直接作为主键 / 索引（需指定前缀长度）

- ⭐ `ENUM` 适合固定选项（如性别：' 男 ',' 女 '），但建议用 TINYINT + 业务枚举（扩展性更好）

---

### 日期时间类型（Date & Time）

|类型|字节|格式|范围|使用场景|备注|
|---|---|---|---|---|---|
|⭐ DATE|3|YYYY-MM-DD|1000-01-01 ~ 9999-12-31|出生日期、交易日期|仅存日期|
|⭐ TIME|3|HH:MM:SS|-838:59:59 ~ 838:59:59|时间段、打卡时长|可存超过 24 小时的时间|
|⭐ DATETIME|8|YYYY-MM-DD HH:MM:SS|1000-01-01 ~ 9999-12-31|创建时间、更新时间|推荐；不受时区影响|
|⭐ TIMESTAMP|4|YYYY-MM-DD HH:MM:SS|1970-01-01 ~ 2038-01-19|自动更新时间|受时区影响；可自动更新（DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP）|
|YEAR|1|YYYY|1901 ~ 2155|年份|极少用，推荐用 DATE|
👉 **重点说明**：

- ⭐ `DATETIME` 最常用，适合存储业务时间（如订单创建时间）

- ⭐ `TIMESTAMP` 常用于自动更新时间（如修改时间），示例：

    ```sql
    
    CREATE TABLE test (
      id INT,
      create_time DATETIME DEFAULT CURRENT_TIMESTAMP, -- 自动填充创建时间
      update_time TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP -- 自动更新
    );
    ```

- 区别：

    - `DATETIME`：存储绝对时间，无时区问题

    - `TIMESTAMP`：存储相对时间（秒数），跨时区自动转换

---

### 二进制类型（Binary Types）

|类型|说明|使用场景|备注|
|---|---|---|---|
|BINARY(n)|固定长度二进制|加密数据、哈希值|类似 CHAR，按字节定长|
|VARBINARY(n)|可变二进制|短二进制数据|类似 VARCHAR，节省空间|
|TINYBLOB|小二进制|小图片、图标|最大 255B|
|BLOB|二进制大对象|图片 / 文件片段|常用；最大 64KB|
|MEDIUMBLOB|中等大小|视频、音频|最大 16MB|
|LONGBLOB|超大|大文件|最大 4GB|
👉 **重要建议**：

- ⚠️ 禁止将大文件（图片 / 视频）存入数据库！推荐用 OSS / 文件系统，数据库仅存文件 URL

- 仅少量二进制数据（如 MD5 哈希）可考虑 VARBINARY

---

### JSON 类型（重点）

|类型|说明|使用场景|备注|
|---|---|---|---|
|⭐ JSON|JSON 数据结构|前后端数据交互、灵活配置|MySQL 5.7 + 支持；支持索引|
👉 **示例**：

```sql

CREATE TABLE user (
  id INT PRIMARY KEY,
  info JSON -- 定义JSON字段
);
INSERT INTO user (info) VALUES ('{"name":"张三","age":18,"hobby":["篮球","读书"]}');
-- 查询JSON字段中的值
SELECT info->'$.name' AS name, info->'$.age' AS age FROM user;
```

👉 **优势 & 注意**：

- 优势：灵活存储非结构化数据，无需提前定义字段

- 注意：JSON 字段查询效率低于普通字段，仅适合非核心 / 灵活数据

---

### 常用类型总结（重点）

|场景|推荐类型|示例|
|---|---|---|
|主键 ID|⭐ BIGINT（自增）|BIGINT PRIMARY KEY AUTO_INCREMENT|
|用户名 / 字符串|⭐ VARCHAR(n)|VARCHAR(50)|
|金额|⭐ DECIMAL(M,D)|DECIMAL(10,2)|
|时间记录|⭐ DATETIME|DATETIME|
|状态值|⭐ TINYINT UNSIGNED|TINYINT UNSIGNED|
|长文本|⭐ TEXT|TEXT|
|配置 / 结构数据|⭐ JSON|JSON|
---

## 三、重要使用建议（面试 / 实战重点）

### 1. 为什么不用 FLOAT/DOUBLE 存金额？

- 原理：浮点型是近似值存储（如 0.1 无法精确表示），会导致金额计算错误

- 正确选择：`DECIMAL(10,2)`（总位数 10，小数位 2，适配大部分金额场景）

### 2. CHAR vs VARCHAR

|维度|CHAR|VARCHAR|
|---|---|---|
|长度|固定|可变|
|速度|极快（直接读取）|略慢（需计算长度）|
|空间|浪费（补空格）|节省|
|适用|短固定长度（如手机号、性别）|变长文本（如用户名、地址）|
👉 结论：**90% 场景用 VARCHAR，仅短固定长度字段用 CHAR**

### 3. DATETIME vs TIMESTAMP

|维度|DATETIME|TIMESTAMP|
|---|---|---|
|范围|1000-9999|1970-2038|
|自动更新|需手动配置|支持自动更新|
|时区|不受影响|受时区影响|
|存储空间|8 字节|4 字节（更节省）|
|推荐场景|业务时间（如创建时间）|自动更新时间（如修改时间）|
### 4. TEXT vs VARCHAR

- VARCHAR：查询快，支持索引，适合长度 < 500 的文本

- TEXT：适合大文本（>500 字符），无法直接索引（需指定前缀：`INDEX idx_content (content(100))`）

---

### 实战建表示例

```sql

CREATE TABLE user (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    username VARCHAR(50),
    password VARCHAR(100),
    age INT,
    balance DECIMAL(10,2),
    status TINYINT,
    create_time DATETIME,
    profile TEXT,
    extra JSON
);
```

---

## 四、数据表操作

### 创建表格

```sql

CREATE TABLE IF NOT EXISTS `student` ( -- 补充IF NOT EXISTS避免报错
  `student_id` INT AUTO_INCREMENT PRIMARY KEY COMMENT '学生ID（主键）', -- 补充COMMENT注释
  `name` VARCHAR(20) NOT NULL COMMENT '学生姓名',
  `major` VARCHAR(20) UNIQUE COMMENT '所学专业（唯一）',
  `class` VARCHAR(20) DEFAULT '1班' COMMENT '班级（默认1班）',
  `create_time` DATETIME DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间' -- 补充通用创建时间字段
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COMMENT='学生信息表'; -- 补充引擎/字符集
```

#### 约束说明（补充）

|约束|作用|示例|
|---|---|---|
|AUTO_INCREMENT|自增主键（从 1 开始）|INT AUTO_INCREMENT|
|NOT NULL|字段非空|VARCHAR(20) NOT NULL|
|UNIQUE|字段值唯一（可多个 NULL）|VARCHAR(20) UNIQUE|
|DEFAULT|默认值|VARCHAR (20) DEFAULT '1 班'|
|PRIMARY KEY|主键（非空 + 唯一）|PRIMARY KEY|
|COMMENT|字段 / 表注释（重要）|COMMENT ' 学生姓名'|
---

### 查询表结构

```sql

DESCRIBE `student`;
-- 或简写
DESC `student`;
-- 更详细的表信息（补充）
SHOW CREATE TABLE `student`; -- 查看建表语句（含引擎、字符集等）
```

### 删除表格

```sql

DROP TABLE IF EXISTS `student`; -- 补充IF EXISTS避免报错
```

---

### 查询表格内容

#### 基础查询

```sql

SELECT * FROM `student`; -- 查询所有字段（生产环境尽量避免*，指定字段）
SELECT `student_id`, `name` FROM `student`; -- 推荐：指定需要的字段
```

#### 条件查询（补充核心知识点）

```sql

-- 带WHERE条件
SELECT * FROM `student` WHERE `major` = '历史' AND `class` = '2班';
-- 排序
SELECT * FROM `student` ORDER BY `student_id` DESC; -- DESC降序，ASC升序（默认）
-- 分页（补充：实战必备）
SELECT * FROM `student` LIMIT 10 OFFSET 0; -- 第1页，每页10条
SELECT * FROM `student` LIMIT 10, 20; -- 简写：从第10条开始，取20条
-- 去重
SELECT DISTINCT `major` FROM `student`; -- 去重查询专业
```

#### UNION 并集

> 注意：UNION 合并的字段数必须一致，数据类型兼容；UNION 默认去重，UNION ALL 保留重复（效率更高）
> 
> 

```sql

-- 去重并集
SELECT `name` FROM `employee` UNION SELECT `client_name` FROM `client`;
-- 保留重复并集（推荐，效率更高）
SELECT `name` FROM `employee` UNION ALL SELECT `client_name` FROM `client`;
```

---

### 修改表格属性

```sql

-- 1. 添加字段
ALTER TABLE `student` ADD `gpa` DECIMAL(3,2) COMMENT '绩点' AFTER `major`; -- AFTER指定位置
-- 2. 删除字段
ALTER TABLE `student` DROP COLUMN `gpa`;
-- 3. 修改字段类型/约束（补充）
ALTER TABLE `student` MODIFY `name` VARCHAR(30) NOT NULL; -- 修改字段长度
-- 4. 重命名字段（补充）
ALTER TABLE `student` CHANGE `name` `student_name` VARCHAR(20) NOT NULL; -- 字段名+类型一起改
```

### 修改表名

```sql

RENAME TABLE `student` TO `student_info`;
-- 补充：也可通过ALTER TABLE修改
ALTER TABLE `student` RENAME TO `student_info`;
```

### 复制表

```sql

-- 1. 仅复制表结构（无数据）
CREATE TABLE `student_copy` LIKE `student`;
-- 2. 复制表结构+数据（补充）
CREATE TABLE `student_copy` AS SELECT * FROM `student`;
```

---

### 修改表格数据

#### INSERT 插入

```sql

-- 方式1：指定所有字段（顺序需匹配）
INSERT INTO `student` VALUES(1, '小白', '历史', '1班', NOW());
-- 方式2：指定字段（推荐，字段顺序可调整，缺省字段用默认值）
INSERT INTO `student` (`name`, `major`) VALUES('小白', '历史');
-- 批量插入（补充：效率更高）
INSERT INTO `student` (`name`, `major`) VALUES
('小红', '数学'),
('小黑', '英语');
```

#### UPDATE 更新

> 高危！必须加 WHERE 条件，否则更新全表！
> 
> 

```sql

-- 单条件更新
UPDATE `student` SET `major` = '英语文学' WHERE `major` = '英语';
-- 多字段更新（补充）
UPDATE `student` SET `major` = '计算机', `class` = '3班' WHERE `student_id` = 1;
-- 加限制（避免误更）
UPDATE `student` SET `major` = '英语文学' WHERE `major` = '英语' LIMIT 10;
```

#### DELETE 删除

> 高危！必须加 WHERE 条件；删除全表推荐 TRUNCATE（效率更高）
> 
> 

```sql

-- 条件删除
DELETE FROM `student` WHERE `student_id` = 4;
-- 清空全表（补充：TRUNCATE，自增ID重置）
TRUNCATE TABLE `student`; -- 比DELETE * 效率高，不可回滚
```

---

## 五、外键（表关联）

> 外键用于建立两个表之间的关联，保证数据的参照完整性；仅 InnoDB 引擎支持外键。
> 核心：外键字段必须关联另一张表的主键 / 唯一索引。
> 
> 

### 外键语法 & 规则

```sql

FOREIGN KEY (外键字段) REFERENCES 主表(主表字段) 
[ON DELETE {CASCADE | SET NULL | RESTRICT | NO ACTION}] 
[ON UPDATE {CASCADE | SET NULL | RESTRICT | NO ACTION}]
```

|选项|作用|适用场景|
|---|---|---|
|ON DELETE CASCADE|主表记录删除，从表关联记录也删除|子表依赖主表（如订单明细依赖订单）|
|ON DELETE SET NULL|主表记录删除，从表外键字段设为 NULL|子表不强制依赖主表（如员工关联经理）|
|ON DELETE RESTRICT|主表有关联记录时，禁止删除|核心数据（如用户表）|
|ON DELETE NO ACTION|同 RESTRICT（MySQL 默认）||
### 外键示例

```sql

-- 1. 创建员工表（主表）
CREATE TABLE IF NOT EXISTS `employee` (
  `emp_id` INT(3) PRIMARY KEY COMMENT '员工ID',
  `name` VARCHAR(20) NOT NULL COMMENT '员工姓名',
  `birth_date` DATE NOT NULL COMMENT '出生日期',
  `sex` ENUM('M','F') NOT NULL COMMENT '性别（M男/F女）', -- 优化：用ENUM限定值
  `salary` DECIMAL(10,2) NOT NULL COMMENT '薪资', -- 优化：金额用DECIMAL
  `branch_id` INT(1) COMMENT '部门ID（外键）',
  `sup_id` INT(3) COMMENT '上级ID（自关联外键）',
  `create_time` DATETIME DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间'
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

-- 2. 创建部门表（从表，先创建空表，再加外键避免关联顺序问题）
CREATE TABLE IF NOT EXISTS `branch` (
  `branch_id` INT(1) NOT NULL PRIMARY KEY COMMENT '部门ID',
  `branch_name` VARCHAR(20) NOT NULL COMMENT '部门名称',
  `manager_id` INT(3) COMMENT '部门经理ID（外键）',
  `create_time` DATETIME DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间'
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

-- 3. 添加外键约束（推荐：先建表，后加外键，避免关联顺序问题）
-- 部门表关联员工表（经理ID）
ALTER TABLE `branch` ADD CONSTRAINT fk_branch_manager 
FOREIGN KEY (`manager_id`) REFERENCES `employee`(`emp_id`) ON DELETE SET NULL;

-- 员工表关联部门表（部门ID）
ALTER TABLE `employee` ADD CONSTRAINT fk_employee_branch 
FOREIGN KEY (`branch_id`) REFERENCES `branch`(`branch_id`) ON DELETE SET NULL;

-- 员工表自关联（上级ID）
ALTER TABLE `employee` ADD CONSTRAINT fk_employee_supervisor 
FOREIGN KEY (`sup_id`) REFERENCES `employee`(`emp_id`) ON DELETE SET NULL;

-- 4. 创建客户表
CREATE TABLE IF NOT EXISTS `client` (
  `client_id` INT(3) NOT NULL PRIMARY KEY COMMENT '客户ID',
  `client_name` VARCHAR(20) NOT NULL COMMENT '客户名称',
  `phone` VARCHAR(20) NOT NULL COMMENT '手机号（优化：用VARCHAR，避免超长）'
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

-- 5. 创建员工-客户关联表（多对多）
CREATE TABLE IF NOT EXISTS `works_with` (
  `emp_id` INT(3) COMMENT '员工ID',
  `client_id` INT(3) COMMENT '客户ID',
  `total_sales` DECIMAL(10,2) NOT NULL COMMENT '销售额',
  PRIMARY KEY (`emp_id`,`client_id`), -- 复合主键
  FOREIGN KEY (`emp_id`) REFERENCES `employee`(`emp_id`) ON DELETE CASCADE,
  FOREIGN KEY (`client_id`) REFERENCES `client`(`client_id`) ON DELETE CASCADE
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;
```

### 外键使用注意事项（补充）

1. 外键会降低写入性能，高并发场景可考虑**业务层维护关联**（不用数据库外键）

2. 外键字段必须与主表字段类型一致（如 INT (3) 对应 INT (3)）

3. 主表字段必须是主键 / 唯一索引，否则无法创建外键

4. 插入数据时，先插主表数据，再插从表数据（避免外键约束报错）

---

## 六、聚合函数（Aggregate Functions）

```sql

-- 1.取得员工人数
SELECT COUNT(*) FROM `employee`;
-- 2.取得所有出生于1970-01-01之后的女性员工人数
SELECT COUNT(*) FROM `employee` WHERE `birth_date`>'1970-01-01' AND `sex`='F';
-- 3.取得所有员工的平均薪水
SELECT AVG(`salary`) FROM `employee`;
-- 4.取得所有员工薪水的总和
SELECT SUM(`salary`) FROM `employee`;
-- 5.取得薪水最高的员工
SELECT MAX(`salary`) FROM `employee`;
-- 6.取得薪水最低的员工
SELECT MIN(`salary`) FROM `employee`;
```

---

## 七、JOIN 连接查询

```sql

INSERT INTO `branch` VALUES(4,'偷懒',NULL);

-- 取得所有部门经理的名字
SELECT `employee`.`name` FROM `employee` JOIN `branch` ON `employee`.`emp_id` = `branch`.`manager_id`;

-- LEFT JOIN：保留左表全部数据
SELECT `employee`.`emp_id`,`employee`.`name`,`branch`.`branch_name` FROM `employee` LEFT JOIN `branch` ON `employee`.`emp_id` = `branch`.`manager_id`;

-- RIGHT JOIN：保留右表全部数据
SELECT `employee`.`emp_id`,`employee`.`name`,`branch`.`branch_name` FROM `employee` RIGHT JOIN `branch` ON `employee`.`emp_id` = `branch`.`manager_id`;
```

---

## 八、通配符（Wildcards）

- `%`：匹配任意多个字符（包括 0 个）

- `_`：匹配**一个**字符

```sql

-- 1.取得电话号码尾数是335的客户
SELECT * FROM `client` WHERE `phone` LIKE '%335';
-- 2.取得姓艾的客户
SELECT * FROM `client` WHERE `client_name` LIKE '艾%';
-- 3.取得生日在12月的员工
SELECT * FROM `employee` WHERE `birth_date` LIKE '_____12%';
```

---

## 九、子查询（Subquery）

```sql

-- 1.找出研发部门经理的名字 
-- 方法1：子查询
SELECT `name` FROM `employee` WHERE `emp_id` = (SELECT `manager_id` FROM `branch` WHERE `branch_name`='研发');
-- 方法2：JOIN
SELECT `employee`.`name` FROM `employee` JOIN `branch` ON `employee`.`emp_id` = `branch`.`manager_id` AND `branch`.`branch_name`='研发';

-- 2.找出对单一位客户销售金额超过50000的员工名字
-- 方法1：子查询 + IN（多结果）
SELECT `name` FROM `employee` WHERE `employee`.`emp_id` IN (SELECT `works_with`.`emp_id` FROM `works_with` WHERE `total_sales`> 50000);
-- 方法2：JOIN
SELECT `employee`.`name` FROM `employee` JOIN `works_with` ON `employee`.`emp_id` = `works_with`.`emp_id` AND `works_with`.`total_sales`> 50000;
```

---

## 十、基础常用查询示例

```sql

-- 1. 取得所有员工资料（指定字段，避免*）
SELECT `emp_id`, `name`, `salary`, `branch_id` FROM `employee`;

-- 2. 按薪水从高到低排序，取前3名（TOP N）
SELECT * FROM `employee` ORDER BY `salary` DESC LIMIT 3;

-- 3. 统计各部门员工数量（补充：聚合函数）
SELECT `branch_id`, COUNT(*) AS emp_count FROM `employee` GROUP BY `branch_id`;

-- 4. 查询销售额>10000的员工ID和销售额（补充：HAVING过滤聚合结果）
SELECT `emp_id`, SUM(`total_sales`) AS total FROM `works_with` 
GROUP BY `emp_id` HAVING total > 10000;

-- 5. 关联查询：查询员工姓名+所属部门名称（补充：JOIN核心）
SELECT e.`name`, b.`branch_name` 
FROM `employee` e 
LEFT JOIN `branch` b ON e.`branch_id` = b.`branch_id`;
```

---

## 十一、核心优化建议（新增）

1. **命名规范**：库 / 表 / 字段用小写 + 下划线，避免关键字（如`user`改为`sys_user`）

2. **引擎选择**：优先 InnoDB（支持事务、外键、行锁），MyISAM 仅用于只读场景

3. **字符集**：统一用 utf8mb4（兼容 emoji、特殊字符），避免乱码

4. **主键设计**：

    - 单表主键：BIGINT AUTO_INCREMENT（避免 INT 溢出）

    - 多对多关联表：复合主键（如`emp_id`+`client_id`）

5. **避免 NULL**：核心字段尽量 NOT NULL，用默认值替代（如状态默认 0）

6. **索引**：

    - 主键自动建索引，外键建议建索引

    - 高频查询字段（如用户名、手机号）建索引

    - 避免给大文本 / JSON 字段建索引

7. **事务**：修改数据时用事务（BEGIN/COMMIT/ROLLBACK），避免数据不一致

---

## 十二、常见错误 & 避坑（新增）

1. 插入数据时外键约束报错：先插主表数据，再插从表

2. 金额计算错误：用 DECIMAL 而非 FLOAT/DOUBLE

3. TIMESTAMP 溢出：2038 年后失效，建议用 DATETIME

4. DELETE/UPDATE 全表：忘记加 WHERE 条件，务必先 SELECT 验证条件

5. 表名 / 字段名冲突：用反引号```包裹（如`order`是关键字，需写`order`）