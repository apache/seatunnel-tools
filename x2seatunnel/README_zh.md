# X2SeaTunnel 配置转换工具
X2SeaTunnel 是一个用于将 DataX 等配置文件转换为 SeaTunnel 配置文件的工具，旨在帮助用户快速从其它数据集成平台迁移到 SeaTunnel。

## 🚀 快速开始

### 前置条件

- Java 8 或更高版本

### 安装

#### 从源码编译
```bash
# 在本仓库内编译 x2seatunnel 模块
mvn clean package -pl x2seatunnel -DskipTests
```
编译结束后，发布包位于 `x2seatunnel/target/x2seatunnel-*.zip`。

#### 使用发布包
```bash
# 下载并解压发布包
unzip x2seatunnel-*.zip
cd x2seatunnel-*/
```

### 基本用法

```bash
# 标准转换：使用默认模板系统，内置常见的Source和Sink
./bin/x2seatunnel.sh -s examples/source/datax-mysql2hdfs.json -t examples/target/mysql2hdfs-result.conf -r examples/report/mysql2hdfs-report.md

# 自定义任务: 通过自定义模板实现定制化转换需求
# 场景：MySQL → Hive（DataX 没有 HiveWriter）
# DataX 配置：MySQL → HDFS 自定义任务：转换为 MySQL → Hive
./bin/x2seatunnel.sh -s examples/source/datax-mysql2hdfs2hive.json -t examples/target/mysql2hive-result.conf -r examples/report/mysql2hive-report.md -T templates/datax/custom/mysql-to-hive.conf

# YAML 配置方式（等效于上述命令行参数）
./bin/x2seatunnel.sh -c examples/yaml/datax-mysql2hdfs2hive.yaml

# 批量转换模式：按目录处理
./bin/x2seatunnel.sh -d examples/source -o examples/target2 -R examples/report2

# 批量模式支持通配符过滤
./bin/x2seatunnel.sh -d examples/source -o examples/target3 -R examples/report3 --pattern "*-full.json" --verbose

# 查看帮助
./bin/x2seatunnel.sh --help
```

### 转换报告
转换完成后，查看生成的Markdown报告文件，包含：
- **基本信息**: 转换时间、源/目标文件路径、连接器类型、转换状态等
- **转换统计**: 直接映射、智能转换、默认值使用、未映射字段的数量和百分比
- **详细字段映射关系**: 每个字段的源值、目标值、使用的过滤器等
- **默认值使用情况**: 列出所有使用默认值的字段
- **未映射字段**: 显示DataX中存在但未转换的字段
- **可能的错误和警告信息**: 转换过程中的问题提示

如果是批量转换，则会在批量生成转换报告的文件夹下，生成批量汇总报告 `summary.md`，包含：
- **转换概览**: 总体统计信息、成功率、耗时等
- **成功转换列表**: 所有成功转换的文件清单
- **失败转换列表**: 失败的文件及错误信息（如有）


### 日志文件
```bash
# 查看日志文件
tail -f logs/x2seatunnel.log
```


## 🎯 功能特性

- ✅ **标准配置转换**: DataX → SeaTunnel 配置文件转换
- ✅ **自定义模板转换**: 支持用户自定义转换模板
- ✅ **详细转换报告**: 生成 Markdown 格式的转换报告
- ✅ **支持正则表达式变量提取**: 从配置中正则提取变量，支持自定义场景
- ✅ **批量转换模式**: 支持目录和文件通配符批量转换，自动生成报告和汇总报告

## 📁 目录结构

```
x2seatunnel/
├── bin/                        # 可执行文件
│   ├── x2seatunnel.sh         # 启动脚本
├── lib/                        # JAR包文件
│   └── x2seatunnel-*.jar      # 核心JAR包
├── config/                     # 配置文件
│   └── log4j2.xml             # 日志配置
├── templates/                  # 模板文件
│   ├── template-mapping.yaml  # 模板映射配置
│   ├── report-template.md     # 报告模板
│   └── datax/                 # DataX相关模板
│       ├── custom/            # 自定义模板
│       ├── env/               # 环境配置模板
│       ├── sources/           # 数据源模板
│       └── sinks/             # 数据目标模板
├── examples/                   # 示例和测试
│   ├── source/                # 示例源文件
│   ├── target/                # 生成的目标文件
│   └── report/                # 生成的报告
├── logs/                       # 日志文件
├── LICENSE                     # 许可证
└── README.md                   # 使用说明
```

## 📖 使用说明

### 基本语法

```bash
x2seatunnel [OPTIONS]
```

### 命令行参数

| 选项     | 长选项          | 描述                                                 | 必需 |
|----------|-----------------|------------------------------------------------------|------|
| -s       | --source        | 源配置文件路径                                       | 是   |
| -t       | --target        | 目标配置文件路径                                     | 是   |
| -st      | --source-type   | 源配置类型 (datax, 默认: datax)                      | 否   |
| -T       | --template      | 自定义模板文件路径                                   | 否   |
| -r       | --report        | 转换报告文件路径                                     | 否   |
| -c       | --config        | YAML 配置文件路径，包含 source, target, report, template 等设置 | 否   |
| -d       | --directory     | 批量转换源目录                                       | 否   |
| -o       | --output-dir    | 批量转换输出目录                                     | 否   |
| -p       | --pattern       | 文件通配符模式（逗号分隔，例如: *.json,*.xml）        | 否   |
| -R       | --report-dir    | 批量模式下报告输出目录，单文件报告和汇总 summary.md 将输出到该目录 | 否   |
| -v       | --version       | 显示版本信息                                         | 否   |
| -h       | --help          | 显示帮助信息                                         | 否   |
|          | --verbose       | 启用详细日志输出                                     | 否   |

```bash
# 示例：查看命令行帮助
./bin/x2seatunnel.sh --help
```

### 支持的配置类型

#### 源配置类型
- **datax**: DataX配置文件（JSON格式）- 默认类型

#### 目标配置类型
- **seatunnel**: SeaTunnel配置文件（HOCON格式）

## 🎨 模板系统

### 设计理念

X2SeaTunnel 采用基于 DSL (Domain Specific Language) 的模板系统，通过配置驱动的方式实现不同数据源和目标的快速适配。核心优势：

- **配置驱动**：所有转换逻辑都通过 YAML 配置文件定义，无需修改 Java 代码
- **易于扩展**：新增数据源类型只需添加模板文件和映射配置
- **统一语法**：使用 Jinja2 风格的模板语法，易于理解和维护
- **智能映射**：通过转换器（transformer）实现复杂的参数映射逻辑

### 模板语法

X2SeaTunnel 支持部分兼容 Jinja2 风格模板语法，提供丰富的过滤器功能来处理配置转换。

```bash
# 基本变量引用
{{ datax.job.content[0].reader.parameter.username }}

# 带过滤器的变量
{{ datax.job.content[0].reader.parameter.column | join(',') }}

# 链式过滤器
{{ datax.job.content[0].writer.parameter.path | split('/') | get(-2) | replace('.db','') }}
```


### 2. 过滤器

| 过滤器 | 语法 | 描述 | 示例 |
|--------|------|------|------|
| `join` | `{{ array \| join('分隔符') }}` | 数组连接 | `{{ columns \| join(',') }}` |
| `default` | `{{ value \| default('默认值') }}` | 默认值 | `{{ port \| default(3306) }}` |
| `upper` | `{{ value \| upper }}` | 大写转换 | `{{ name \| upper }}` |
| `lower` | `{{ value \| lower }}` | 小写转换 | `{{ name \| lower }}` |
| `split` | `{{ string \| split('/') }}` | 字符串分割 | `'a/b/c' → ['a','b','c']` |
| `get` | `{{ array \| get(0) }}` | 获取数组元素 | `['a','b','c'] → 'a'` |
| `replace` | `{{ string \| replace('old,new') }}` | 字符串替换 | `'hello' → 'hallo'` |
| `regex_extract` | `{{ string \| regex_extract('pattern') }}` | 正则提取 | 提取匹配的内容 |
| `jdbc_driver_mapper` | `{{ jdbcUrl \| jdbc_driver_mapper }}` | JDBC 驱动映射 | 自动推断驱动类 |

### 3. 样例

```bash
# join 过滤器：数组连接
query = "SELECT {{ datax.job.content[0].reader.parameter.column | join(',') }} FROM table"

# default 过滤器：默认值
partition_column = "{{ datax.job.content[0].reader.parameter.splitPk | default('') }}"
fetch_size = {{ datax.job.content[0].reader.parameter.fetchSize | default(1024) }}

# 字符串操作
driver = "{{ datax.job.content[0].reader.parameter.connection[0].jdbcUrl[0] | upper }}"
```

```bash
# 链式过滤器：字符串分割和获取
{{ datax.job.content[0].writer.parameter.path | split('/') | get(-2) | replace('.db','') }}

# 正则表达式提取
{{ jdbcUrl | regex_extract('jdbc:mysql://([^:]+):') }}

# 转换器调用：智能参数映射
driver = "{{ datax.job.content[0].reader.parameter.connection[0].jdbcUrl[0] | jdbc_driver_mapper }}"
```

```bash
# 智能查询生成
query = "{{ datax.job.content[0].reader.parameter.querySql[0] | default('SELECT') }} {{ datax.job.content[0].reader.parameter.column | join(',') }} FROM {{ datax.job.content[0].reader.parameter.connection[0].table[0] }} WHERE {{ datax.job.content[0].reader.parameter.where | default('1=1') }}"

# 路径智能解析：从 HDFS 路径提取 Hive 表名
# 路径: /user/hive/warehouse/test_ods.db/test_table/partition=20240101
database = "{{ datax.job.content[0].writer.parameter.path | split('/') | get(-3) | replace('.db','') }}"
table = "{{ datax.job.content[0].writer.parameter.path | split('/') | get(-2) }}"
table_name = "{{ database }}.{{ table }}"
```

```bash
# 自动推断数据库驱动
{{ datax.job.content[0].reader.parameter.connection[0].jdbcUrl[0] | jdbc_driver_mapper }}

# 映射关系（在 template-mapping.yaml 中配置）:
# mysql -> com.mysql.cj.jdbc.Driver
# postgresql -> org.postgresql.Driver
# oracle -> oracle.jdbc.driver.OracleDriver
# sqlserver -> com.microsoft.sqlserver.jdbc.SQLServerDriver
```

### 4. 模板配置示例

```hocon
env {
  execution.parallelism = {{ datax.job.setting.speed.channel | default(1) }}
  job.mode = "BATCH"
}

source {
  Jdbc {
    url = "{{ datax.job.content[0].reader.parameter.connection[0].jdbcUrl[0] }}"
    driver = "com.mysql.cj.jdbc.Driver"
    user = "{{ datax.job.content[0].reader.parameter.username }}"
    password = "{{ datax.job.content[0].reader.parameter.password }}"
    query = "{{ datax.job.content[0].reader.parameter.querySql[0] | default('SELECT') }} {{ datax.job.content[0].reader.parameter.column | join(',') }} FROM {{ datax.job.content[0].reader.parameter.connection[0].table[0] }}"
    plugin_output = "source_table"
  }
}

sink {
  Hive {
    # 从路径智能提取 Hive 表名
    # 使用 split 和 get 过滤器来提取数据库名和表名
    # 步骤1：分割路径
    # 步骤2：获取倒数第二个部分作为数据库名，去掉.db后缀
    # 步骤3：获取倒数第一个部分作为表名
    table_name = "{{ datax.job.content[0].writer.parameter.path | split('/') | get(-3) | replace('.db,') }}.{{ datax.job.content[0].writer.parameter.path | split('/') | get(-2) }}"

    # Hive Metastore配置
    metastore_uri = "{{ datax.job.content[0].writer.parameter.metastoreUri | default('thrift://localhost:9083') }}"
    
    # 压缩配置
    compress_codec = "{{ datax.job.content[0].writer.parameter.compress | default('none') }}"
    
    # Hadoop配置文件路径（可选）
    # hdfs_site_path = "/etc/hadoop/conf/hdfs-site.xml"
    # hive_site_path = "/etc/hadoop/conf/hive-site.xml"
    
    # Hadoop配置（可选）
    # hive.hadoop.conf = {
    #   "fs.defaultFS" = "{{ datax.job.content[0].writer.parameter.defaultFS | default('hdfs://localhost:9000') }}"
    # }
    
    # 结果表名
    plugin_input = "source_table"
  }
}
```

### 自定义转换器

通过 `templates/template-mapping.yaml` 配置自定义转换器：

```yaml
transformers:
  # JDBC 驱动映射
  jdbc_driver_mapper:
    mysql: "com.mysql.cj.jdbc.Driver"
    postgresql: "org.postgresql.Driver"
    oracle: "oracle.jdbc.driver.OracleDriver"
    sqlserver: "com.microsoft.sqlserver.jdbc.SQLServerDriver"
  
  # 文件格式映射
  file_format_mapper:
    text: "text"
    orc: "orc"
    parquet: "parquet"
    json: "json"
```

## 扩展新数据源

添加新数据源类型只需三步：

1. **创建模板文件**：在 `templates/datax/sources/` 下创建新的模板文件
2. **配置映射关系**：在 `template-mapping.yaml` 中添加映射配置
3. **添加转换器**：如需特殊处理，添加对应的转换器配置

无需修改任何 Java 代码，即可支持新的数据源类型。


## 🌐 支持的数据源和目标

### 数据源（Sources）

| 数据源类型 | DataX Reader | 模板文件 | 支持状态 |
|-----------|-------------|----------|----------|
| **MySQL** | `mysqlreader` | `mysql-source.conf` | ✅ 支持 |
| **PostgreSQL** | `postgresqlreader` | `jdbc-source.conf` | ✅ 支持 |
| **Oracle** | `oraclereader` | `jdbc-source.conf` | ✅ 支持 |
| **SQL Server** | `sqlserverreader` | `jdbc-source.conf` | ✅ 支持 |
| **HDFS** | `hdfsreader` | `hdfs-source.conf` | 支持 |

### 数据目标（Sinks）

| 数据目标类型 | DataX Writer | 模板文件 | 支持状态 |
|-------------|-------------|----------|----------|
| **MySQL** | `mysqlwriter` | `jdbc-sink.conf` | ✅ 支持 |
| **PostgreSQL** | `postgresqlwriter` | `jdbc-sink.conf` | ✅ 支持 |
| **Oracle** | `oraclewriter` | `jdbc-sink.conf` | ✅ 支持 |
| **SQL Server** | `sqlserverwriter` | `jdbc-sink.conf` | ✅ 支持 |
| **HDFS** | `hdfswriter` | `hdfs-sink.conf` | ✅ 支持 |


## 开发指南
### 自定义配置模板

可以在 `templates/datax/custom/` 目录下自定义配置模板，参考现有模板的格式和占位符语法。

### 代码结构

```
src/main/java/org/apache/seatunnel/tools/x2seatunnel/
├── cli/                    # 命令行界面
├── core/                   # 核心转换逻辑
├── template/               # 模板处理
├── utils/                  # 工具类
└── X2SeaTunnelApplication.java  # 主应用类
```

### 限制和注意事项
#### 版本兼容性
- 支持 DataX 主流版本的配置格式
- 生成的配置兼容 SeaTunnel 2.3.11+ 版本，旧版本大部分差异不大
- 模板系统向后兼容

### 更新日志

#### v1.0.0-SNAPSHOT (当前版本)
- ✅ **核心功能**：支持DataX到SeaTunnel的基础配置转换
- ✅ **模板系统**：基于Jinja2风格的DSL模板语言，支持配置驱动扩展
- ✅ **JDBC统一支持**：MySQL、PostgreSQL、Oracle、SQL Server等关系型数据库
- ✅ **智能特性**：
  - 自动驱动映射（根据jdbcUrl推断数据库驱动）
  - 智能查询生成（根据column、table、where自动拼接SELECT语句）
  - 参数自动映射（splitPk→partition_column、fetchSize→fetch_size等）
- ✅ **模板语法**：
  - 基础变量访问：`{{ datax.path.to.value }}`
  - 过滤器支持：`{{ array | join(',') }}`、`{{ value | default('default') }}`
  - 自定义转换器：`{{ url | jdbc_driver_mapper }}`
- ✅ **批量处理**：支持目录级别的批量转换和报告生成
- ✅ **完整示例**：提供4种JDBC数据源的完整DataX配置样例
- ✅ **详细文档**：完整的使用说明和API文档

---

# 附录1：X2SeaTunnel 转换报告样例

## 📋 Basic Information

| Item | Value |
|------|----|
| **Conversion Time** | 2025-08-04T14:01:00.628 |
| **Source File** | `examples/source/datax-mysql2hdfs.json` |
| **Target File** | `examples/target/mysql2hdfs-result2.conf` |
| **Source Type** | DATAX |
| **Target Type** | SeaTunnel |
| **Source Connector** | Jdbc (mysql) |
| **Target Connector** | HdfsFile |
| **Conversion Status** | ✅ Success |

| **Tool Version** | 0.1 |



## 📊 Conversion Statistics

| Type | Count | Percentage |
|------|------|--------|
| ✅ **Direct Mapping** | 16 | 57.1% |
| 🔧 **Transform Mapping** | 2 | 7.1% |
| 🔄 **Default Values Used** | 8 | 28.6% |
| ❌ **Missing Fields** | 0 | 0.0% |
| ⚠️ **Unmapped** | 2 | 7.1% |
| **Total** | 28 | 100% |

## ✅ Direct Mapped Fields

| SeaTunnel Field | Value | DATAX Source Field |
|---------------|----|--------------|
| `env.parallelism` | `3` | `null` |
| `source.Jdbc.url` | `jdbc:mysql://localhost:3306/testdb` | `job.content[0].reader.parameter.connection[0].jdbcUrl[0]` |
| `source.Jdbc.driver` | `jdbc:mysql://localhost:3306/testdb` | `job.content[0].reader.parameter.connection[0].jdbcUrl[0]` |
| `source.Jdbc.user` | `root` | `job.content[0].reader.parameter.username` |
| `source.Jdbc.password` | `1234567` | `job.content[0].reader.parameter.password` |
| `source.Jdbc.partition_column` | `id` | `null` |
| `source.Jdbc.partition_num` | `3` | `null` |
| `sink.HdfsFile.fs.defaultFS` | `hdfs://localhost:9000` | `job.content[0].writer.parameter.defaultFS` |
| `sink.HdfsFile.path` | `/data/users` | `job.content[0].writer.parameter.path` |
| `sink.HdfsFile.file_format_type` | `text` | `null` |
| `sink.HdfsFile.field_delimiter` | `	` | `null` |
| `sink.HdfsFile.row_delimiter` | `
` | `null` |
| `sink.HdfsFile.compress_codec` | `gzip` | `job.content[0].writer.parameter.compress` |
| `sink.HdfsFile.compress_codec` | `gzip` | `null` |
| `sink.HdfsFile.encoding` | `UTF-8` | `null` |
| `sink.HdfsFile.batch_size` | `50000` | `null` |


## 🔧 Transform Mapped Fields

| SeaTunnel Field | Value | DATAX Source Field | Filter Used |
|---------------|----|--------------|-----------|
| `source.Jdbc.driver` | `com.mysql.cj.jdbc.Driver` | `null` | jdbc_driver_mapper |
| `source.Jdbc.query` | `SELECT id,name,age,email,create_time FROM users WHERE 1=1` | `{{ datax.job.content[0].reader.parameter.querySql[0] \| default('SELECT') }} {{ datax.job.content[0].reader.parameter.column \| join(',') }} FROM {{ datax.job.content[0].reader.parameter.connection[0].table[0] }} WHERE {{ datax.job.content[0].reader.parameter.where \| default('1=1') }}` | default, join |


## 🔄 Fields Using Default Values

| SeaTunnel Field | Default Value |
|---------------|--------|
| `env.job.mode` | `BATCH` |
| `source.Jdbc.connection_check_timeout_sec` | `60` |
| `source.Jdbc.max_retries` | `3` |
| `source.Jdbc.fetch_size` | `1024` |
| `source.Jdbc.plugin_output` | `jdbc_source_table` |
| `sink.HdfsFile.tmp_path` | `/tmp/seatunnel` |
| `sink.HdfsFile.is_enable_transaction` | `true` |
| `sink.HdfsFile.enable_header_write` | `false` |


## ❌ Missing Fields

*No missing fields* 🎉


## ⚠️ Unmapped Fields

| DataX Field | Value |
|--------|------|
| `job.content[0].writer.parameter.fileName` | `users_export_${now}` |
| `job.content[0].writer.parameter.writeMode` | `append` |


# 附录2: 批量汇总报告样例

## 📋 Conversion Overview

| Item | Value |
|------|-------|
| **Start Time** | 2025-08-04 14:53:35 |
| **End Time** | 2025-08-04 14:53:36 |
| **Duration** | 1 seconds |
| **Source Directory** | `examples/source` |
| **Output Directory** | `examples/target2` |
| **Report Directory** | `examples/report2` |
| **File Pattern** | `*.json` |
| **Custom Template** | `Default template` |
| **Successful Conversions** | 10 files |
| **Failed Conversions** | 0 files |
| **Total** | 10 files |
| **Success Rate** | 100.0% |

## ✅ Successful Conversions (10)

| # | Source File | Target File | Report File |
|---|-------------|-------------|-------------|
| 1 | `examples/source/datax-hdfs2mysql.json` | `examples/target2/datax-hdfs2mysql.conf` | `examples/report2/datax-hdfs2mysql.md` |
| 2 | `examples/source/datax-mysql2hdfs-full.json` | `examples/target2/datax-mysql2hdfs-full.conf` | `examples/report2/datax-mysql2hdfs-full.md` |
| 3 | `examples/source/datax-mysql2hdfs.json` | `examples/target2/datax-mysql2hdfs.conf` | `examples/report2/datax-mysql2hdfs.md` |
| 4 | `examples/source/datax-mysql2hdfs2hive.json` | `examples/target2/datax-mysql2hdfs2hive.conf` | `examples/report2/datax-mysql2hdfs2hive.md` |
| 5 | `examples/source/datax-mysql2mysql-full.json` | `examples/target2/datax-mysql2mysql-full.conf` | `examples/report2/datax-mysql2mysql-full.md` |
| 6 | `examples/source/datax-mysql2mysql.json` | `examples/target2/datax-mysql2mysql.conf` | `examples/report2/datax-mysql2mysql.md` |
| 7 | `examples/source/datax-oracle2hdfs-full.json` | `examples/target2/datax-oracle2hdfs-full.conf` | `examples/report2/datax-oracle2hdfs-full.md` |
| 8 | `examples/source/datax-postgresql2hdfs-full.json` | `examples/target2/datax-postgresql2hdfs-full.conf` | `examples/report2/datax-postgresql2hdfs-full.md` |
| 9 | `examples/source/datax-postgresql2hdfs.json` | `examples/target2/datax-postgresql2hdfs.conf` | `examples/report2/datax-postgresql2hdfs.md` |
| 10 | `examples/source/datax-sqlserver2hdfs-full.json` | `examples/target2/datax-sqlserver2hdfs-full.conf` | `examples/report2/datax-sqlserver2hdfs-full.md` |

## ❌ Failed Conversions (0)

*No failed conversion files*

---
*Report generated at: 2025-08-04 14:53:36*
*Tool version: X2SeaTunnel v0.1*
