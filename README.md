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