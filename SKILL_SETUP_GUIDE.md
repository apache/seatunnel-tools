# SeaTunnel Skill Setup Guide

**English** | [中文](#中文版本)

## Getting Started with SeaTunnel Skill in Claude Code

SeaTunnel Skill is an AI-powered assistant for Apache SeaTunnel integrated directly into Claude Code. It helps you with configuration, troubleshooting, learning, and best practices.

---

## Installation

### Step 1: Clone the Repository

```bash
git clone https://github.com/apache/seatunnel-tools.git
cd seatunnel-tools
```

### Step 2: Locate Skills Directory

Claude Code stores skills in your home directory. Create the skills directory if it doesn't exist:

```bash
# Create ~/.claude/skills directory if it doesn't exist
mkdir -p ~/.claude/skills
```

**Directory Locations by OS:**
- **macOS/Linux**: `~/.claude/skills/`
- **Windows**: `%USERPROFILE%\.claude\skills\`

### Step 3: Copy the Skill

```bash
# Copy seatunnel-skill to Claude Code skills directory
cp -r seatunnel-skill ~/.claude/skills/

# Verify installation
ls ~/.claude/skills/seatunnel-skill/
```

You should see:
```
SKILL.md         # Skill definition and metadata
README.md        # Skill documentation
```

### Step 4: Verify Installation

**Option A: Using Claude Code Terminal**

```bash
# In Claude Code terminal, run:
ls ~/.claude/skills/seatunnel-skill/

# You should see the skill files listed
```

**Option B: Check Skill Loading**

In Claude Code, you might see a skill reload notification. If not:
1. Restart Claude Code
2. Or reload the skills manually through the skill menu

### Step 5: Test the Skill

Open Claude Code and try:

```bash
/seatunnel-skill "What is SeaTunnel?"
```

You should get an AI-powered response about SeaTunnel.

---

## Usage Examples

### Getting Help with Configuration

**Question:** How do I configure a MySQL to PostgreSQL job?

```bash
/seatunnel-skill "Create a job configuration to sync data from MySQL to PostgreSQL with batch mode"
```

**Response:** The skill will provide a complete HOCON configuration example with explanations.

### Learning SeaTunnel Concepts

**Question:** Explain CDC mode

```bash
/seatunnel-skill "Explain Change Data Capture (CDC) in SeaTunnel. When should I use it?"
```

**Response:** Comprehensive explanation of CDC, use cases, and configuration examples.

### Troubleshooting

**Question:** My job is failing

```bash
/seatunnel-skill "I'm getting 'OutOfMemoryError: Java heap space' in my batch job. How do I fix it?"
```

**Response:** Detailed diagnosis and solutions, including:
- Root cause explanation
- Configuration fixes
- Environment variable adjustments
- Performance tuning tips

### Connector Information

**Question:** Available Kafka options

```bash
/seatunnel-skill "What are all the configuration options for Kafka source connector?"
```

**Response:** Complete list of options with descriptions and examples.

### Performance Optimization

**Question:** How to optimize streaming

```bash
/seatunnel-skill "How do I optimize a Kafka to Elasticsearch streaming job for maximum throughput?"
```

**Response:** Performance tuning recommendations for parallelism, batch sizes, and resource allocation.

---

## Common Questions

### Q: Why doesn't the skill show up?

**A:** Make sure you:
1. Copied the folder to `~/.claude/skills/` (not a subdirectory)
2. Restarted Claude Code or reloaded skills
3. The folder is named exactly `seatunnel-skill`

**Fix:**
```bash
# Verify the path
ls -la ~/.claude/skills/seatunnel-skill/SKILL.md

# If it doesn't exist, copy it again
cp -r seatunnel-skill ~/.claude/skills/
```

### Q: How do I update the skill?

**A:**
```bash
# Navigate to seatunnel-tools directory
cd /path/to/seatunnel-tools

# Pull latest changes
git pull origin main

# Update the skill
rm -rf ~/.claude/skills/seatunnel-skill
cp -r seatunnel-skill ~/.claude/skills/

# Restart Claude Code
```

### Q: Can I customize the skill?

**A:** Yes! Edit `seatunnel-skill/SKILL.md`:

```bash
# Open the skill definition
nano ~/.claude/skills/seatunnel-skill/SKILL.md

# Make your changes
# The skill will use your customizations
```

### Q: Where are skill responses saved?

**A:** Skill responses are part of your Claude Code conversation history. They are saved in:
- Local Claude Code workspace
- Optionally synced to Claude.ai if configured

---

## Advanced Usage

### Chaining Questions

You can build on previous questions in the same conversation:

```bash
/seatunnel-skill "What is batch mode?"

# In next message, reference previous context:
/seatunnel-skill "Show me a complete example combining batch mode with MySQL source"

# The skill understands the context from previous messages
```

### Getting Code Examples

The skill can generate complete, production-ready configurations:

```bash
/seatunnel-skill "Generate a complete SeaTunnel job configuration that:
1. Reads from MySQL database 'sales_db' table 'orders'
2. Filters orders from last 30 days
3. Writes to PostgreSQL 'analytics_db' table 'orders_processed'
4. Uses batch mode with 4 parallelism"
```

### Integration with Your Workflow

**Development Pipeline:**
```bash
# 1. Understand requirements
/seatunnel-skill "Explain how to set up CDC from MySQL"

# 2. Design solution
/seatunnel-skill "Design a real-time data pipeline from MySQL CDC to Kafka"

# 3. Generate configuration
/seatunnel-skill "Generate the complete HOCON configuration for the pipeline"

# 4. Debug issues
/seatunnel-skill "My job is timing out. Debug this configuration: [paste config]"

# 5. Optimize performance
/seatunnel-skill "How can I optimize this job for better throughput?"
```

---

## Troubleshooting

### Issue: Skill not found error

```
Error: Unknown skill: seatunnel-skill
```

**Solution:**
```bash
# 1. Verify skill exists
ls ~/.claude/skills/seatunnel-skill/

# 2. Check file permissions
chmod +r ~/.claude/skills/seatunnel-skill/*

# 3. Restart Claude Code and try again
```

### Issue: Outdated responses

**Solution:**
```bash
# Update skill to latest version
cd seatunnel-tools
git pull origin main
rm -rf ~/.claude/skills/seatunnel-skill
cp -r seatunnel-skill ~/.claude/skills/
```

### Issue: Responses are too generic

**Try:**
```bash
# Be more specific in your question:
# Instead of:
/seatunnel-skill "How to configure MySQL?"

# Try:
/seatunnel-skill "Configure MySQL source for a batch job that reads table 'users' with filters"
```

---

## Tips for Best Results

1. **Be Specific**: More details in your question = better responses
2. **Include Context**: Mention your use case (batch/streaming, source/sink types)
3. **Show Configuration**: Paste your HOCON config for debugging
4. **Reference Versions**: Specify SeaTunnel version (e.g., 2.3.12)
5. **Ask Follow-ups**: The skill remembers conversation context

---

## Keyboard Shortcuts

- **Cmd+K** (macOS) / **Ctrl+K** (Windows/Linux): Quick open skill
- **Type** `/seatunnel-skill`: Invoke skill
- **Tab**: Auto-complete skill parameters
- **Esc**: Cancel skill input

---

## File Locations

```
seatunnel-tools/
├── seatunnel-skill/              # AI Skill
│   ├── SKILL.md                  # Skill definition
│   └── README.md                 # Documentation
├── README.md                      # Main documentation
├── README_CN.md                   # Chinese documentation
└── SKILL_SETUP_GUIDE.md          # This file
```

---

## Getting Help

- **Skill Issues**: Try `/seatunnel-skill "How do I troubleshoot..."`
- **SeaTunnel Questions**: Ask the skill directly
- **Installation Help**: See [README.md](README.md) or [README_CN.md](README_CN.md)
- **Report Issues**: [GitHub Issues](https://github.com/apache/seatunnel-tools/issues)

---

## Next Steps

1. ✅ Install skill (`cp -r seatunnel-skill ~/.claude/skills/`)
2. ✅ Test skill (`/seatunnel-skill "What is SeaTunnel?"`)
3. 📚 Explore examples in this guide
4. 🚀 Use skill for your SeaTunnel projects
5. 📝 Share feedback and improvements

---

---

# 中文版本

# SeaTunnel Skill 安装使用指南

## 开始使用 SeaTunnel Skill

SeaTunnel Skill 是一个集成到 Claude Code 中的 AI 助手，帮助您进行 Apache SeaTunnel 的配置、故障排查、学习和最佳实践。

---

## 安装步骤

### 第一步：克隆仓库

```bash
git clone https://github.com/apache/seatunnel-tools.git
cd seatunnel-tools
```

### 第二步：定位技能目录

Claude Code 在您的主目录中存储技能。如果目录不存在，请创建：

```bash
# 如果目录不存在，则创建 ~/.claude/skills 目录
mkdir -p ~/.claude/skills
```

**不同操作系统的目录位置：**
- **macOS/Linux**: `~/.claude/skills/`
- **Windows**: `%USERPROFILE%\.claude\skills\`

### 第三步：复制技能文件

```bash
# 复制 seatunnel-skill 到 Claude Code 技能目录
cp -r seatunnel-skill ~/.claude/skills/

# 验证安装
ls ~/.claude/skills/seatunnel-skill/
```

您应该看到：
```
SKILL.md         # 技能定义和元数据
README.md        # 技能文档
```

### 第四步：验证安装

**选项 A：使用 Claude Code 终端**

```bash
# 在 Claude Code 终端中运行：
ls ~/.claude/skills/seatunnel-skill/

# 您应该看到技能文件列出
```

**选项 B：检查技能加载**

在 Claude Code 中，您可能会看到技能重新加载通知。如果没有：
1. 重启 Claude Code
2. 或通过技能菜单手动重新加载

### 第五步：测试技能

打开 Claude Code 并尝试：

```bash
/seatunnel-skill "什么是 SeaTunnel？"
```

您应该获得关于 SeaTunnel 的 AI 驱动响应。

---

## 使用示例

### 获取配置帮助

**问题：** 如何配置从 MySQL 到 PostgreSQL 的任务？

```bash
/seatunnel-skill "创建一个任务配置，以批处理模式将数据从 MySQL 同步到 PostgreSQL"
```

**响应：** 技能将提供完整的 HOCON 配置示例和说明。

### 学习 SeaTunnel 概念

**问题：** 解释 CDC 模式

```bash
/seatunnel-skill "在 SeaTunnel 中解释变更数据捕获 (CDC)。何时应该使用它？"
```

**响应：** 关于 CDC 的全面解释、用例和配置示例。

### 故障排查

**问题：** 我的任务失败了

```bash
/seatunnel-skill "我的批处理任务出现 'OutOfMemoryError: Java heap space' 错误。我应该如何修复？"
```

**响应：** 详细的诊断和解决方案，包括：
- 根本原因说明
- 配置修复
- 环境变量调整
- 性能调优建议

### 连接器信息

**问题：** 可用的 Kafka 选项

```bash
/seatunnel-skill "Kafka 源连接器的所有配置选项是什么？"
```

**响应：** 完整的选项列表，带有描述和示例。

### 性能优化

**问题：** 如何优化流处理

```bash
/seatunnel-skill "如何优化从 Kafka 到 Elasticsearch 的流处理任务以获得最大吞吐量？"
```

**响应：** 并行度、批大小和资源分配的性能调优建议。

---

## 常见问题

### Q: 为什么技能不显示？

**A:** 请确保您：
1. 将文件夹复制到 `~/.claude/skills/`（不是子目录）
2. 重启了 Claude Code 或重新加载了技能
3. 文件夹名称完全是 `seatunnel-skill`

**修复：**
```bash
# 验证路径
ls -la ~/.claude/skills/seatunnel-skill/SKILL.md

# 如果不存在，再次复制
cp -r seatunnel-skill ~/.claude/skills/
```

### Q: 如何更新技能？

**A：**
```bash
# 导航到 seatunnel-tools 目录
cd /path/to/seatunnel-tools

# 拉取最新更改
git pull origin main

# 更新技能
rm -rf ~/.claude/skills/seatunnel-skill
cp -r seatunnel-skill ~/.claude/skills/

# 重启 Claude Code
```

### Q: 我可以自定义技能吗？

**A：** 可以！编辑 `seatunnel-skill/SKILL.md`：

```bash
# 打开技能定义
nano ~/.claude/skills/seatunnel-skill/SKILL.md

# 进行更改
# 技能将使用您的自定义设置
```

### Q: 技能响应保存在哪里？

**A：** 技能响应是您的 Claude Code 对话历史的一部分。它们保存在：
- 本地 Claude Code 工作区
- 如果配置，可选地同步到 Claude.ai

---

## 高级用法

### 链接问题

您可以在同一对话中基于之前的问题进行构建：

```bash
/seatunnel-skill "什么是批处理模式？"

# 在下一条消息中，参考之前的上下文：
/seatunnel-skill "展示一个结合批处理模式和 MySQL 源的完整示例"

# 技能理解来自之前消息的上下文
```

### 获取代码示例

技能可以生成完整的、生产就绪的配置：

```bash
/seatunnel-skill "生成一个完整的 SeaTunnel 任务配置，该配置：
1. 从 MySQL 数据库 'sales_db' 表 'orders' 读取
2. 过滤最近 30 天的订单
3. 写入 PostgreSQL 'analytics_db' 表 'orders_processed'
4. 使用 4 个并行度的批处理模式"
```

### 与您的工作流集成

**开发流程：**
```bash
# 1. 了解需求
/seatunnel-skill "解释如何从 MySQL 设置 CDC"

# 2. 设计解决方案
/seatunnel-skill "设计从 MySQL CDC 到 Kafka 的实时数据管道"

# 3. 生成配置
/seatunnel-skill "为管道生成完整的 HOCON 配置"

# 4. 调试问题
/seatunnel-skill "我的任务超时。调试此配置：[粘贴配置]"

# 5. 优化性能
/seatunnel-skill "我应该如何优化此任务以获得更好的吞吐量？"
```

---

## 故障排查

### 问题：技能未找到错误

```
Error: Unknown skill: seatunnel-skill
```

**解决方案：**
```bash
# 1. 验证技能存在
ls ~/.claude/skills/seatunnel-skill/

# 2. 检查文件权限
chmod +r ~/.claude/skills/seatunnel-skill/*

# 3. 重启 Claude Code 并重试
```

### 问题：响应过时

**解决方案：**
```bash
# 更新技能到最新版本
cd seatunnel-tools
git pull origin main
rm -rf ~/.claude/skills/seatunnel-skill
cp -r seatunnel-skill ~/.claude/skills/
```

### 问题：响应过于笼统

**尝试：**
```bash
# 在问题中更具体：
# 不是：
/seatunnel-skill "如何配置 MySQL？"

# 而是：
/seatunnel-skill "配置 MySQL 源进行批处理任务，读取 'users' 表并应用过滤器"
```

---

## 获得最佳结果的提示

1. **具体明确**: 问题中的细节越多 = 响应越好
2. **包含上下文**: 提及您的用例（批/流、源/宿类型）
3. **显示配置**: 粘贴您的 HOCON 配置以进行调试
4. **参考版本**: 指定 SeaTunnel 版本（例如 2.3.12）
5. **提出后续问题**: 技能会记住对话上下文

---

## 键盘快捷键

- **Cmd+K** (macOS) / **Ctrl+K** (Windows/Linux): 快速打开技能
- **输入** `/seatunnel-skill`: 调用技能
- **Tab**: 自动完成技能参数
- **Esc**: 取消技能输入

---

## 文件位置

```
seatunnel-tools/
├── seatunnel-skill/              # AI 技能
│   ├── SKILL.md                  # 技能定义
│   └── README.md                 # 文档
├── README.md                      # 主文档
├── README_CN.md                   # 中文文档
└── SKILL_SETUP_GUIDE.md          # 此文件
```

---

## 获取帮助

- **技能问题**: 尝试 `/seatunnel-skill "我应该如何故障排查..."`
- **SeaTunnel 问题**: 直接向技能提问
- **安装帮助**: 查看 [README.md](README.md) 或 [README_CN.md](README_CN.md)
- **报告问题**: [GitHub Issues](https://github.com/apache/seatunnel-tools/issues)

---

## 后续步骤

1. ✅ 安装技能 (`cp -r seatunnel-skill ~/.claude/skills/`)
2. ✅ 测试技能 (`/seatunnel-skill "什么是 SeaTunnel？"`)
3. 📚 探索本指南中的示例
4. 🚀 将技能用于您的 SeaTunnel 项目
5. 📝 分享反馈和改进

---

**最后更新**: 2026-01-28 | **许可证**: Apache 2.0