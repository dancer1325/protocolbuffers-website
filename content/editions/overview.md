+++
title = "Protobuf Editions Overview"
linkTitle = "Overview"
weight = 42
description = "An overview of the Protobuf Editions functionality."
type = "docs"
+++

* Protobuf Editions
  * == collection of [features](/editions/features)
      * vs hardcoded behaviors / default value | older versions
      * == options / can be override between them (Check [lexical scoping](#scoping)) |
          * file
          * message
          * field
          * enum
          * ...
      * allows specifying the behavior of
          * protoc
          * code generators
          * protobuf runtimes
  * allows
      * language can evolve incrementally over time
  * -- replace the -- proto2 and proto3 designations
      * allows
          * 👁️ specifying the default behaviors | your file 👁️
      * | top of proto definition files
          * `syntax = "proto2"` or `syntax ="proto3"` -> `edition = "editionNumber"` (-- _Example:_ `edition = "2023"` --) 

## Lifecycle of a Feature {#lifecycles} | Protobuf Editions

* fundamental increments / lifecycle of a feature
* lifecycle
  * introducing
  * changing its default behavior
  * deprecating it
  * removing it
* _Example:_
  * Protobuf Edition 2031 creates `feature.amazing_new_feature` / `false` as default value
    * `false` as default value == backward compatibility
  * Developers update their ".proto" to `edition = "2031"`
  * Protobuf Edition 2033, switches the default from `false` -> `true`
    * if you use Prototiller tool (earlier versions of ".proto" -- are migrated quickly to --  ".proto" edition 2033) -> adds explicit `feature.amazing_new_feature = false`
      * if you want use the new behavior -> remove these newly-added settings
  * `feature.amazing_new_feature` is marked deprecated | edition x
    * -> `feature.amazing_new_feature` is removed | edition x+1 (major version)
      * -> code generators for that behavior & the runtime libraries / support it -- may also be -- removed
* if you do NOT use deprecated features | ANY `.proto` -> NO-op upgrade from edition x -- to the edition x+1

## Migrating to Protobuf Editions {#migrating}

* Protobuf Editions
  * will NOT
    * break existing binaries
    * change a message's binary, text, or JSON serialization format
  * v1 == proto2 + proto3 definitions
  * release 1 / year

### Proto2 -> Editions {#proto2-migration}

* ".proto2" file -- and run the Prototiller tool to change to -> | Protobuf Editions

<section class="tabs">

#### Proto2 syntax {.new-tab}

```proto
// proto2 file
syntax = "proto2";

package com.example;

message Player {
  // optional fields have explicit presence | proto2
  optional string name = 1;
  // still supports "required" field rule | proto2 
  required int32 id = 2;
  // NOT packed by default | proto2 
  repeated int32 scores = 3;

  enum Handed {
    HANDED_UNSPECIFIED = 0;
    HANDED_LEFT = 1;
    HANDED_RIGHT = 2;
    HANDED_AMBIDEXTROUS = 3;
  }

  // enums are closed | proto2 
  optional Handed handed = 4;

  reserved "gender";
}
```

#### Editions syntax {.new-tab}

```proto
// Edition version of proto2 file
edition = "2023";

package com.example;

message Player {
  // fields have explicit presence -> NOT need to explicitly set
  string name = 1;
  // to match the proto2 behavior -> LEGACY_REQUIRED is set at the field level
  int32 id = 2 [features.field_presence = LEGACY_REQUIRED];
  // to match the proto2 behavior -> EXPANDED is set at the field level
  repeated int32 scores = 3 [features.repeated_field_encoding = EXPANDED];

  enum Handed {
    // this overrides the default edition 2023 behavior, which is OPEN
    option features.enum_type = CLOSED;
    HANDED_UNSPECIFIED = 0;
    HANDED_LEFT = 1;
    HANDED_RIGHT = 2;
    HANDED_AMBIDEXTROUS = 3;
  }

  Handed handed = 4;

  reserved gender;
}
```

</section>

### Proto3 to Editions {#proto3-migration}

* ".proto3" file -- and run the Prototiller tool to change to -> | Protobuf Editions

<section class="tabs">

#### Proto3 syntax {.new-tab}

```proto
// proto3 file
syntax = "proto3";

package com.example;

message Player {
  // optional fields have explicit presence | proto3
  optional string name = 1;
  // NOT specified field rule defaults to implicit presence | proto3
  int32 id = 2;
  // packed by default | proto3
  repeated int32 scores = 3;

  enum Handed {
    HANDED_UNSPECIFIED = 0;
    HANDED_LEFT = 1;
    HANDED_RIGHT = 2;
    HANDED_AMBIDEXTROUS = 3;
  }

  // in proto3 enums are open
  optional Handed handed = 4;

  reserved "gender";
}
```

#### Editions syntax {.new-tab}

```proto
// Editions version of proto3 file
edition = "2023";

package com.example;

message Player {
  // fields have explicit presence, so no explicit setting needed
  string name = 1;
  // to match the proto3 behavior, IMPLICIT is set at the field level
  int32 id = 2 [features.field_presence = IMPLICIT];
  // PACKED is the default state, and is provided just for illustration
  repeated int32 scores = 3 [features.repeated_field_encoding = PACKED];

  enum Handed {
    HANDED_UNSPECIFIED = 0;
    HANDED_LEFT = 1;
    HANDED_RIGHT = 2;
    HANDED_AMBIDEXTROUS = 3;
  }

  Handed handed = 4;

  reserved gender;
}
```

</section>

<a name="inheritance"></a>

### Lexical Scoping {#scoping}

* Protobuf Editions supports lexical scoping / list of allowed targets / per-feature
  * allows
    * setting the default behavior for a feature | entire file & override that behavior |
      * message
      * field
      * enum
      * enum value
      * oneof
      * service
      * method  
  * _Example:_  features | `edition1"` -- can be specified ONLY -- | file level or the lowest level of granularity
* _Example:_ features being set | file, field, and enum level

```proto {highlight="lines:3,7,16"}
edition = "2023";

option features.enum_type = CLOSED;

message Person {
  string name = 1;
  int32 id = 2 [features.presence = IMPLICIT]; // default value is `EXPLICIT`

  // `CLOSED`  because it applies the file-level setting
  enum Pay_Type {
    PAY_TYPE_UNSPECIFIED = 1;
    PAY_TYPE_SALARY = 2;
    PAY_TYPE_HOURLY = 3;
  }

  // `OPEN` becauseit is set | enum
  enum Employment {
    option features.enum_type = OPEN;
    EMPLOYMENT_UNSPECIFIED = 0;
    EMPLOYMENT_FULLTIME = 1;
    EMPLOYMENT_PARTTIME = 2;
  }
  Employment employment = 4;
}
```

### Prototiller {#prototiller}

* := migration tooling /
  * allows
    * making easier the migration to and between editions
    * manipulating ".proto" in other ways

### Backward Compatibility {#compatibility}

* Protobuf Editions are minimally disruptive as possible
  * _Example:_ proto2 and proto3 definitions <-- can be imported into -> editions-based

```proto
// file myproject/foo.proto
syntax = "proto2";

enum Employment {
  EMPLOYMENT_UNSPECIFIED = 0;
  EMPLOYMENT_FULLTIME = 1;
  EMPLOYMENT_PARTTIME = 2;
}
```

```proto
// file myproject/edition.proto
edition = "2023";

import "myproject/foo.proto";
```
* once you migrate to another version
  * generated code changes
  * wire format does NOT change
    * 👁️== using editions-syntax proto definitions -- you can access -- proto2 and proto3 data files or file streams 👁️
