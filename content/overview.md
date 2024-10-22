+++
title = "Overview"
weight = 10
description = "Protocol Buffers are a language-neutral, platform-neutral extensible mechanism for serializing structured data."
type = "docs"
+++

* vs JSON
  * smaller
  * faster
  * -- generate -- native language bindings
* == definition language ('.proto' files) + code / -- generated with -- compiler + runtime libraries language-specific + serialization format + serialized data
  * '.proto' files
    * allows
      * describing protocol buffer
        * *messages* &
        * *services*
    * _Examples:_ 
      * [timestamp.proto](https://github.com/protocolbuffers/protobuf/blob/main/src/google/protobuf/timestamp.proto)
      * [status.proto](https://github.com/googleapis/googleapis/blob/master/google/rpc/status.proto)
      * 
  
        ```proto
        message Person {
          optional string name = 1;
          optional int32 id = 2;
          optional string email = 3;
        }
        ```
  * proto compiler
    * from '.proto' | build time -> generates code | [different programming languages](#cross-lang)
  * generated code
    * -- contains --
      * accessors / EACH field
      * methods / serialize & parse the whole structure <- to & from -> raw bytes
    * _Example:_ using the generated code

      ```java
      Person john = Person.newBuilder()
          .setId(1234)
          .setName("John Doe")
          .setEmail("jdoe@example.com")
          .build();
      output = new FileOutputStream(args[0]);
      john.writeTo(output);
      ```

## What Problems do Protocol Buffers Solve? {#solve}

* recommended for
  * ephemeral network traffic &
    * _Example:_ inter-server communications 
  * long-term data storage
    * _Example:_ archival storage of data | disk 
  * serialize data types, structured & record-like | manner language-neutral, platform-neutral & extensible
  * defining communication protocols
  * maintain backwards compatibility

## What are the Benefits of Using Protocol Buffers? {#benefits}

* benefits of using it
  * Compact data storage
  * Fast parsing
  * Availability in many programming languages
  * Optimized functionality through automatically-generated classes

### Cross-language Compatibility {#cross-lang}

The same messages can be read by code written in any supported programming
language. You can have a Java program on one platform capture data from one
software system, serialize it based on a `.proto` definition, and then extract
specific values from that serialized data in a separate Python application
running on another platform.

The following languages are supported directly in the protocol buffers compiler,
protoc:

*   [C++](/reference/cpp/cpp-generated#invocation)
*   [C#](/reference/csharp/csharp-generated#invocation)
*   [Java](/reference/java/java-generated#invocation)
*   [Kotlin](/reference/kotlin/kotlin-generated#invocation)
*   [Objective-C](/reference/objective-c/objective-c-generated#invocation)
*   [PHP](/reference/php/php-generated#invocation)
*   [Python](/reference/python/python-generated#invocation)
*   [Ruby](/reference/ruby/ruby-generated#invocation)

The following languages are supported by Google, but the projects' source code
resides in GitHub repositories. The protoc compiler uses plugins for these
languages:

<!-- mdformat off(mdformat adds a space between the ) and the {) -->
*   [Dart](https://github.com/google/protobuf.dart)
*   [Go](https://github.com/protocolbuffers/protobuf-go)
<!-- mdformat on -->

Additional languages are not directly supported by Google, but rather by other
GitHub projects. These languages are covered in
[Third-Party Add-ons for Protocol Buffers](https://github.com/protocolbuffers/protobuf/blob/master/docs/third_party.md).

### Cross-project Support {#cross-proj}

You can use protocol buffers across projects by defining `message` types in
`.proto` files that reside outside of a specific project’s code base. If you're
defining `message` types or enums that you anticipate will be widely used
outside of your immediate team, you can put them in their own file with no
dependencies.

A couple of examples of proto definitions widely-used within Google are
[`timestamp.proto`](https://github.com/protocolbuffers/protobuf/blob/master/src/google/protobuf/timestamp.proto)
and
[`status.proto`](https://github.com/googleapis/googleapis/blob/master/google/rpc/status.proto).

### Updating Proto Definitions Without Updating Code {#updating-defs}

* backward & forward compatible As long as you follow some
  * follow [simple practices](/programming-guides/proto3/#updating)
  * forward compatible ==
    * old code -- ignoring any new added fields, will read -- new messages
    * new code -- ignore new fields, wil read -- old messages

### When are Protocol Buffers not a Good Fit? {#not-good-fit}

* data > 1MB
  * Reason: 🧠Protocol buffers assume that entire messages 
    * -- can be loaded into memory -- | 1!
    * < object graph 🧠 
  * consider another alternative
  * side impacts
    * spikes | memory usage
      * Reason: 🧠 serialized copies -> several copies of the data 🧠 
* need to compare messages
  * Reason: 🧠 
    * if protocol buffers are serialized -> can exist several binary serializations / SAME data 
    * -> FULLY parsing them 🧠
* messages are NOT compressed
  * Reason: 🧠 produce much smaller files 🧠
    * _Example:_ use JPEG and PNG
* usig large multi-dimensional arrays of floating point numbers
  * Reason: 🧠 NOT efficient neither size nor speed 🧠
  * Solution: use other formats -- [FITS](https://en.wikipedia.org/wiki/FITS) --
* NOT-object-oriented languages
  * Reason: 🧠NOT well-supported 🧠
  * _Example:_ Fortran
* NOT possible to fully interpret a .proto` / -- WITHOUT accessing to -- it
  * Reason: 🧠Protocol buffer messages do NOT inherently self-describe their data 🧠
* environments / require to build on standards
  * Reason: 🧠 protocol buffers are NOT a formal standard of any organization 🧠

## Who Uses Protocol Buffers? {#who-uses}

* 👁️MOST commonly-used data format | Google 👁️
* [gRPC](https://grpc.io)
* [Google Cloud](https://cloud.google.com)
* [Envoy Proxy](https://www.envoyproxy.io)

## How do Protocol Buffers Work? {#work}

![](protocol-buffers-concepts.png) 

* .java, .py, .cc / generated by protocol buffers -- provide -- utility methods to
  * retrieve data -- from -- files & streams
  * extract individual values -- from -- data
  * check if data exists
  * serialize data back to -- file OR stream
  * ...
* _Example:_

```proto
message Person {
  optional string name = 1;
  optional int32 id = 2;
  optional string email = 3;
}
```

compiling `.proto` -> creates a `Builder` class / you can use to

```java
Person john = Person.newBuilder()
    .setId(1234)
    .setName("John Doe")
    .setEmail("jdoe@example.com")
    .build();
output = new FileOutputStream(args[0]);
john.writeTo(output);
```

you can deserialize data -- via -- methods protocol buffers | other languages like C++

```cpp
Person john;
fstream input(argv[1], ios::in | ios::binary);
john.ParseFromIstream(&input);
int id = john.id();
std::string name = john.name();
std::string email = john.email();
```

## Protocol Buffers Definition Syntax {#syntax}

* fields
  * `optionality dataType nameToGive numberToGive`
    * optionality
      * `optional`  -- proto2 & proto3--
      * `repeated` -- proto2 & proto3 --
      * `required` -- proto2 --
    * dataType 
      * Scalar value types -- Check [Scalar Value Types](/programming-guides/proto3#scalar) --
      * `message`
        * allows
          * 👁nesting parts 👁
      * `enum`
        * allows
          * 👁specifying a set of values from 👁
      * `oneof`
        * uses
          * if it has got several optional fields, BUT >=1 it's set
      * `map`
    * nameToGive
      * ⚠️once they are set & used in production -> impossible to change ⚠️
      * Check [Message and Field Names](/programming-guides/style#message-field-names)
      * ⚠️ do NOT use `-` ⚠️
      * if field is repeated -> use pluralized names
    * numberToGive
      * ⚠️ can NOT be reused ⚠️
        * == although you delete the field -> NOT reassign it
    * [Specifying Field Rules](/programming-guides/proto3#specifying-field-rules).)
* extensions
  * 👁 ONLY allowed | "proto2" 👁
  * allows
    * fields -- are defined -- outside of the message
  * _Example:_ the protobuf library's internal message schema -- allows extensions for -- custom, usage-specific options
* check
  * [proto2](/programming-guides/proto2)
  * [proto3](/programming-guides/proto3)

## Additional Data Type Support {#data-types}

* many scalar value types
  * _Example:_ integers
* create your own composite data types
  * how?
    * define messages / are, themselves, data types / you -- can assign to a -- field
* other [common types](/programming-guides/dos-donts#common)

## History {#history}

* [History of Protocol Buffers](/history)

## Protocol Buffers Open Source Philosophy {#philosophy}

* open sourced | 2008
  * Reason: 🧠 provide developers outside of Google / SAME benefits 🧠
* regular updates to the language
* accept selected PR -- from -- external developers / 👀priority is ALWAYS Google’s specific needs 👀

## Developer Community {#community}

* [join the Google Group](https://groups.google.com/g/protobuf)

## Additional Resources {#additional-resources}

*   [Protocol Buffers GitHub](https://github.com/protocolbuffers/protobuf/)
*   [Codelabs](/getting-started/codelabs)
