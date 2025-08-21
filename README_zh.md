# SeaTunnel Tools（工具集）

本仓库用于沉淀与 Apache SeaTunnel 相关的周边工具，目标是提升配置生产力、迁移与运维体验。目前包含：

- x2seatunnel：将 DataX 等配置转换为 SeaTunnel 配置文件的工具。

未来可能会新增更多模块；SeaTunnel 引擎本体请参考
[Apache SeaTunnel](https://github.com/apache/seatunnel)。

## 模块文档导航

- x2seatunnel
	- 英文：[x2seatunnel/README.md](x2seatunnel/README.md)
	- 中文：[x2seatunnel/README_zh.md](x2seatunnel/README_zh.md)

## 构建与测试

先决条件：
- Java 8+
- Maven 3.6+

构建整个仓库：

```bash
mvn -T 1C -e -DskipIT clean verify
```

仅构建某个子模块（例如 x2seatunnel）：

```bash
mvn -pl x2seatunnel -am -DskipTests clean package
```

产物在 `x2seatunnel/target/`：
- 可运行 JAR：`x2seatunnel-<version>.jar`
- 分发 ZIP：`x2seatunnel-<version>-bin.zip`（或类似命名）

解压后参考子模块 README 进行运行。

## 版本与依赖

本仓库依赖已发布的 SeaTunnel 组件（如 `seatunnel-common`、`seatunnel-jackson`）。
版本通过根 POM 的 `seatunnel.version` 统一管理（当前为 2.3.11）。

## 贡献

欢迎提交 Issue 与 PR。
