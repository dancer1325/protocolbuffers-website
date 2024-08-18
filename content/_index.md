+++
title = "Protocol Buffers"
weight = 5
toc_hide = "true"
description = "Protocol Buffers are language-neutral, platform-neutral extensible mechanisms for serializing structured data."
type = "docs"
no_list = "true"
+++

## What Are Protocol Buffers?

* Check [README.md](https://github.com/dancer1325/protocolbuffers-website/blob/main/content/README.md)

## Pick Your Favorite Language

* Check [README.md](https://github.com/dancer1325/protocolbuffers-website/blob/main/content/README.md)

## Example Implementation

```proto
message Person {
  optional string name = 1;
  optional int32 id = 2;
  optional string email = 3;
}
```

**Figure 1.** A proto definition.

```java
// Java code
Person john = Person.newBuilder()
    .setId(1234)
    .setName("John Doe")
    .setEmail("jdoe@example.com")
    .build();
output = new FileOutputStream(args[0]);
john.writeTo(output);
```

**Figure 2.** Using a generated class to persist data.

```cpp
// C++ code
Person john;
fstream input(argv[1],
    ios::in | ios::binary);
john.ParseFromIstream(&input);
id = john.id();
name = john.name();
email = john.email();
```

**Figure 3.** Using a generated class to parse persisted data.

## How Do I Start?

<ol>

  <li>
    <a href="https://github.com/protocolbuffers/protobuf#protobuf-compiler-installation">Download
    and install</a> the protocol buffer compiler.
  </li>

  <li>
    Read the
    <a href="/overview">overview</a>.
  </li>
  <li>
    Try the <a href="/getting-started">tutorial</a> for your
    chosen language.
  </li>
</ol>
