## MySQL字符集

``show  character set;``查看支持的字符集

``show collation;``查看支持的字符序

### 服务端的字符集

- character_set_client     | utf8mb4 

  客户端来源数据使用的字符集，即为客户端字符集

- character_set_connection | utf8mb4 

  连接层字符集，即为客户端连接字符集

- character_set_database   | ascii 

  当前选中的数据库的默认字符集，即为数据库字符集，配置文件指定或者建库表指定

- character_set_filesystem | binary 

  文件系统字符集

- character_set_results    | utf8mb4 

  查询结果字符集，即客户端返回结果字符集

- character_set_server     | ascii 

  默认的内部操作字符集，服务器字符集，配置文件指定或建库表指定

- character_set_system     | utf8

  系统元数据（字段名）字符集，即为系统字符集

### 客户端字符集

#### 对于输入来说

客户端使用的字符集必须通过：character_set_client、character_set_connection体现

1. 在客户端对数据进行编码（Linux: utf8、 windows: gbk）

2. MySQL接受到SQL语句后（比如insert），发现有字符，询问客户端通过什么方式对字符进行编码：客户端通过character_set_cllient参数高数MySQL客户端的编码方式（所以此时参数需要正确反映客户端对应的编码

3. 当MySQL发现客户端的client所传输的字符集与自己的connection不一样时，会将传输的内容重新编码为connection过的字符集（中间经过Unicode）

4. MySQL将转换后的内容存储到MySQL的表的列上时，在存储的时候再判断编码是否与内部存储字符集相同，不一致再进行转换

   > character_set_client-->character_set_connection-->表字符集

#### 对于查询来说

客户端使用的字符集必须通过character_set_results来体现，服务端询问客户独胆字符集，通过character_set_results将结果转换为与客户端相同的字符集传递给客户端。

> 表字符集-->character_set_results

#### 数据库、表和列字符集

- 建库时，若未明确指定字符集，则采用character_set_server指定的字符集
- 建表时，若未明确指定字符集，则采用当前库所采用的字符集
- 新增时，修改表字段时，若未明确指定字符集，则采用当前表所采用的字符集

#### 查看数据库、表和列的字符集

- 数据库``show create database pinginglab;``
- 表``show create table pinginglab.test``
- 字段``show full columns from pinginglab.test``

#### 字符集与字符序配置

- 通过配置文件指定

- 在创建数据库、表的时候指定

  `` create table student(c1 varchar(30)) engine=innodb character set gbk collate gbk_chinese_ci ; ``


- 设置字符集编码

- ``set names 'utf8'``该语句同时将character_set_client、character_set_results、character_set_connection改为utf8

- 修改数据库字符集

  ``alter database database_name character set xxx``

  只修改库的字符集，影响后序建表的表的默认定义

  对于已创建的表的字符集不受影响

  一般在数据库实现字符集即可，表和列都默认采用数据库的字符集

- 修改表的字符集

  ``alter table table_name character set xxx``只修改表的字符集，影响后续该表新增列的的默认定义，已有列的字符集不受影响

  ``alter table table_name convert tocharacter set xxx ``同时修改表字符集和已有字符集，并将已有数据进行字符集编码转换

- 修改列的字符集

  ``alter table table_name modify col_name varchar(col_length) character set xxx``