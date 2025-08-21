```markdown
# Apache SeaTunnel 工具

本仓库托管 Apache SeaTunnel 的辅助工具，专注于配置、转换、打包和诊断等提升开发/运维效率的工具集。当前包含的模块：

- x2seatunnel：将其它工具（例如 DataX）的配置转换为 SeaTunnel 配置文件。

未来可能会添加更多工具。如需主数据集成引擎，请参见
[Apache SeaTunnel](https://github.com/apache/seatunnel) 项目。

## 工具 1 - SeaTunnel MCP Server

什么是 MCP？
- MCP（Model Context Protocol）是一种将 LLM 与工具、数据和系统连接的开放协议。通过 SeaTunnel MCP，你可以从基于 LLM 的界面直接操作 SeaTunnel，同时保持服务端逻辑安全且可审计。
- 了解更多：https://github.com/modelcontextprotocol

SeaTunnel MCP Server
- 源码目录： [seatunnel-mcp/](seatunnel-mcp/)
- 英文 README： [seatunnel-mcp/README.md](seatunnel-mcp/README.md)
- 中文 README： [seatunnel-mcp/README_CN.md](seatunnel-mcp/README_CN.md)
- 快速开始： [seatunnel-mcp/docs/QUICK_START.md](seatunnel-mcp/docs/QUICK_START.md)
- 用户指南： [seatunnel-mcp/docs/USER_GUIDE.md](seatunnel-mcp/docs/USER_GUIDE.md)
- 开发者指南： [seatunnel-mcp/docs/DEVELOPER_GUIDE.md](seatunnel-mcp/docs/DEVELOPER_GUIDE.md)

有关截图、演示视频、功能、安装与使用说明，请参阅 `seatunnel-mcp` 目录下的 README。

## 工具 2 - x2seatunnel

x2seatunnel 是什么？
- x2seatunnel 是一个配置转换工具，帮助用户将来自其他数据集成工具（例如 DataX）的配置迁移到 SeaTunnel，通过自动转换现有配置生成 SeaTunnel 可识别的格式。
- x2seatunnel 文档：
    - 英文： [x2seatunnel/README.md](x2seatunnel/README.md)
    - 中文： [x2seatunnel/README_zh.md](x2seatunnel/README_zh.md)

## 参与贡献

欢迎提交 Issue 与 PR。

可从 [Apache SeaTunnel](https://github.com/apache/seatunnel) 获取主项目源码。