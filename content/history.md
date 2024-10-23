+++
title = "History"
weight = 1020
description = "A brief history behind the creation of protocol buffers."
type = "docs"
+++

## Why Did You Release Protocol Buffers? {#why}

* used by many projects inside Google /
  * some of them -- were pending to become -- open source projects
    * -> Protocol buffers was made public
* provide public APIs / accept
  * protocol buffers
  * XML
    * Reason: 🧠-- be converted to -- protocol buffers 🧠

## Why Is the First Release Version 2? What Happened to Version 1? {#version-1}

* "Proto1"
  * started early 2001
  * evolved chaotically
    * if someone needed a feature -> accepted to be implemented
* "Proto2"
  * == complete refactor of "Proto1" /
    * 👀-- NO dependant of -- NON-public Google libraries 👀
    * main design & implementation ideas == "Proto1"'s ones

## Why the Name "Protocol Buffers"? {#name}

* Originally, there was
  * 👀NO protocol buffer compiler 👀
  * a class named "ProtocolBuffer" / -- acted as -- buffer | individual method
    * == store bytes | buffer / once the message is built -> written out 
    * you could add tag/value pairs to the buffer -- via calling -- `AddValue(tag, value)`
* Nowadays
  * 👀"buffers" hast lost its meaning 👀
  * terms used
    * "protocol message" -- refers to -- message in an abstract sense
    * "protocol buffer" -- refers to -- serialized copy of a message
    * "protocol message object" -- refers to -- in-memory object / represents the parsed message

## Does Google Have Any Patents on Protocol Buffers? {#patents}

* NO

## How Do Protocol Buffers vs ASN.1, COM, CORBA, and Thrift? {#differ}

* use -- depends on the -- case
* Protocol buffers == interchange format
  * 👀-> NOT tied to -- RPC implementation or protocol 👀
* CORBA & Thrift
  * define both
    * interchange format &
    * RPC protocol
* ASN.1 == interchange format
* COM -- enables -- inter-process communication / NOT define an RPC protocol