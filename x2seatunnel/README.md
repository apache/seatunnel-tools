# X2SeaTunnel Configuration Conversion Tool

X2SeaTunnel is a tool for converting DataX and other configuration files to SeaTunnel configuration files, designed to help users quickly migrate from other data integration platforms to SeaTunnel.

## 🚀 Quick Start

### Prerequisites

- Java 8 or higher

### Installation

#### Build from Source
```bash
# Build x2seatunnel module in this repository
mvn clean package -pl x2seatunnel -DskipTests
```
After compilation, the release package will be at `x2seatunnel/target/x2seatunnel-*.zip`.

#### Using Release Package
```bash
# Download and extract release package
unzip x2seatunnel-*.zip
cd x2seatunnel-*/
```

### Basic Usage

```bash
# Standard conversion: Use default template system with built-in common Sources and Sinks
./bin/x2seatunnel.sh -s examples/source/datax-mysql2hdfs.json -t examples/target/mysql2hdfs-result.conf -r examples/report/mysql2hdfs-report.md

# Custom task: Implement customized conversion requirements through custom templates
# Scenario: MySQL → Hive (DataX doesn't have HiveWriter)
# DataX configuration: MySQL → HDFS Custom task: Convert to MySQL → Hive
./bin/x2seatunnel.sh -s examples/source/datax-mysql2hdfs2hive.json -t examples/target/mysql2hive-result.conf -r examples/report/mysql2hive-report.md -T templates/datax/custom/mysql-to-hive.conf

# YAML configuration method (equivalent to above command line parameters)
./bin/x2seatunnel.sh -c examples/yaml/datax-mysql2hdfs2hive.yaml

# Batch conversion mode: Process by directory
./bin/x2seatunnel.sh -d examples/source -o examples/target2 -R examples/report2

# Batch mode supports wildcard filtering
./bin/x2seatunnel.sh -d examples/source -o examples/target3 -R examples/report3 --pattern "*-full.json" --verbose

# View help
./bin/x2seatunnel.sh --help
```

### Conversion Report
After conversion is completed, view the generated Markdown report file, which includes:
- **Basic Information**: Conversion time, source/target file paths, connector types, conversion status, etc.
- **Conversion Statistics**: Counts and percentages of direct mappings, smart transformations, default values used, and unmapped fields
- **Detailed Field Mapping Relationships**: Source values, target values, filters used for each field
- **Default Value Usage**: List of all fields using default values
- **Unmapped Fields**: Fields present in DataX but not converted
- **Possible Error and Warning Information**: Issue prompts during conversion process

For batch conversions, a batch summary report `summary.md` will be generated in the batch report directory, including:
- **Conversion Overview**: Overall statistics, success rate, duration, etc.
- **Successful Conversion List**: Complete list of successfully converted files
- **Failed Conversion List**: Failed files and error messages (if any)

### Log Files
```bash
# View log files
tail -f logs/x2seatunnel.log
```

## 🎯 Features

- ✅ **Standard Configuration Conversion**: DataX → SeaTunnel configuration file conversion
- ✅ **Custom Template Conversion**: Support for user-defined conversion templates
- ✅ **Detailed Conversion Reports**: Generate Markdown format conversion reports
- ✅ **Regular Expression Variable Extraction**: Extract variables from configuration using regex, supporting custom scenarios
- ✅ **Batch Conversion Mode**: Support directory and file wildcard batch conversion, automatic report and summary report generation

## 📁 Directory Structure

```
x2seatunnel/
├── bin/                        # Executable files
│   ├── x2seatunnel.sh         # Startup script
├── lib/                        # JAR package files
│   └── x2seatunnel-*.jar      # Core JAR package
├── config/                     # Configuration files
│   └── log4j2.xml             # Log configuration
├── templates/                  # Template files
│   ├── template-mapping.yaml  # Template mapping configuration
│   ├── report-template.md     # Report template
│   └── datax/                 # DataX related templates
│       ├── custom/            # Custom templates
│       ├── env/               # Environment configuration templates
│       ├── sources/           # Data source templates
│       └── sinks/             # Data target templates
├── examples/                   # Examples and tests
│   ├── source/                # Example source files
│   ├── target/                # Generated target files
│   └── report/                # Generated reports
├── logs/                       # Log files
├── LICENSE                     # License
└── README.md                   # Usage instructions
```

## 📖 Usage Instructions

### Basic Syntax

```bash
x2seatunnel [OPTIONS]
```

### Command Line Parameters

| Option   | Long Option     | Description                                                 | Required |
|----------|-----------------|-------------------------------------------------------------|----------|
| -s       | --source        | Source configuration file path                              | Yes      |
| -t       | --target        | Target configuration file path                              | Yes      |
| -st      | --source-type   | Source configuration type (datax, default: datax)          | No       |
| -T       | --template      | Custom template file path                                   | No       |
| -r       | --report        | Conversion report file path                                 | No       |
| -c       | --config        | YAML configuration file path, containing source, target, report, template and other settings | No |
| -d       | --directory     | Batch conversion source directory                           | No       |
| -o       | --output-dir    | Batch conversion output directory                           | No       |
| -p       | --pattern       | File wildcard pattern (comma separated, e.g.: *.json,*.xml)| No       |
| -R       | --report-dir    | Report output directory in batch mode, individual file reports and summary.md will be output to this directory | No |
| -v       | --version       | Show version information                                    | No       |
| -h       | --help          | Show help information                                       | No       |
|          | --verbose       | Enable verbose log output                                   | No       |

```bash
# Example: View command line help
./bin/x2seatunnel.sh --help
```

### Supported Configuration Types

#### Source Configuration Types
- **datax**: DataX configuration files (JSON format) - Default type

#### Target Configuration Types
- **seatunnel**: SeaTunnel configuration files (HOCON format)

## 🎨 Template System

### Design Philosophy

X2SeaTunnel adopts a DSL (Domain Specific Language) based template system, implementing rapid adaptation of different data sources and targets through configuration-driven approach. Core advantages:

- **Configuration-driven**: All conversion logic is defined through YAML configuration files, no need to modify Java code
- **Easy to extend**: Adding new data source types only requires adding template files and mapping configurations
- **Unified syntax**: Uses Jinja2-style template syntax, easy to understand and maintain
- **Intelligent mapping**: Implements complex parameter mapping logic through transformers

### Template Syntax

X2SeaTunnel supports partially compatible Jinja2-style template syntax, providing rich filter functionality to handle configuration conversion.

```bash
# Basic variable reference
{{ datax.job.content[0].reader.parameter.username }}

# Variables with filters
{{ datax.job.content[0].reader.parameter.column | join(',') }}

# Chained filters
{{ datax.job.content[0].writer.parameter.path | split('/') | get(-2) | replace('.db','') }}
```

### 2. Filters

| Filter | Syntax | Description | Example |
|--------|--------|-------------|---------|
| `join` | `{{ array \| join('separator') }}` | Array join | `{{ columns \| join(',') }}` |
| `default` | `{{ value \| default('default_value') }}` | Default value | `{{ port \| default(3306) }}` |
| `upper` | `{{ value \| upper }}` | Uppercase conversion | `{{ name \| upper }}` |
| `lower` | `{{ value \| lower }}` | Lowercase conversion | `{{ name \| lower }}` |
| `split` | `{{ string \| split('/') }}` | String split | `'a/b/c' → ['a','b','c']` |
| `get` | `{{ array \| get(0) }}` | Get array element | `['a','b','c'] → 'a'` |
| `replace` | `{{ string \| replace('old,new') }}` | String replace | `'hello' → 'hallo'` |
| `regex_extract` | `{{ string \| regex_extract('pattern') }}` | Regex extract | Extract matching content |
| `jdbc_driver_mapper` | `{{ jdbcUrl \| jdbc_driver_mapper }}` | JDBC driver mapping | Auto infer driver class |

### 3. Examples

```bash
# join filter: Array join
query = "SELECT {{ datax.job.content[0].reader.parameter.column | join(',') }} FROM table"

# default filter: Default value
partition_column = "{{ datax.job.content[0].reader.parameter.splitPk | default('') }}"
fetch_size = {{ datax.job.content[0].reader.parameter.fetchSize | default(1024) }}

# String operations
driver = "{{ datax.job.content[0].reader.parameter.connection[0].jdbcUrl[0] | upper }}"
```

```bash
# Chained filters: String split and get
{{ datax.job.content[0].writer.parameter.path | split('/') | get(-2) | replace('.db','') }}

# Regular expression extraction
{{ jdbcUrl | regex_extract('jdbc:mysql://([^:]+):') }}

# Transformer call: Intelligent parameter mapping
driver = "{{ datax.job.content[0].reader.parameter.connection[0].jdbcUrl[0] | jdbc_driver_mapper }}"
```

```bash
# Intelligent query generation
query = "{{ datax.job.content[0].reader.parameter.querySql[0] | default('SELECT') }} {{ datax.job.content[0].reader.parameter.column | join(',') }} FROM {{ datax.job.content[0].reader.parameter.connection[0].table[0] }} WHERE {{ datax.job.content[0].reader.parameter.where | default('1=1') }}"

# Path intelligent parsing: Extract Hive table name from HDFS path
# Path: /user/hive/warehouse/test_ods.db/test_table/partition=20240101
database = "{{ datax.job.content[0].writer.parameter.path | split('/') | get(-3) | replace('.db','') }}"
table = "{{ datax.job.content[0].writer.parameter.path | split('/') | get(-2) }}"
table_name = "{{ database }}.{{ table }}"
```

```bash
# Auto infer database driver
{{ datax.job.content[0].reader.parameter.connection[0].jdbcUrl[0] | jdbc_driver_mapper }}

# Mapping relationships (configured in template-mapping.yaml):
# mysql -> com.mysql.cj.jdbc.Driver
# postgresql -> org.postgresql.Driver
# oracle -> oracle.jdbc.driver.OracleDriver
# sqlserver -> com.microsoft.sqlserver.jdbc.SQLServerDriver
```

### Custom Transformers

Configure custom transformers through `templates/template-mapping.yaml`:

```yaml
transformers:
  # JDBC driver mapping
  jdbc_driver_mapper:
    mysql: "com.mysql.cj.jdbc.Driver"
    postgresql: "org.postgresql.Driver"
    oracle: "oracle.jdbc.driver.OracleDriver"
    sqlserver: "com.microsoft.sqlserver.jdbc.SQLServerDriver"

  # File format mapping
  file_format_mapper:
    text: "text"
    orc: "orc"
    parquet: "parquet"
    json: "json"
```

## Extending New Data Sources

Adding new data source types requires only three steps:

1. **Create template files**: Create new template files under `templates/datax/sources/`
2. **Configure mapping relationships**: Add mapping configurations in `template-mapping.yaml`
3. **Add transformers**: If special processing is needed, add corresponding transformer configurations

No need to modify any Java code to support new data source types.

## 🌐 Supported Data Sources and Targets

### Data Sources (Sources)

| Data Source Type | DataX Reader | Template File | Support Status |
|------------------|-------------|---------------|----------------|
| **MySQL** | `mysqlreader` | `mysql-source.conf` | ✅ Support |
| **PostgreSQL** | `postgresqlreader` | `jdbc-source.conf` | ✅ Support |
| **Oracle** | `oraclereader` | `jdbc-source.conf` | ✅ Support |
| **SQL Server** | `sqlserverreader` | `jdbc-source.conf` | ✅ Support |
| **HDFS** | `hdfsreader` | `hdfs-source.conf` | ✅ Support |

### Data Targets (Sinks)

| Data Target Type | DataX Writer | Template File | Support Status |
|------------------|-------------|---------------|----------------|
| **MySQL** | `mysqlwriter` | `jdbc-sink.conf` | ✅ Support |
| **PostgreSQL** | `postgresqlwriter` | `jdbc-sink.conf` | ✅ Support |
| **Oracle** | `oraclewriter` | `jdbc-sink.conf` | ✅ Support |
| **SQL Server** | `sqlserverwriter` | `jdbc-sink.conf` | ✅ Support |
| **HDFS** | `hdfswriter` | `hdfs-sink.conf` | ✅ Support |
| **Doris** | `doriswriter` | `doris-sink.conf` | 📋 Planned |

## Development Guide

### Custom Configuration Templates

You can customize configuration templates in the `templates/datax/custom/` directory, referring to the format and placeholder syntax of existing templates.

### Code Structure

```
src/main/java/org/apache/seatunnel/tools/x2seatunnel/
├── cli/                    # Command line interface
├── core/                   # Core conversion logic
├── template/               # Template processing
├── utils/                  # Utility classes
└── X2SeaTunnelApplication.java  # Main application class
```

### Changelog

#### v1.0.0-SNAPSHOT (Current Version)
- ✅ **Core Features**: Support for basic DataX to SeaTunnel configuration conversion
- ✅ **Template System**: Jinja2-style DSL template language with configuration-driven extension support
- ✅ **Unified JDBC Support**: MySQL, PostgreSQL, Oracle, SQL Server and other relational databases
- ✅ **Intelligent Features**:
  - Auto driver mapping (infer database driver based on jdbcUrl)
  - Intelligent query generation (auto-generate SELECT statements based on column, table, where)
  - Auto parameter mapping (splitPk→partition_column, fetchSize→fetch_size, etc.)
- ✅ **Template Syntax**:
  - Basic variable access: `{{ datax.path.to.value }}`
  - Filter support: `{{ array | join(',') }}`, `{{ value | default('default') }}`
  - Custom transformers: `{{ url | jdbc_driver_mapper }}`
- ✅ **Batch Processing**: Support directory-level batch conversion and report generation
- ✅ **Complete Examples**: Complete DataX configuration examples for 4 JDBC data sources
- ✅ **Comprehensive Documentation**: Complete usage instructions and API documentation

# Appendix 1: X2SeaTunnel Conversion Report

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

# Appendix 2: Batch Conversion Report

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
