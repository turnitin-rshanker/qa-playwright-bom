# qa-java-testlib-bom

Shared Maven BOM for QA Java test libraries to centralize dependency version management and ensure consistent dependency
versions across projects.

---

## Overview

This project provides a **Bill of Materials (BOM)** for managing dependency versions across the QA Java test library
stack.

It acts as a **single source of truth for third-party dependency versions**, helping prevent version conflicts and
improve build consistency.

---

## Why This Exists

A dependency convergence proof of concept showed that:

* Multiple versions of the same dependency can exist without being obvious
* These conflicts often only appear later (e.g., Jenkins or runtime)
* Maven Enforcer can detect these issues early
* Managing versions directly in each project leads to large, hard-to-maintain POM files

This BOM solves that by centralizing version management for shared dependencies.

---

## Usage

Import this BOM in your project’s `dependencyManagement` section:

```xml

<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>com.tii</groupId>
            <artifactId>qa-java-testlib-bom</artifactId>
            <version>1.0.0-SNAPSHOT</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
```

After importing:

* Remove explicit versions for dependencies managed by the BOM
* Let Maven resolve versions automatically

---

## Example

The **`qa-java-selenium-pagefactory-boilerplate`** project has been updated to use this BOM and can be used as a
reference.

It demonstrates:

* How to import the BOM
* Which versions can be removed
* How to keep internal dependencies explicitly versioned

---

## What It Manages

This BOM defines versions for **shared third-party dependencies**, including:

### Imported BOMs

* Selenium (`selenium-bom`)
* Netty (`netty-bom`)
* Jackson (`jackson-bom`)
* Log4j (`log4j-bom`)

### Core Libraries

* SLF4J (`slf4j-api`, `jcl-over-slf4j`)
* Google (`guava`, `gson`, `error_prone_annotations`)
* Apache Commons (`commons-lang3`, `commons-io`, `commons-codec`)
* YAML (`snakeyaml`)
* CSV (`opencsv`)
* HTTP (`httpclient5`)
* Bytecode (`byte-buddy`)

### Test / Automation Tooling

* TestNG
* Lombok
* AssertJ
* Awaitility
* Allure (`allure-testng`)
* WebDriverManager
* Appium

---

## Important Notes

* **This BOM manages third-party dependencies only**
* **Internal QA test libraries must remain explicitly versioned**

Example:

```xml

<dependency>
    <groupId>com.tii</groupId>
    <artifactId>qa-java-selenium-testlib</artifactId>
    <version>5.x.x-SNAPSHOT</version>
</dependency>
```

This preserves compatibility guarantees between internal libraries and avoids accidental breakages.

---

## Working With Enforcer

This BOM is intended to be used with Maven Enforcer:

```xml

<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-enforcer-plugin</artifactId>
    <version>3.6.2</version>
    <executions>
        <execution>
            <id>enforce-dependency-convergence</id>
            <goals>
                <goal>enforce</goal>
            </goals>
            <configuration>
                <rules>
                    <dependencyConvergence/>
                </rules>
            </configuration>
        </execution>
    </executions>
</plugin>
```

Together they:

* Detect conflicts (Enforcer)
* Resolve them consistently (BOM)

### Notes

* It is recommended to apply Enforcer in **top-level test projects first**
* Initial adoption may surface existing conflicts
* Builds can be temporarily bypassed with:

```bash
-Denforcer.skip=true
```

---

## Versioning

This BOM follows semantic versioning (X.Y.Z):

* **Major (X.0.0)**
  Breaking dependency changes (e.g., major framework upgrades)

* **Minor (X.Y.0)**
  Backward-compatible dependency upgrades or additions

* **Patch (X.Y.Z)**
  Fixes to dependency alignment or convergence issues

Consumers must explicitly upgrade the BOM version to adopt changes.

---

## Development

Install locally:

```bash
./mvnw clean install
```

### Updating dependency versions

1. Update dependency versions in this BOM
2. **Bump the BOM version**
3. Install or publish the updated BOM:

```bash
./mvnw clean install
```

4. Update the BOM version in consumer projects
5. Run a consumer project:

```bash
./mvnw verify -DskipTests
```

6. Verify `dependencyConvergence` passes
7. Run tests to confirm no runtime issues

---

## Summary

This BOM enables:

* Consistent dependency versions across projects
* Cleaner project POM files
* Early detection of dependency conflicts
* More reliable and reproducible builds
