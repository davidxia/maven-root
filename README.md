# Maven root POM

A root Maven project for other projects to depend on, containing common
configuration and plugins.

## How to use

```xml
<parent>
  <groupId>com.davidxia</groupId>
  <artifactId>maven-root</artifactId>
  <version>$VERSION</version>
</parent>
```

Replace `$VERSION` with a real version. 

The root POM is versioned like `1`, `2` etc.

## Configuring plugins

If adding a plugin configuration for others to use, prefer to add it to the
[`<pluginManagement>` section][pluginmanagement] so that the configuration only
applies if child projects actually intend to use that plugin.

[pluginmanagement]: https://maven.apache.org/pom.html#Plugin_Management

## Service packaging convention

The root POM defines a profile that can be used to package a service module
as a runnable artifact. It aims to standardize how we package our services and
to reduce the amount of boilerplate in projects.

The profile is activated automatically given the presence of a marker file
in your maven modules. Since it's activated per module, a multi-module maven
project can be set up to build service artifacts for a subset of the modules.

### `service` profile

If your module contains a marker file named `SERVICE.marker`, we'll assume that it is supposed
to be built into a service artifact.

This profiles configure and activate the following plugins. It expects you to define
a property with the name `service.mainClass` in your project module.

- `maven-dependency-plugin`
  - Copies all jar dependencies into a `/lib` directory
- `buildnumber-maven-plugin`
  - Adds the `buildNumber` and `buildTimestamp` properties to the build
    - `buildNumber`: the git hash of the repo, e.g. `93dc88a`
    - `buildTimestamp`: the time of the build, e.g. `1458228429945`
- `maven-jar-plugin`
  - Adds a `Main-Class` manifest entry to the jar, pointing to `service.mainClass`
  - Adds a `Class-Path` manifest entry referencing all dependency jars in `/lib`
  - Adds an `Implementation-Version` manifest entry with the value `${buildTimestamp}-${buildNumber}`

These are tied to the `prepare-package` Maven lifecycle phase.
