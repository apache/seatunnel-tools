# Apache SeaTunnel 工具集

[English](README.md) | **中文**

Apache SeaTunnel 辅助工具集，重点关注开发者/运维生产力，包括配置转换、LLM 集成、打包和诊断。

## 🎯 工具概览

| 工具 | 用途 | 状态 |
|------|------|------|
| **SeaTunnel Skill** | Claude AI 集成 | ✅ 新功能 |
| **SeaTunnel MCP 服务** | LLM 集成协议 | ✅ 可用 |
| **x2seatunnel** | 配置转换工具 (DataX → SeaTunnel) | ✅ 可用 |

---

## ⚡ 快速开始

### SeaTunnel Skill (Claude Code 集成)

**安装步骤：**

```bash
# 1. 克隆本仓库
git clone https://github.com/apache/seatunnel-tools.git
cd seatunnel-tools

# 2. 复制 seatunnel-skill 到 Claude Code 技能目录
cp -r seatunnel-skill ~/.claude/skills/

# 3. 重启 Claude Code 或重新加载技能
# 然后使用: /seatunnel-skill "你的问题"
```

**快速示例：**

```bash
# 查询 SeaTunnel 文档
/seatunnel-skill "如何配置 MySQL 到 PostgreSQL 的数据同步？"

# 获取连接器信息
/seatunnel-skill "列出所有可用的 Kafka 连接器选项"

# 调试配置问题
/seatunnel-skill "为什么我的任务出现 OutOfMemoryError 错误？"
```

### SeaTunnel 核心引擎（直接安装）

```bash
# 下载二进制文件（推荐）
wget https://archive.apache.org/dist/seatunnel/2.3.12/apache-seatunnel-2.3.12-bin.tar.gz
tar -xzf apache-seatunnel-2.3.12-bin.tar.gz
cd apache-seatunnel-2.3.12

# 验证安装
./bin/seatunnel.sh --version

# 运行第一个任务
./bin/seatunnel.sh -c config/hello_world.conf -e spark
```

---

## 📋 功能概览

### SeaTunnel Skill
- 🤖 **AI 助手**: 获得 SeaTunnel 概念和配置的即时帮助
- 📚 **知识集成**: 查询官方文档和最佳实践
- 🔍 **智能调试**: 分析错误并提出修复建议
- 💡 **代码示例**: 为您的用例生成配置示例

### SeaTunnel 核心引擎
- **多模式支持**: 结构化、非结构化和半结构化数据
- **100+ 连接器**: 数据库、数据仓库、云服务、消息队列
- **多引擎支持**: Zeta（轻量级）、Spark、Flink
- **同步模式**: 批处理、流处理、CDC（变更数据捕获）
- **实时性能**: 每秒 100K - 1M 条记录吞吐量

---

## 🔧 安装与设置

### 方法 1: SeaTunnel Skill (AI 集成)

**第一步：复制技能文件**
```bash
mkdir -p ~/.claude/skills
cp -r seatunnel-skill ~/.claude/skills/
```

**第二步：验证安装**
```bash
# 在 Claude Code 中尝试：
/seatunnel-skill "什么是 SeaTunnel？"
```

**第三步：开始使用**
```bash
# 帮助配置
/seatunnel-skill "创建一个 MySQL 到 Elasticsearch 的任务配置"

# 故障排除
/seatunnel-skill "我的 Kafka 连接器一直超时"

# 学习功能
/seatunnel-skill "在 SeaTunnel 中解释 CDC（变更数据捕获）"
```

### 方法 2: 二进制安装

**支持平台**: Linux、macOS、Windows

```bash
# 下载最新版本
VERSION=2.3.12
wget https://archive.apache.org/dist/seatunnel/${VERSION}/apache-seatunnel-${VERSION}-bin.tar.gz

# 解压
tar -xzf apache-seatunnel-${VERSION}-bin.tar.gz
cd apache-seatunnel-${VERSION}

# 设置环境
export JAVA_HOME=/path/to/java
export PATH=$PATH:$(pwd)/bin

# 验证
seatunnel.sh --version
```

### 方法 3: 从源代码构建

```bash
# 克隆仓库
git clone https://github.com/apache/seatunnel.git
cd seatunnel

# 构建
mvn clean install -DskipTests

# 从分发目录运行
cd seatunnel-dist/target/apache-seatunnel-*-bin/apache-seatunnel-*
./bin/seatunnel.sh --version
```

### 方法 4: Docker

```bash
# 拉取官方镜像
docker pull apache/seatunnel:latest

# 运行容器
docker run -it apache/seatunnel:latest /bin/bash

# 直接运行任务
docker run -v /path/to/config:/config \
  apache/seatunnel:latest \
  seatunnel.sh -c /config/job.conf -e spark
```

---

## 💻 使用指南

### 用例 1: MySQL 到 PostgreSQL（批处理）

**config/mysql_to_postgres.conf**
```hocon
env {
  job.mode = "BATCH"
  job.name = "MySQL 到 PostgreSQL"
}

source {
  Jdbc {
    driver = "com.mysql.cj.jdbc.Driver"
    url = "jdbc:mysql://mysql-host:3306/mydb"
    user = "root"
    password = "password"
    query = "SELECT * FROM users"
    connection_check_timeout_sec = 100
  }
}

sink {
  Jdbc {
    driver = "org.postgresql.Driver"
    url = "jdbc:postgresql://pg-host:5432/mydb"
    user = "postgres"
    password = "password"
    database = "mydb"
    table = "users"
    primary_keys = ["id"]
    connection_check_timeout_sec = 100
  }
}
```

**运行：**
```bash
seatunnel.sh -c config/mysql_to_postgres.conf -e spark
```

### 用例 2: Kafka 流到 Elasticsearch

**config/kafka_to_es.conf**
```hocon
env {
  job.mode = "STREAMING"
  job.name = "Kafka 到 Elasticsearch"
  parallelism = 2
}

source {
  Kafka {
    bootstrap.servers = "kafka-host:9092"
    topic = "events"
    consumer.group = "seatunnel-group"
    format = "json"
    schema = {
      fields {
        event_id = "bigint"
        event_name = "string"
        timestamp = "bigint"
      }
    }
  }
}

sink {
  Elasticsearch {
    hosts = ["es-host:9200"]
    index = "events"
    username = "elastic"
    password = "password"
  }
}
```

**运行：**
```bash
seatunnel.sh -c config/kafka_to_es.conf -e flink
```

### 用例 3: MySQL CDC 到 Kafka

**config/mysql_cdc_kafka.conf**
```hocon
env {
  job.mode = "STREAMING"
  job.name = "MySQL CDC 到 Kafka"
}

source {
  Mysql {
    server_id = 5400
    hostname = "mysql-host"
    port = 3306
    username = "root"
    password = "password"
    database = ["mydb"]
    table = ["users", "orders"]
    startup.mode = "initial"
  }
}

sink {
  Kafka {
    bootstrap.servers = "kafka-host:9092"
    topic = "mysql_cdc"
    format = "canal_json"
    semantic = "EXACTLY_ONCE"
  }
}
```

**运行：**
```bash
seatunnel.sh -c config/mysql_cdc_kafka.conf -e flink
```

---

## 📚 API 参考

### 核心连接器类型

**源连接器**
- `Jdbc` - 通用 JDBC 数据库（MySQL、PostgreSQL、Oracle、SQL Server）
- `Kafka` - Apache Kafka 主题
- `Mysql` - 支持 CDC 的 MySQL
- `MongoDB` - MongoDB 集合
- `PostgreSQL` - 支持 CDC 的 PostgreSQL
- `S3` - Amazon S3 和兼容存储
- `Http` - HTTP/HTTPS 端点
- `FakeSource` - 用于测试

**宿连接器**
- `Jdbc` - 写入 JDBC 兼容数据库
- `Kafka` - 发布到 Kafka 主题
- `Elasticsearch` - 写入 Elasticsearch 索引
- `S3` - 写入 S3 存储桶
- `Redis` - 写入 Redis
- `HBase` - 写入 HBase 表
- `Console` - 输出到控制台

**转换连接器**
- `Sql` - 执行 SQL 转换
- `FieldMapper` - 列重命名/映射
- `JsonPath` - 从 JSON 提取数据

---

## ⚙️ 配置与优化

### 环境变量

```bash
# Java 配置
export JAVA_HOME=/path/to/java
export JVM_OPTS="-Xms1G -Xmx4G"

# Spark 配置（使用 Spark 引擎时）
export SPARK_HOME=/path/to/spark
export SPARK_MASTER=spark://master:7077

# Flink 配置（使用 Flink 引擎时）
export FLINK_HOME=/path/to/flink

# SeaTunnel 配置
export SEATUNNEL_HOME=/path/to/seatunnel
```

### 批处理任务性能调优

```hocon
env {
  job.mode = "BATCH"
  parallelism = 8  # 根据集群大小增加
}

source {
  Jdbc {
    split_size = 100000    # 并行读取
    fetch_size = 5000
  }
}

sink {
  Jdbc {
    batch_size = 1000      # 批量插入
    max_retries = 3
  }
}
```

### 流处理任务性能调优

```hocon
env {
  job.mode = "STREAMING"
  parallelism = 4
  checkpoint.interval = 30000  # 30 秒
}

source {
  Kafka {
    consumer.group = "seatunnel-consumer"
    max_poll_records = 500
  }
}
```

---

## 🛠️ 开发指南

### 项目结构

```
seatunnel-tools/
├── seatunnel-skill/          # Claude Code AI 技能
├── seatunnel-mcp/            # LLM 集成 MCP 服务
├── x2seatunnel/              # DataX 到 SeaTunnel 转换器
└── README_CN.md
```

### SeaTunnel 核心架构

```
seatunnel/
├── seatunnel-api/            # 核心 API
├── seatunnel-core/           # 执行引擎
├── seatunnel-engines/        # 引擎实现
│   ├── seatunnel-engine-flink/
│   ├── seatunnel-engine-spark/
│   └── seatunnel-engine-zeta/
├── seatunnel-connectors/     # 连接器实现
└── seatunnel-dist/           # 分发包
```

### 从源代码构建 SeaTunnel

```bash
# 完整构建
git clone https://github.com/apache/seatunnel.git
cd seatunnel
mvn clean install -DskipTests

# 构建特定模块
mvn clean install -pl seatunnel-connectors/seatunnel-connectors-seatunnel-kafka -DskipTests
```

### 运行测试

```bash
# 单元测试
mvn test

# 特定测试类
mvn test -Dtest=MySqlConnectorTest

# 集成测试
mvn verify
```

---

## 🐛 故障排查（6 个常见问题）

### 问题 1: ClassNotFoundException: com.mysql.jdbc.Driver

**解决方案：**
```bash
wget https://dev.mysql.com/get/Downloads/Connector-J/mysql-connector-java-8.0.33.jar
cp mysql-connector-java-8.0.33.jar $SEATUNNEL_HOME/lib/
seatunnel.sh -c config/job.conf -e spark
```

### 问题 2: OutOfMemoryError: Java heap space

**解决方案：**
```bash
export JVM_OPTS="-Xms2G -Xmx8G"
echo 'JVM_OPTS="-Xms2G -Xmx8G"' >> $SEATUNNEL_HOME/bin/seatunnel-env.sh
```

### 问题 3: Connection refused: connect

**解决方案：**
```bash
# 验证连接
ping source-host
telnet source-host 3306

# 检查凭证
mysql -h source-host -u root -p
```

### 问题 4: CDC 期间找不到表

**解决方案：**
```sql
-- 检查二进制日志状态
SHOW VARIABLES LIKE 'log_bin';

-- 在 my.cnf 中启用二进制日志
[mysqld]
log_bin = mysql-bin
binlog_format = row
```

### 问题 5: 任务性能缓慢

**解决方案：**
```hocon
env {
  parallelism = 8  # 增加并行性
}

source {
  Jdbc {
    fetch_size = 5000
    split_size = 100000
  }
}

sink {
  Jdbc {
    batch_size = 2000
  }
}
```

### 问题 6: Kafka 偏移量超出范围

**解决方案：**
```hocon
source {
  Kafka {
    auto.offset.reset = "earliest"  # 或 "latest"
  }
}
```

---

## ❓ 常见问题（8 个常见问题）

**Q: BATCH 和 STREAMING 模式有什么区别？**

A:
- **BATCH**: 一次性执行，适合全量数据库迁移
- **STREAMING**: 持续执行，适合实时同步和 CDC

**Q: 如何在 CDC 期间处理架构更改？**

A: 在源中配置自动检测：
```hocon
source {
  Mysql {
    schema_change_mode = "auto"
  }
}
```

**Q: 我能在同步期间转换数据吗？**

A: 可以，使用 SQL 转换：
```hocon
transform {
  Sql {
    sql = "SELECT id, UPPER(name) as name FROM source"
  }
}
```

**Q: 最大吞吐量是多少？**

A: 典型吞吐量为每个执行器每秒 100K - 1M 条记录。取决于：
- 硬件（CPU、RAM、网络）
- 数据库配置
- 每条记录的数据大小
- 网络延迟

**Q: 如何在生产环境中处理错误？**

A: 配置重启策略：
```hocon
env {
  restart_strategy = "exponential_delay"
  restart_strategy.exponential_delay.initial_delay = 1000
  restart_strategy.exponential_delay.max_delay = 30000
  restart_strategy.exponential_delay.multiplier = 2.0
}
```

**Q: 是否有用于任务管理的 Web UI？**

A: 是的！使用 SeaTunnel Web 项目：
```bash
git clone https://github.com/apache/seatunnel-web.git
cd seatunnel-web
mvn clean install
java -jar target/seatunnel-web-*.jar
# 访问 http://localhost:8080
```

**Q: 如何在 Claude Code 中使用 SeaTunnel Skill？**

A: 复制到 `~/.claude/skills/` 后，使用：
```bash
/seatunnel-skill "关于 SeaTunnel 的问题"
```

**Q: 应该使用哪个引擎：Spark、Flink 还是 Zeta？**

A:
- **Zeta**: 轻量级，无外部依赖，单机
- **Spark**: 分布式集群的批处理和批流混合
- **Flink**: 分布式集群的高级流处理和 CDC

---

## 🔗 资源与链接

### 官方文档
- [SeaTunnel 官网](https://seatunnel.apache.org/)
- [GitHub 仓库](https://github.com/apache/seatunnel)
- [连接器列表](https://seatunnel.apache.org/docs/2.3.12/connector-v2/overview)
- [HOCON 配置指南](https://github.com/lightbend/config/blob/main/HOCON.md)

### 社区与支持
- [Slack 频道](https://the-asf.slack.com/archives/C01CB5186TL)
- [邮件列表](https://seatunnel.apache.org/community/mail-lists/)
- [GitHub Issues](https://github.com/apache/seatunnel/issues)
- [讨论论坛](https://github.com/apache/seatunnel/discussions)

### 相关项目
- [SeaTunnel Web UI](https://github.com/apache/seatunnel-web)
- [SeaTunnel 工具集](https://github.com/apache/seatunnel-tools)
- [Apache Kafka](https://kafka.apache.org/)
- [Apache Flink](https://flink.apache.org/)
- [Apache Spark](https://spark.apache.org/)

---

## 📄 单个工具说明

### 1. SeaTunnel Skill（新功能）
- **用途**: Claude Code 中 SeaTunnel 的 AI 助手
- **位置**: [seatunnel-skill/](seatunnel-skill/)
- **快速设置**: `cp -r seatunnel-skill ~/.claude/skills/`
- **使用方法**: `/seatunnel-skill "你的问题"`

### 2. SeaTunnel MCP 服务
- **用途**: LLM 系统的模型上下文协议集成
- **位置**: [seatunnel-mcp/](seatunnel-mcp/)
- **英文**: [README.md](seatunnel-mcp/README.md)
- **中文**: [README_CN.md](seatunnel-mcp/README_CN.md)
- **快速开始**: [QUICK_START.md](seatunnel-mcp/docs/QUICK_START.md)

### 3. x2seatunnel
- **用途**: 将 DataX 等配置转换为 SeaTunnel 格式
- **位置**: [x2seatunnel/](x2seatunnel/)
- **英文**: [README.md](x2seatunnel/README.md)
- **中文**: [README_zh.md](x2seatunnel/README_zh.md)

---

## 🤝 贡献

欢迎提交 Issues 和 Pull Requests！

对于主要的 SeaTunnel 引擎，请参阅 [Apache SeaTunnel](https://github.com/apache/seatunnel)。

对于这些工具，请贡献到 [SeaTunnel 工具集](https://github.com/apache/seatunnel-tools)。

---

**最后更新**: 2026-01-28 | **许可证**: Apache 2.0