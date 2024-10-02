# Overview

* == core low-level incremental ("streaming") parser + generator abstractions + default implementation of handler types (parser, generator) / handle JSON format 
  * generator abstractions
    * used by [Jackson Data Processor](https://github.com/FasterXML/jackson)
  * 👁️NOT JSON specific 👁️
    * ALTHOUGH some naming does contain 'JSON'
      * REASON: 🧠historical reasons 🧠
    * 👁️EXCEPT TO packages / contain word 'json'👁️
  * uses
    * base of [Jackson data-binding](https://github.com/FasterXML/jackson-databind)
    * 👁️by other data format implementations / implement the core interfaces 👁️ 
      * _Example:_
        * [Smile (binary JSON)](https://github.com/FasterXML/jackson-dataformats-binary/tree/master/smile)
        * [XML](https://github.com/FasterXML/jackson-dataformat-xml)
        * [CSV](https://github.com/FasterXML/jackson-dataformats-text/tree/master/csv)
        * [Protobuf](https://github.com/FasterXML/jackson-dataformats-binary/tree/master/protobuf)
        * [CBOR](https://github.com/FasterXML/jackson-dataformats-binary/tree/master/cbor)
      * -> can use [data-binding package](https://github.com/FasterXML/jackson-databind)
* Available versions
  * v2.0+
  * v1.x
    * check [Jackson-1](../../../jackson-1) github repo

## Status

| Type | Status |
| ---- | ------ |
| Build (CI) | [![Build (github)](https://github.com/FasterXML/jackson-core/actions/workflows/main.yml/badge.svg)](https://github.com/FasterXML/jackson-core/actions/workflows/main.yml) |
| Artifact | [![Maven Central](https://maven-badges.herokuapp.com/maven-central/com.fasterxml.jackson.core/jackson-core/badge.svg)](https://maven-badges.herokuapp.com/maven-central/com.fasterxml.jackson.core/jackson-core) |
| OSS Sponsorship | [![Tidelift](https://tidelift.com/badges/package/maven/com.fasterxml.jackson.core:jackson-core)](https://tidelift.com/subscription/pkg/maven-com-fasterxml-jackson-core-jackson-core?utm_source=maven-com-fasterxml-jackson-core-jackson-core&utm_medium=referral&utm_campaign=readme) |
| Javadocs | [![Javadoc](https://javadoc.io/badge/com.fasterxml.jackson.core/jackson-core.svg)](https://javadoc.io/doc/com.fasterxml.jackson.core/jackson-core) |
| Code coverage (2.18) | [![codecov.io](https://codecov.io/github/FasterXML/jackson-core/coverage.svg?branch=2.18)](https://codecov.io/github/FasterXML/jackson-core?branch=2.18) |
| CodeQ (ClusterFuzz) | [![Fuzzing Status](https://oss-fuzz-build-logs.storage.googleapis.com/badges/jackson-core.svg)](https://bugs.chromium.org/p/oss-fuzz/issues/list?sort=-opened&can=1&q=proj:jackson-core) |
| OpenSSF Score | [![OpenSSF Scorecard](https://api.securityscorecards.dev/projects/github.com/FasterXML/jackson-core/badge)](https://securityscorecards.dev/viewer/?uri=github.com/FasterXML/jackson-core) |

# Get it!

## Maven

* add it | "pom.xml"

    ```xml
    <dependency>
        <groupId>com.fasterxml.jackson.core</groupId>
        <artifactId>jackson-core</artifactId>
        <version>${jackson.version.core}</version>
    </dependency>
    ```

* jars
  * you can download
    * | Maven repository or
    * links on [Wiki](../../wiki)
  * == functional OSGi bundle / import/export declarations
    * -> can be used | OSGi container
  * from Jackson v2.10+
    * `module-info.class` definitions are included -> jar == proper Java module (JPMS)

* `JUnit`
  * 1! external dependency

## Non-Maven

* download jars
* from Jackson v2.12+
  * additional Gradle v6 Module Metadata -> version alignment with Gradle

-----

# Use it!

## General

* create `JsonFactory` instance
  * if you configure it -> thread-safe

      ```java
      // 1. Builder-style since 2.10:
      JsonFactory factory = JsonFactory.builder()         
      // configure, if necessary:
           .enable(JsonReadFeature.ALLOW_JAVA_COMMENTS)
           .build();
    
      // 2. older 2.x mechanism, still supported for 2.x
      JsonFactory factory = new JsonFactory();
      // configure, if necessary:
      factory.enable(JsonReadFeature.ALLOW_JAVA_COMMENTS);
    
     // 3. `ObjectMapper` -- from -- Jackson Databind package
     JsonFactory factory = objectMapper.getFactory();      
      ```

## Usage, simple reading

* `JsonParser` or its subclasses ( for data formats != JSON)
  * allows
    * reading
  * instance -- can be created via -- `JsonFactory`
  * _Example:_ [Reading and Writing Event Streams](http://www.cowtowncoder.com/blog/archives/2009/01/entry_132.html)
    * TODO:

## Usage, simple writing

* `JsonGenerator` or its subclasses ( for data formats != JSON)
  * allows
    * writing
  * instance -- can be created via -- `JsonFactory`
  * _Example:_ [Reading and Writing Event Streams](http://www.cowtowncoder.com/blog/archives/2009/01/entry_132.html)
    * TODO:

## Processing limits

* requirements
  * [Jackson v2.15+](https://github.com/FasterXML/jackson/wiki/Jackson-Release-2.15)
* == configurable limits
  * 👁️about some aspects of 👁️ 
    * input decoding
    * output generation
  * -- are defined 
    * via -- new `StreamReadConstraints` or `StreamWriteConstraints` classes /
      * configurable / `JsonFactory` basis 
    * -- <= length, ⚠️BUT NOT necessarily imposed ⚠️
      * _Example:_ if maximum allowed length is 1000 units & there is something with length 1003 -- may NOT cause -- exception (but 1500 is MORE probable to fail)
  * allows
    * reducing likelihood of excessive
      * memory usage/retention
      * processing costs / WITHOUT validation  
  * length is expressed in input/output units
    * -- depending on -- input source
      * `byte`s
      * `char`s

### Input parsing limits

* Maximum Number token length (2.15+): (see https://github.com/FasterXML/jackson-core/issues/815)
    * Default: Maximum 1000 for both integral and floating-point numbers.
* Maximum String value length (2.15+): (see https://github.com/FasterXML/jackson-core/issues/863)
    * Default: 20_000_000 (20 million) (since 2.15.1; 2.15.0 had lower limit, 5 million)
* Maximum Input nesting depth (2.15+): (see https://github.com/FasterXML/jackson-core/pull/943)
    * Default: 1000 levels
* Maximum Property name length (2.16+): (see https://github.com/FasterXML/jackson-core/issues/1047)
    * Default: 50,000
* Maximum Document length (2.16+): (see https://github.com/FasterXML/jackson-core/issues/1046)
    * Default: Unlimited (-1)

### Output generation limits

* Maximum Output nesting depth
  * requirements
    * Jackson v2.16+
  * https://github.com/FasterXML/jackson-core/pull/1055
  * default: 1000 levels

### Re-configuring limits

* way to change per-factory limits

    ```java
    JsonFactory f = JsonFactory.builder()
      .streamReadConstraints(StreamReadConstraints.builder().maxDocumentLength(10_000_000L).build())
      .streamReadConstraints(StreamReadConstraints.builder().maxNumberLength(250).build())
      .streamWriteConstraints(StreamWriteConstraints.builder().maxNestingDepth(2000).build())
      .build();
    ```

## Error Report Configuration

* TODO:
Starting with [Jackson 2.16](https://github.com/FasterXML/jackson/wiki/Jackson-Release-2.16), Jackson offers configurable
behavior around error-reporting.

Currently supported configuration options are:

- `maxErrorTokenLength` : Maximum length of token to include in error messages (2.16+): (see [#1066](https://github.com/FasterXML/jackson-core/issues/1066))
- `maxRawContentLength` : Maximum length of raw content to include in error messages (2.16+): (see [#1066](https://github.com/FasterXML/jackson-core/issues/1066))

... see [Example](#example) section below.

### Example

```java
JsonFactory f = JsonFactory.builder()
 .errorReportConfiguration(ErrorReportConfiguration.builder()
   .maxErrorTokenLength(1004)
   .maxRawContentLength(2008)
   .build()
 ).build();
```

-----

## Compatibility

### JDK

Jackson-core package baseline JDK requirement:

* Versions 2.0 - 2.13 require JDK 6
* Versions 2.14 and above require JDK 8

### Android

List is incomplete due to recent addition of compatibility checker.

* 2.13: Android SDK 19+
* 2.14 and above: Android SDK 26+

for information on Android SDK versions to Android Release names see [Android version history](https://en.wikipedia.org/wiki/Android_version_history)

-----

## Release Process

Starting with Jackson 2.15, releases of this module will be [SLSA](https://slsa.dev/) compliant: see issue #844 for details.

Release process is triggered by

    ./release.sh

script which uses Maven Release plug-in under the hood (earlier release plug-in was directly invoked).

-----

## Support

### Community support

Jackson components are supported by the Jackson community through mailing lists, Gitter forum, Github issues. See [Participation, Contributing](../../../jackson#participation-contributing) for full details.

### Enterprise support

Available as part of the [Tidelift](https://tidelift.com/subscription/pkg/maven-com-fasterxml-jackson-core-jackson-databind) Subscription.

The maintainers of `jackson-core` and thousands of other packages are working with Tidelift to deliver commercial support and maintenance for the open source dependencies you use to build your applications. Save time, reduce risk, and improve code health, while paying the maintainers of the exact dependencies you use. [Learn more.](https://tidelift.com/subscription/pkg/maven-com-fasterxml-jackson-core-jackson-core?utm_source=maven-com-fasterxml-jackson-core-jackson-core&utm_medium=referral&utm_campaign=enterprise&utm_term=repo)

-----

# Further reading

## Differences from Jackson 1.x

Project contains versions 2.0 and above: source code for the latest 1.x version (1.9.13) is available from
[FasterXML/jackson-1](https://github.com/FasterXML/jackson-1) repo (unmaintained).

Note that the main differences compared to 1.0 core jar are:

* Maven build instead of Ant
* Annotations carved out to a separate package (that this package depends on)
* Java package is now `com.fasterxml.jackson.core` (instead of `org.codehaus.jackson`)

## Links

* Project  [Wiki](../../wiki) has JavaDocs and links to downloadable artifacts
* [Jackson (portal)](https://github.com/FasterXML/jackson) has links to all FasterXML-maintained "official" Jackson components
* [Jackson Docs](https://github.com/FasterXML/jackson-docs) is the portal/hub for all kinds of Jackson documentation
