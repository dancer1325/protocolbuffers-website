* Why to release it?
  * used by many projects inside Google / some of them were pending to become open source projects
  * provide public APIs / accept protocol buffers
* Why is "Proto2" the first version?
  * "Proto1" evolved chaotically
  * "Proto2"
    * == complete refactor of "Proto1" /
      * NO dependant of non-public Google libraries
* What's the root cause of the name "Protocol Buffers"?
  * Original, there was a class named "ProtocolBuffer" / -- acted as -- buffer | individual method
    * == add tag/value pairs to the buffer -- via calling -- `AddValue(tag, value)`
  * Nowadays
    * "buffers" hast lost its meaning
    * "protocol message" -- refers to -- message in an abstract sense
    * "protocol buffer" -- refers to -- serialized copy of a message
    * "protocol message object" -- refers to -- in-memory object / represents the parsed message
* vs ASN.1 or COM or CORBA or Thrift
  * use -- depends on the -- case