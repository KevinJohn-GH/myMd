## MySQL日志

### MySQL日志简述

服务器在数据目录中建立所有的日志文件；

在未设置其他路径名的情况下，服务器会将文件名设置为当前主机名（hostname.log）;

通过在启动服务器时使用对应的选项（在命令行上或者选项文件中），可以启用这些日志文件



### basedir和datadir路径说明

- basedir

  > 该参数指定了安装MySQL的安装路径

- datadir

  > 该参数指定了MySQL的数据库文件放在什么路径下
  >
  > 数据库文件即我们常说的mysqldata文件
  >
  > 可以理解成为MySQL的一个缺省目录：
  >
  > 如果某个参数对应的是一个文件，并且没有带绝对路径，则该文件是存放在datadir参数指定的目录



### 一、错误日志

#### 日志路径

`show variables like 'log_error%'`

可以获取此时MySQL运行时的错误日志位置，但是输出结果是一个相对路径；

``show variables like 'datadir%'``

可以获取错误日志的父目录路径（绝对路径）

也可以在命令行参数中使用--log-error去指定错误日志的存放路径

#### 日志内容

- MySQL启动和关闭过程中的信息
- MySQL 运行过程中的错误信息
- 事件调度器运行一个事件时产生的信息
- 在从服务器上启动从服务器进程时产生的信息
- 当数据库出现任何故障导致无法使用时，可以优先查询该日志

#### 日志配置

- my.cnf配置文件中``log_error=/PATH/TO/ERROR_LOG_FLIENAME``

- 推荐配置方法：在my.cnf中加入配置

- 错误日志粗糙监控方法

  ```
  mkdir /data/backup
  cp error.log /data/backup
  diff /data/backup/error.log ./error.log
  ```

#### 优化建议

- 错误日志，mysql默认开启
- 需要重启mysql才能使配置生效

### 二、二进制日志

#### 日志路径

通过log_bin参数去控制是否开启二进制日志

- 开启二进制日志的方式 ``log_bin=/data/mysql/mysql3306/logs/mysql-bin``
- 关闭二进制日志方式``log_bin=off``

#### 日志格式``binlog_format = row``

- statement

  > 说明
  >
  > - 日志中记录的都是语句（statement），每一条对数据造成修改的SQL语句都会记录在日志中，通过``mysqlbinlog mysql-bin``,可以清晰地看到每条语句的文本
  > - 主从复制的时候，从库（slave）会将日志解析为原文本，并在从库重新执行一次
  >
  > 优点
  >
  > - 日志记录清晰易读，日志量少，对IO影响较小
  >
  > 缺点
  >
  > - 缺点是在某些情况下slave的日志复制会出错


- row

  >说明
  >
  >- 它将每一行的变更记录到日志文件中，而不是记录SQL·语句
  >
  >优点
  >
  >- 惠济路到每一行的变化细节，不会出现某些情况下无法复制的情况
  >
  >缺点
  >
  >- 日志量大，对IO影响较大

-  mixed

  >说明
  >
  >- 目前MySQL默认的日志格式，即混合了statement和row两种日志
  >- 默认情况下使用statement，但在一些特殊情况下采用row来进行记录
  >- 使用row的情况：客户端使用了不确定函数，比如``current_user()``等，因为这种不确定函数在主从复制中得到的值可能 不同，导致主从数据产生不一致

  ​

  #### 日志内容

  - 记录了所有的DDL与DML语句，不包括数据查询语句
  - 一般用来数据库恢复和主从复制
  - 二进制日志以一种更有效的格式，并且是事物安全的方式包含更新日志中所有的信息
  - 二进制日志包含了所有更新了数据或者潜在地更新了数据的所有语句。语句以“事件”的形式保存，它描述数据更改
  - 二进制日志还包含了关于每个更新数据库语句的执行时间信息，它不包含没有修改任何数据的语句
  - 二进制日志的主要目的是在恢复时能最大可能地更新数据库，因为二进制日志包含备份后进行的所有更新
  - 二进制日志还用在主从复制服务器上记录所有将发送给从服务器的语句
  - 运行服务器时若启用二进制日志性能大约慢1%，但是二进制日志的好处，远超过这个小小的性能损失

  #### 日志配置

  - ``logbin =/data/mysql/mysql3306/logs/mysql-bin``定义二进制日志路径
  - ``expire_logs_days = 7``定义二进制日志文件删除周期，7代表7天删除一次二进制日志文件
  - ``max_binlog_size = 100M``定义二进制日志文件的最大值
  - ``binlog_do_db= include_database_name``:指定的``include_database_name``数据库下运行的语句，将会记录到二进制日志文件
  - ``binlog_ignore_db =include_database_name ``:该数据库下运行的语句，不会被记录到二进制日志文件

  #### 日志管理

  - 日志读取

    >说明：二进制日志是以二进制进行存执，不能直接读取，需要借助mysqlbinloog工具进行日志查看
    >
    >``mysqlbinlong binlog_name``

    - ``show binary logs;``: 列出当前的二进制日志文件及大小
    - ``show master status``: 显示MySQL当前的二进制日志及状态（需要具备super、replication和cllient权限）
    - ``show binlog event in 'mysql-bin.000001'``: MySQL的二进制日志是以”event“（事件）为单位存储到日志中，一个insert、update......由多个事件组成

  - 日志删除

    - ``reset master``: 将所有binlog日志删除，新日志从”000001“开始
    - ``purge binary logs to 'mysql-bin.xxxxxx'``: 删除xxxxxx编号之前的所有日志
    - ``purge master logs before 'yyyy-mm-dd hh24:mi:ss'``:删除指定时间前的所有日志
    - ``purge binary logs before now() -interval 3 days``: 将前三天的二进制日志清空

    ​

    - 删除建议
      - 建议使用``expire_logs_days=``的方式实现二进制日志清理
      - 如果日志量过大导致磁盘空间爆了建议使用``purge binary logs to mysql-bin.xxxxxx``
      - 系统因为日志文件导致系统很卡，只能使用rm命令清空，清理原则是：清理编号较小的二进制日志文件
      - mysql-bin.index不能动

  - 优化建议

    - 如果需要做数据库恢复主从复制，建议开启该功
    - ``show binlog events in 'binary_logfile' from position``查看二进制文件内容
    - 修改my.cnf配置文件后，需要重启mysql才能生效
    - 由于二进制日志的重要性，请仅在确定不再需要将要被删除的二进制文件，或者已经对二进制文件日志进行归档备份，或者已经进行数据库备份的情况下，才能进行删除操作，且不要使用rm命令
    - 可以在global和session级别对binlog_format 进行日止格式的设置，但一定要谨慎操作

### 三、 一般查询日志

#### 日志路径

``show variables like 'general_log%'; ``

#### 日志内容

> 查询操作都会被记录到查询日记中

#### 日志配置

- ``general_log =off`` 定义是否打开查询日志功能，off表示关闭，on表示打开
- ``general_log_file=general.log``定义查询日志的具体内容和文件
- 可以通过启动MySQL时通过--general_log参数指定日志文件路径和文件名

#### 优化建议

- 查询日志默认关闭，查询日志的开启，会过多的消耗系统的IO资源
- 除非是调试，否则一般建议不开启查询日志

### 四、慢查询日志

### 日志路径

``show variables like'%query%'; ``

#### 日志内容

- 查询时间超过my.cnf文件中``long_query_time``参数定义的时间，则会将查询日志写入到慢查询中
- 慢查询日志记录了所有执行爱过时间超过了参数``long_query_time``（单位：秒）设置值并扫描记录不少于``min_exmined_row_limit``的所有SQL语句
- ``long_query_time``默认为10秒，最小为0，精度可以到微秒
- 管理语句和不使用索引进行查询的语句不会记录到慢查询日志中，如果要监控这两类SQL·语句，可以分别通过参数``--log-slow-admin-statement``和``--log-queries_not_using_indexes``进行控制

#### 日志配置

- ``slow_query_log={on|off}``定义是否开启慢查询日志

- ``slow_query_log_file``慢查询日志文件路径

- ````long_slow_queries={on|off}``定义是否开启慢查询日志功能

- ``long_query_time=2``定义慢查询阀值，时间单位为秒

- ``log_queries_not_using_indexs``

  定义查询类型不使用索引

  不使用索引进行查询的语句是不会记录到日志中的

  在实际工程项目中这个参数我们可以不用设置


#### 优化建议

- 本次使用的版本，默认慢查询日志是关闭的
- 慢查询日志消耗系统资源小，对数据库优化有帮助，建议开启

