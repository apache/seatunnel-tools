# Apache SeaTunnel Tools

**English** | [中文](README_CN.md)

Auxiliary tools for Apache SeaTunnel focusing on developer/operator productivity around configuration, conversion, LLM integration, packaging, and diagnostics.

## 🎯 What's Inside

| Tool | Purpose | Status |
|------|---------|--------|
| **SeaTunnel Skill** | Claude AI integration for SeaTunnel operations | ✅ New |
| **SeaTunnel MCP Server** | Model Context Protocol for LLM integration | ✅ Available |
| **x2seatunnel** | Configuration converter (DataX → SeaTunnel) | ✅ Available |

---

## ⚡ Quick Start

### For SeaTunnel Skill (Claude Code Integration)

**Installation & Setup:**

```bash
# 1. Clone this repository
git clone https://github.com/apache/seatunnel-tools.git
cd seatunnel-tools

# 2. Copy seatunnel-skill to Claude Code skills directory
cp -r seatunnel-skill ~/.claude/skills/

# 3. Restart Claude Code or reload skills
# Then use: /seatunnel-skill "your prompt here"
```

**Quick Example:**

```bash
# Query SeaTunnel documentation
/seatunnel-skill "How do I configure a MySQL to PostgreSQL job?"

# Get connector information
/seatunnel-skill "List all available Kafka connector options"

# Debug configuration issues
/seatunnel-skill "Why is my job failing with OutOfMemoryError?"
```

### For SeaTunnel Core (Direct Installation)

```bash
# Download binary (recommended)
wget https://archive.apache.org/dist/seatunnel/2.3.12/apache-seatunnel-2.3.12-bin.tar.gz
tar -xzf apache-seatunnel-2.3.12-bin.tar.gz
cd apache-seatunnel-2.3.12

# Verify installation
./bin/seatunnel.sh --version

# Run your first job
./bin/seatunnel.sh -c config/hello_world.conf -e spark
```

---

## 📋 Features Overview

### SeaTunnel Skill
- 🤖 **AI-Powered Assistant**: Get instant help with SeaTunnel concepts and configurations
- 📚 **Knowledge Integration**: Query official documentation and best practices
- 🔍 **Smart Debugging**: Analyze errors and suggest fixes
- 💡 **Code Examples**: Generate configuration examples for your use case

### SeaTunnel Core Engine
- **Multimodal Support**: Structured, unstructured, and semi-structured data
- **100+ Connectors**: Databases, data warehouses, cloud services, message queues
- **Multiple Engines**: Zeta (lightweight), Spark, Flink
- **Synchronization Modes**: Batch, Streaming, CDC (Change Data Capture)
- **Real-time Performance**: 100K - 1M records/second throughput

---

## 🔧 Installation & Setup

### Method 1: SeaTunnel Skill (AI Integration)

**Step 1: Copy Skill File**
```bash
mkdir -p ~/.claude/skills
cp -r seatunnel-skill ~/.claude/skills/
```

**Step 2: Verify Installation**
```bash
# In Claude Code, try:
/seatunnel-skill "What is SeaTunnel?"
```

**Step 3: Start Using**
```bash
# Help with configuration
/seatunnel-skill "Create a MySQL to Elasticsearch job config"

# Troubleshoot errors
/seatunnel-skill "My Kafka connector keeps timing out"

# Learn features
/seatunnel-skill "Explain CDC (Change Data Capture) in SeaTunnel"
```

### Method 2: SeaTunnel Binary Installation

**Supported Platforms**: Linux, macOS, Windows

```bash
# Download latest version
VERSION=2.3.12
wget https://archive.apache.org/dist/seatunnel/${VERSION}/apache-seatunnel-${VERSION}-bin.tar.gz

# Extract
tar -xzf apache-seatunnel-${VERSION}-bin.tar.gz
cd apache-seatunnel-${VERSION}

# Set environment
export JAVA_HOME=/path/to/java
export PATH=$PATH:$(pwd)/bin

# Verify
seatunnel.sh --version
```

### Method 3: Build from Source

```bash
# Clone repository
git clone https://github.com/apache/seatunnel.git
cd seatunnel

# Build
mvn clean install -DskipTests

# Run from distribution
cd seatunnel-dist/target/apache-seatunnel-*-bin/apache-seatunnel-*
./bin/seatunnel.sh --version
```

### Method 4: Docker

```bash
# Pull official image
docker pull apache/seatunnel:latest

# Run container
docker run -it apache/seatunnel:latest /bin/bash

# Run job directly
docker run -v /path/to/config:/config \
  apache/seatunnel:latest \
  seatunnel.sh -c /config/job.conf -e spark
```

---

## 💻 Usage Guide

### Use Case 1: MySQL to PostgreSQL (Batch)

**config/mysql_to_postgres.conf**
```hocon
env {
  job.mode = "BATCH"
  job.name = "MySQL to PostgreSQL"
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

**Run:**
```bash
seatunnel.sh -c config/mysql_to_postgres.conf -e spark
```

### Use Case 2: Kafka Streaming to Elasticsearch

**config/kafka_to_es.conf**
```hocon
env {
  job.mode = "STREAMING"
  job.name = "Kafka to Elasticsearch"
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

**Run:**
```bash
seatunnel.sh -c config/kafka_to_es.conf -e flink
```

### Use Case 3: MySQL CDC to Kafka

**config/mysql_cdc_kafka.conf**
```hocon
env {
  job.mode = "STREAMING"
  job.name = "MySQL CDC to Kafka"
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

**Run:**
```bash
seatunnel.sh -c config/mysql_cdc_kafka.conf -e flink
```

---

## 📚 API Reference

### Core Connector Types

**Source Connectors**
- `Jdbc` - Generic JDBC databases (MySQL, PostgreSQL, Oracle, SQL Server)
- `Kafka` - Apache Kafka topics
- `Mysql` - MySQL with CDC support
- `MongoDB` - MongoDB collections
- `PostgreSQL` - PostgreSQL with CDC
- `S3` - Amazon S3 and compatible storage
- `Http` - HTTP/HTTPS endpoints
- `FakeSource` - For testing

**Sink Connectors**
- `Jdbc` - Write to JDBC-compatible databases
- `Kafka` - Publish to Kafka topics
- `Elasticsearch` - Write to Elasticsearch indices
- `S3` - Write to S3 buckets
- `Redis` - Write to Redis
- `HBase` - Write to HBase tables
- `Console` - Output to console

**Transform Connectors**
- `Sql` - Execute SQL transformations
- `FieldMapper` - Rename/map columns
- `JsonPath` - Extract data from JSON

---

## ⚙️ Configuration & Tuning

### Environment Variables

```bash
# Java configuration
export JAVA_HOME=/path/to/java
export JVM_OPTS="-Xms1G -Xmx4G"

# Spark configuration (if using Spark engine)
export SPARK_HOME=/path/to/spark
export SPARK_MASTER=spark://master:7077

# Flink configuration (if using Flink engine)
export FLINK_HOME=/path/to/flink

# SeaTunnel configuration
export SEATUNNEL_HOME=/path/to/seatunnel
```

### Performance Tuning for Batch Jobs

```hocon
env {
  job.mode = "BATCH"
  parallelism = 8  # Increase for larger clusters
}

source {
  Jdbc {
    split_size = 100000    # Parallel reads
    fetch_size = 5000
  }
}

sink {
  Jdbc {
    batch_size = 1000      # Batch inserts
    max_retries = 3
  }
}
```

### Performance Tuning for Streaming Jobs

```hocon
env {
  job.mode = "STREAMING"
  parallelism = 4
  checkpoint.interval = 30000  # 30 seconds
}

source {
  Kafka {
    consumer.group = "seatunnel-consumer"
    max_poll_records = 500
  }
}
```

---

## 🛠️ Development Guide

### Project Structure

```
seatunnel-tools/
├── seatunnel-skill/          # Claude Code AI skill
├── seatunnel-mcp/            # MCP server for LLM integration
├── x2seatunnel/              # DataX to SeaTunnel converter
└── README.md
```

### SeaTunnel Core Architecture

```
seatunnel/
├── seatunnel-api/            # Core APIs
├── seatunnel-core/           # Execution engine
├── seatunnel-engines/        # Engine implementations
│   ├── seatunnel-engine-flink/
│   ├── seatunnel-engine-spark/
│   └── seatunnel-engine-zeta/
├── seatunnel-connectors/     # Connector implementations
└── seatunnel-dist/           # Distribution package
```

### Building SeaTunnel from Source

```bash
# Full build
git clone https://github.com/apache/seatunnel.git
cd seatunnel
mvn clean install -DskipTests

# Build specific module
mvn clean install -pl seatunnel-connectors/seatunnel-connectors-seatunnel-kafka -DskipTests
```

### Running Tests

```bash
# Unit tests
mvn test

# Specific test class
mvn test -Dtest=MySqlConnectorTest

# Integration tests
mvn verify
```

---

## 🐛 Troubleshooting (6 Common Issues)

### Issue 1: ClassNotFoundException: com.mysql.jdbc.Driver

**Solution:**
```bash
wget https://dev.mysql.com/get/Downloads/Connector-J/mysql-connector-java-8.0.33.jar
cp mysql-connector-java-8.0.33.jar $SEATUNNEL_HOME/lib/
seatunnel.sh -c config/job.conf -e spark
```

### Issue 2: OutOfMemoryError: Java heap space

**Solution:**
```bash
export JVM_OPTS="-Xms2G -Xmx8G"
echo 'JVM_OPTS="-Xms2G -Xmx8G"' >> $SEATUNNEL_HOME/bin/seatunnel-env.sh
```

### Issue 3: Connection refused: connect

**Solution:**
```bash
# Verify connectivity
ping source-host
telnet source-host 3306

# Check credentials
mysql -h source-host -u root -p
```

### Issue 4: Table not found during CDC

**Solution:**
```sql
-- Check binlog status
SHOW VARIABLES LIKE 'log_bin';

-- Enable binlog in my.cnf
[mysqld]
log_bin = mysql-bin
binlog_format = row
```

### Issue 5: Slow Job Performance

**Solution:**
```hocon
env {
  parallelism = 8  # Increase parallelism
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

### Issue 6: Kafka offset out of range

**Solution:**
```hocon
source {
  Kafka {
    auto.offset.reset = "earliest"  # or "latest"
  }
}
```

---

## ❓ FAQ (8 Common Questions)

**Q: What's the difference between BATCH and STREAMING mode?**

A:
- **BATCH**: One-time execution, suitable for full database migration
- **STREAMING**: Continuous execution, suitable for real-time sync and CDC

**Q: How do I handle schema changes during CDC?**

A: Configure auto-detection in source:
```hocon
source {
  Mysql {
    schema_change_mode = "auto"
  }
}
```

**Q: Can I transform data during synchronization?**

A: Yes, use SQL transform:
```hocon
transform {
  Sql {
    sql = "SELECT id, UPPER(name) as name FROM source"
  }
}
```

**Q: What's the maximum throughput?**

A: Typical throughput is 100K - 1M records/second per executor. Depends on:
- Hardware (CPU, RAM, Network)
- Database configuration
- Data size per record
- Network latency

**Q: How do I handle errors in production?**

A: Configure restart strategy:
```hocon
env {
  restart_strategy = "exponential_delay"
  restart_strategy.exponential_delay.initial_delay = 1000
  restart_strategy.exponential_delay.max_delay = 30000
  restart_strategy.exponential_delay.multiplier = 2.0
}
```

**Q: Is there a web UI for job management?**

A: Yes! Use SeaTunnel Web Project:
```bash
git clone https://github.com/apache/seatunnel-web.git
cd seatunnel-web
mvn clean install
java -jar target/seatunnel-web-*.jar
# Access at http://localhost:8080
```

**Q: How do I use the SeaTunnel Skill with Claude Code?**

A: After copying to `~/.claude/skills/`, use:
```bash
/seatunnel-skill "your question about SeaTunnel"
```

**Q: Which engine should I use: Spark, Flink, or Zeta?**

A:
- **Zeta**: Lightweight, no external dependencies, single machine
- **Spark**: Batch and batch-stream processing on distributed clusters
- **Flink**: Advanced streaming and CDC on distributed clusters

---

## 🔗 Resources & Links

### Official Documentation
- [SeaTunnel Website](https://seatunnel.apache.org/)
- [GitHub Repository](https://github.com/apache/seatunnel)
- [Connector List](https://seatunnel.apache.org/docs/2.3.12/connector-v2/overview)
- [HOCON Configuration Guide](https://github.com/lightbend/config/blob/main/HOCON.md)

### Community & Support
- [Slack Channel](https://the-asf.slack.com/archives/C01CB5186TL)
- [Mailing Lists](https://seatunnel.apache.org/community/mail-lists/)
- [GitHub Issues](https://github.com/apache/seatunnel/issues)
- [Discussion Forum](https://github.com/apache/seatunnel/discussions)

### Related Projects
- [SeaTunnel Web UI](https://github.com/apache/seatunnel-web)
- [SeaTunnel Tools](https://github.com/apache/seatunnel-tools)
- [Apache Kafka](https://kafka.apache.org/)
- [Apache Flink](https://flink.apache.org/)
- [Apache Spark](https://spark.apache.org/)

---

## 📄 Individual Tools

### 1. SeaTunnel Skill (New)
- **Purpose**: AI-powered assistant for SeaTunnel in Claude Code
- **Location**: [seatunnel-skill/](seatunnel-skill/)
- **Quick Setup**: `cp -r seatunnel-skill ~/.claude/skills/`
- **Usage**: `/seatunnel-skill "your question"`

### 2. SeaTunnel MCP Server
- **Purpose**: Model Context Protocol integration for LLM systems
- **Location**: [seatunnel-mcp/](seatunnel-mcp/)
- **English**: [README.md](seatunnel-mcp/README.md)
- **Chinese**: [README_CN.md](seatunnel-mcp/README_CN.md)
- **Quick Start**: [QUICK_START.md](seatunnel-mcp/docs/QUICK_START.md)

### 3. x2seatunnel
- **Purpose**: Convert DataX and other configurations to SeaTunnel format
- **Location**: [x2seatunnel/](x2seatunnel/)
- **English**: [README.md](x2seatunnel/README.md)
- **Chinese**: [README_zh.md](x2seatunnel/README_zh.md)

---

## 🤝 Contributing

Issues and PRs are welcome!

For the main SeaTunnel engine, see [Apache SeaTunnel](https://github.com/apache/seatunnel).

For these tools, please contribute to [SeaTunnel Tools](https://github.com/apache/seatunnel-tools).

---

**Last Updated**: 2026-01-28 | **License**: Apache 2.0 
