# Apache SeaTunnel Tools

This repository hosts auxiliary tools for Apache SeaTunnel. It focuses on developer/operator productivity around configuration, conversion, packaging and diagnostics. Current modules:

- x2seatunnel: Convert configurations (e.g., DataX) into SeaTunnel configuration files.

More tools may be added in the future. For the main data integration engine, see the
[Apache SeaTunnel](https://github.com/apache/seatunnel) project.

## Modules documentation

- x2seatunnel
	- English: [x2seatunnel/README.md](x2seatunnel/README.md)
	- 中文: [x2seatunnel/README_zh.md](x2seatunnel/README_zh.md)

## Build and Test

Prerequisites:
- Java 8+
- Maven 3.6+

Build the whole repository:

## Tool 1 - SeaTunnel MCP Server

What is MCP?
- MCP (Model Context Protocol) is an open protocol for connecting LLMs to tools, data, and systems. With SeaTunnel MCP, you can operate SeaTunnel directly from an LLM-powered interface while keeping the server-side logic secure and auditable.
- Learn more: https://github.com/modelcontextprotocol

SeaTunnel MCP Server
- Source folder: [seatunnel-mcp/](seatunnel-mcp/)
- English README: [seatunnel-mcp/README.md](seatunnel-mcp/README.md)
- Chinese: [seatunnel-mcp/README_CN.md](seatunnel-mcp/README_CN.md)
- Quick Start: [seatunnel-mcp/docs/QUICK_START.md](seatunnel-mcp/docs/QUICK_START.md)
- User Guide: [seatunnel-mcp/docs/USER_GUIDE.md](seatunnel-mcp/docs/USER_GUIDE.md)
- Developer Guide: [seatunnel-mcp/docs/DEVELOPER_GUIDE.md](seatunnel-mcp/docs/DEVELOPER_GUIDE.md)

For screenshots, demo video, features, installation and usage instructions, please refer to the README in the seatunnel-mcp directory.

Get the main project from [Apache SeaTunnel](https://github.com/apache/seatunnel) 
```bash
mvn -T 1C -e -DskipIT clean verify
```

Build only a submodule (x2seatunnel as example):

```bash
mvn -pl x2seatunnel -am -DskipTests clean package
```

Artifacts will be generated under `x2seatunnel/target/`:
- Runnable JAR: `x2seatunnel-<version>.jar`
- Distribution ZIP: `x2seatunnel-<version>-bin.zip` (or similar)

Unzip the distribution and follow the submodule README to run.

## Versioning and Dependencies

This repository depends on released Seatunnel artifacts (e.g., `seatunnel-common`, `seatunnel-jackson`).
Versions are centrally managed via the `seatunnel.version` property in the root POM.

## Contributing

Issues and PRs are welcome.