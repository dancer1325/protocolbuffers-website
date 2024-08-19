+++
title = "Enum Behavior"
weight = 55
description = "Explains how enums currently work in Protocol Buffers vs. how they should work."
type = "docs"
+++

* Enums | different language libraries, behave different
* goal
  * different enum's behaviors
  * plans to move protobufs -> state / -- consistent across -- ALL languages
  * general content | proto*
    * [proto2](/programming-guides/proto2#enum)
    * [proto3](/programming-guides/proto3#enum)

## Definitions {#definitions}

* Enums flavors
  * are
    * *open*
    * *closed*
  * differences
    * handling of unknown values
* _Example:_ let's have `.proto` / NOT specified if it's `syntax = "proto2"` or `syntax = "proto3"`

  ```
  enum Enum {
    A = 0;
    B = 1;
  }
  
  message Msg {
    optional Enum enum = 1;
  }
  ```
  * *open* vs *closed* --  

    > What happens when a program parses binary data that contains field 1 with the value `2`?

      * **Open** enums
        * will parse the value `2` + store it directly | field
        * accessor will
          * -- report the field -- / being *set*
          * -- return -- something / represents `2`
      * **Closed** enums
        *  will parse the value `2` + store it | message's unknown field
        *  accessors will
          * -- report the field -- / being *unset*
          * -- return -- enum's default value

## Implications of *Closed* Enums

* if `repeated Enum` field is parsed -> ALL unknown values will be placed | [unknown field](/programming-guides/proto3/#unknowns) set
  * Once it is serialized -> those unknown values will be written again / NOT | original place in the list
    * _Example:_ given the `.proto`

    ```
    enum Enum {
      A = 0;
      B = 1;
    }
    
    message Msg {
      repeated Enum r = 1;
    }
    ```

      * wire format / contain the values `[0, 2, 1, 2]` / field 1 will parse to -> repeated field contains `[0, 1]` & the value `[2, 2]` will end up
stored as an unknown field
      * after reserializing the message, the wire format == `[0, 1, 2, 2]`
* if maps with *closed* enums / value is unknown -> their value will place entire entries (key and value) | unknown fields

## History {#history}

* TODO:
Prior to the introduction of `syntax = "proto3"` all enums were *closed*. Proto3
introduced *open* enums specifically because of the unexpected behavior that
*closed* enums cause.

## Specification {#spec}

The following specifies the behavior of conformant implementations for protobuf.
Because this is subtle, many implementations are out of conformance. See
[Known Issues](#known-issues) for details on how different implementations
behave.

*   When a `proto2` file imports an enum defined in a `proto2` file, that enum
    should be treated as **closed**.
*   When a `proto3` file imports an enum defined in a `proto3` file, that enum
    should be treated as **open**.
*   When a `proto3` file imports an enum defined in a `proto2` file, the
    `protoc` compiler will produce an error.
*   When a `proto2` file imports an enum defined in a `proto3` file, that enum
    should be treated as **open**.

## Known Issues {#known-issues}

### C++ {#cpp}

All known C++ releases are out of conformance. When a `proto2` file imports an
enum defined in a `proto3` file, C++ treats that field as a **closed** enum.

### C&#35; {#csharp}

All known C# releases are out of conformance. C# treats all enums as **open**.

### Java {#java}

All known Java releases are out of conformance. When a `proto2` file imports an
enum defined in a `proto3` file, Java treats that field as a **closed** enum.

> **NOTE:** Java's handling of **open** enums has surprising edge cases. Given
> the following definitions:
>
> ```
> syntax = "proto3";
>
> enum Enum {
>   A = 0;
>   B = 1;
> }
>
> message Msg {
>   repeated Enum name = 1;
> }
> ```
>
> Java will generate the methods `Enum getName()` and `int getNameValue()`. The
> method `getName` will return `Enum.UNRECOGNIZED` for values outside the known
> set (such as `2`), whereas `getNameValue` will return `2`.
>
> Similarly, Java will generate methods `Builder setName(Enum value)` and
> `Builder setNameValue(int value)`. The method `setName` will throw an
> exception when passed `Enum.UNRECOGNIZED`, whereas `setNameValue` will accept
> `2`.

### Kotlin {#java}

All known Kotlin releases are out of conformance. When a `proto2` file imports
an enum defined in a `proto3` file, Kotlin treats that field as a **closed**
enum.

Kotlin is built on Java and shares all of its oddities.

### Go {#go}

All known Go releases are out of conformance. Go treats all enums as **open**.

### JSPB {#jspb}

All known JSPB releases are out of conformance. JSPB treats all enums as
**open**.

### PHP {#php}

PHP is conformant.

### Python {#python}

After 4.22.0, Python is conformant.

In 4.21.x, Python is conformant by default, but setting
`PROTOCOL_BUFFERS_PYTHON_IMPLEMENTATION=python` will cause it to be out of
conformance.

Before 4.21.0, Python is out of conformance.

When a `proto2` file imports an enum defined in a `proto3` file, non-conformant
Python versions treat that field as a **closed** enum.

### Ruby {#ruby}

All known Ruby releases are out of conformance. Ruby treats all enums as
**open**.

### Objective-C {#obj-c}

After 22.0, Objective-C is conformant.

Prior to 22.0, Objective-C was out of conformance. When a `proto2` file imported
an enum defined in a `proto3` file, it would treat that field as a **closed**
enum.

### Swift {#swift}

Swift is conformant.

### Dart {#dart}

Dart treats all enums as **closed**.
