---
title: "Extensible In-band Processing Headers Definitions"
abbrev: "EIP Headers"
category: info

docname: draft-eip-headers-definitions-latest
ipr: trust200902
area: AREA
workgroup: SIG on EIP
keyword: Internet-Draft
venue:
  group: EIP
  type: SIG
  mail: eip@postino.cnit.it
  arch: http://postino.cnit.it/cgi-bin/mailman/private/eip/
  github: eip-home/eip-headers
  latest: https://eip-home.github.io/eip-headers/draft-eip-headers-definitions.html

stand_alone: yes
smart_quotes: no
pi: [toc, sortrefs, symrefs]

author:
 -
    name: "Stefano Salsano"
    organization: Univ. of Rome Tor Vergata / CNIT
    email: "stefano.salsano@uniroma2.it"
 -
    name: "Hesham ElBakoury"
    organization: Consultant
    email: "helbakoury@gmail.com"

normative:

informative:


--- abstract

This document discussed the EIP header format.

Caveat: this document is at an early brainstorming stage, 
it is distributed only to stimulate discussion.

--- middle

# Introduction

Caveat: this document is at an early brainstorming stage, 
it is distributed only to stimulate discussion.

The EIP header is used to carry information that IPv6 nodes can read and write to implement different use cases.
EIP provides a common framework which can be extended/tailored for the different use cases.

The design of the EIP header takes into account the requirement to be
efficient and "hardware friendly". 

The benefits of having EIP as a common header and framework to support 
multiple use cases will be discussed in this document.

The EIP header could be carried in different ways inside the IPv6 Header:
1) EIP Option for Hop-by-Hop Extension Header; 2) EIP TLV for Segment Routing Header
At the end of the analysis/design phase either only one of the different mechanisms will be selected or more than one will be specified, to be used depending on the scenario.

# Benefits of a common EIP header for multiple use cases.

There are reasons why it is beneficial to define a common EIP header that has multiple use cases.
The EIP header will carry different EIP Information Elements that are defined to support the different use cases. 

1) The number of available Option Types in HBH header is limited, likewise the number of available TLVs in the Segment Routing Header (SRH) is limited. Defining multiple Option Types or SRH TLVs for multiple use case is not scalable and puts pressure on the allocation of such codepoints.

2) The definition and standardization of specific EIP Information Elements for the different use cases is much simpler, rather than requiring the definition of a new Option Type or SRH TLVs.

3) Different use cases may share the EIP Information Elements.

4) Efficient and Hardware Friendly mechanisms can be defined when the different EIP Information Elements are carried inside the same EIP header.

# Definition of EIP Option for HBH EH 

The EIP header can be carried as an Option in the Hop by Hop Extension Header, as shown in the following figure.

~~~
                                   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
                                   | Option type   |Opt Data Len=xx|
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |   EIP Information Elements (variable in number and length)    |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |                         ......                                |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |                         ......                                |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
~~~


Option type

~~~
   +-+-+-+-+-+-+-+-+
   |0|0|1|EIP code |
   +-+-+-+-+-+-+-+-+
~~~

   First 3 bits in Option type field: 

   - 0 0 Skip if not implemented
   
   - 1 Content might change at every hop
   
   (eventually) EIP code needs to be allocated by IANA
   for the time being we use 11110, so overall the Option Type for EIP is
   
   0x3E	001	11110	RFC3692-style Experiment
   
   NB the current IANA allocation for Option Types starting with 001 is
   (see https://www.iana.org/assignments/ipv6-parameters/ipv6-parameters.xhtml)
   
   32 possible Option Types starting with 001
   2 allocated by RFCs
   2 temporary allocated by Internet Drafts
   1 allocated for RFC3692-style Experiment
   27 not allocated
   
   Opt Data Len is the lenght in bytes of the rest of the EIP Option
   
   Within the EIP Option, we have a LTV structure:
   
      EIP-LTV a code that is specific of each EIP LTV
      LTV Len is the lenght in bytes of the rest of the LTV


# Definition of EIP TLV for SRH 

The EIP header can be carried as a TLV in the Segment Routing Header. A generic TLV in the SRH is defined as follows.

~~~
    0                   1
    0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-----------------------
   |     Type      |    Length     | Variable-length data
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-----------------------
~~~

   First bit in type field:
   1 - Content might change at every hop
   
   type code needs (eventually) to be allocated by IANA
   for the time being we use 252 for the EIP TLV. 
   This is part of the experimental range

   252-254 	Experimentation and Test 	[RFC8754]

   NB current IANA allocation for Types starting with 1 is
   (see https://www.iana.org/assignments/ipv6-parameters/ipv6-parameters.xhtml#segment-routing-header-tlvs)
   
   128-251 	Unassigned 	

   252-254 	Experimentation and Test 	[RFC8754]

   255 	Reserved 	[RFC8754]
 
   127 possible Option Types starting with 001
   
   1 Reserved
   
   3 allocated for Experimentation and Test
   
   123 not allocated

The EIP TLV for SRH will carry a set of EIP Information Elements as shown hereafter.

~~~
    0                   1                   2                   3
    0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
                                   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
                                   |1|   EIP       |    Length     | 
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |   EIP Information Elements (variable in number and length)    |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |                         ......                                |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |                         ......                                |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+

~~~


# Generic format for EIP Information Elements

EIP Information Elements are used to carry the information needed by the different use cases.
The same Information Element can be reused across multiple use cases.

An fundamental requirement for EIP is to be "Extensible", therefore we need to have a potentially large number of different Information Elements. On the other hand, we may need to be efficient, limiting the overhead in bytes for carrying a given information. In order to have the possibility to find the optimal trade-off between these contrasting requirements, the Codes or "Tags" for the Information Elements will be variable in length.

In order to support the variability in the size of the Code of the Information Element, we use an LTV (Length-Tag-Value) approach instead of TLVs (Tag-Length-Value). 

We are currently considering two approaches for the structure of the EIP Information Elements or EIP LTVs

## Proposed approach #1 for EIP LTVs.

In this approach, we have one byte LTV Data Length field. The LTV Data Len is in octets (up to 255 bytes for the LTV Data Length).

~~~
    0                   1                   2                   3
    0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |LTV Data Len=xx|0| EIP-TLV code|       LTV content             |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |          LTV content (optional, variable lenght)              |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+

    0                   1                   2                   3
    0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |LTV Data Len=xx|1|  EIP extended TLV code      | LTV content   |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |          LTV content (optional, variable lenght)              |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
~~~

In this approach, we can have 128 codes of one byte and 32768 codes of 2 bytes.

## Proposed approach #2 for EIP LTVs.

In this second approach, the first two bits of the Len field can differentiate the code length,
and the remaining 6 bits will specify the length of the optional part of the LTV content in 32 bits units (4-octets units). The LTV Data length can be up to 63x4=252 bytes (covering the optional part).

Note that when the Data Len is 0, the optional part of the LTV content is not present, and the Information Element is made only of 4 bytes (i.e. one row in the figures below).

~~~
    0                   1                   2                   3
    0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |0 1| Data Len  | EIP-LTV code  |        LTV content            |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |          LTV content (optional, variable lenght)              |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+

    0                   1                   2                   3
    0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |1 0| Data Len  |    EIP extended LTV code      | LTV content   | 
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |          LTV content (optional, variable lenght)              |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+

    0                   1                   2                   3
    0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |1 1| Data Len  |       EIP double-extended LTV code            | 
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |          LTV content (optional, variable lenght)              |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
~~~

In this approach, we can have 256 codes of one byte, 65536 codes of 2 bytes
and 2^24 codes of 3 bytes.

## Decision on the approach for EIP LTVs

The selected approach is the #2, because it is more flexible and it supports a much higher number of Information Elements Codes.


# Definition of EIP Information Elements (a.k.a. EIP LTVs)
   
## HMAC LTV
   
   Alignment requirement: 8n

   The keyed Hashed Message Authentication Code (HMAC) LTV is OPTIONAL
   and has the following format:

~~~
    0                   1                   2                   3
    0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |0 1|  Length   |    EIP extended LTV code      |  RESERVED     | 
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |                      HMAC Key ID (4 octets)                   |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |                                                              //
   |                      HMAC (variable)                         //
   |                                                              //
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
~~~

EIP extended TLV code:  HMAC = TBA

   Length:  The length of the variable-length data in bytes.

   RESERVED:  8 bits.  MUST be 0 on transmission.

   HMAC Key ID:  A 4-octet opaque number that uniquely identifies the
   pre-shared key and algorithm used to generate the HMAC.

   HMAC:  Keyed HMAC, in multiples of 8 octets, at most 32 octets.  

## Timestamps LTV:

OLD APPROACH (deprecated)

~~~
    0                   1                   2                   3
    0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |0|EIP-TLV code | TLV Data Len  |  Timestamps TLV Parameters    |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |           Timestamps TLV content (variable lenght)            |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
~~~

NEW APPROACH

~~~
    0                   1                   2                   3
    0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |0 0| Data Len  | EIP-LTV code  |  Timestamps TLV Parameters    |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |           Timestamps TLV content (variable lenght)            |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
~~~

EIP-LTV code: Timestamps = TBA

Timestamps TLV Parameters

~~~
    0                   1           
    0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |  Timestamps TLV Parameters    |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
~~~

~~~
    0                   1           
    0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |    Type       | Parameters    |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
~~~

Type : it further characterizes the format and the content of Timestamps

Timestamp Type: Basic = 1

~~~
    0                   1           
    0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |    Type=1     |LEN|Format |RES|
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
~~~

LEN: the lenght of each timestamp

~~~
00 : 1 byte
01 : 2 bytes
10 : 4 bytes
11 : 8 bytes
~~~

Format: indicates the format of the timestamp

~~~
0001 :   1 ns
0010 :  10 ns
0011 : 100 ns
0100 :   1 us
0101 :  10 us 
0110 : 100 us 
0111 :   1 ms
1000 : NTP (only for 8 bytes)
1001 : Linux epoch (only for 8 bytes)
~~~

RES: Reserved, set to 0 0 

Example of a Timestamp TLV of type basic, that carries 8 timestamps 
of length 2 bytes, each one representing a time granularity of 10 us. 

~~~
    0                   1                   2                   3
    0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
                                   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
                                   |0|0|1|   EIP   |Opt Data Len=20|
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |0 0| Len=4     |  Timestamps   |    Type=1     |0 1|0 1 0 1|0 0|
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |               1               |               2               |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |               3               |               4               |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |               5               |               6               |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |               7               |               8               |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
~~~

NB using this granularity (10 us) and timestamp size (2 bytes), assuming that 
all node clocks are synchronized with a maximum error E [ms] it is possible to 
correctly evaluate hop-by-hop delays up to (655-E) [ms]

The entire timestamp will be reconstructed at last node using system
time and subtracting intermediate timestamps.

## Processing Accelerator LTV

The purpose of this LTV is to identify a portion of the EIP Header that has a fixed pre-known format so that it can be processed in a more efficient way, rather than proceeding with a "sequential" parsing.
This is can be used for parallel hardware processing, but also software processing can be optimized.
For example, a pre-known sequence of LTVs following the Processing Accelerator LTV can be processed in parallel. The Processing Accelerator LTV can be used to know in advance the size and type of some fields of the following LTVs in order to speed up the processing.

~~~
    0                   1                   2                   3
    0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |0 0|  Len = 0  | Proc. Accel.  | Processing Acceleration ID    |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
~~~

Proc. Accel. : EIP-LTV code, To Be Assigned

The Processing Acceleration ID is an opaque identifier, its definition is domain-specific.

## Compact Path Tracing (CPT) LTV

This LTV is a porting of draft-filsfils-spring-path-tracing-00 into the EIP framework.

~~~
    0                   1                   2                   3
    0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |0 0|  Length   |         Compact PT            |Type |  RES    |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |                                                               |
   ~                          MCD  Stack                           ~
   |                                                               |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
~~~

Compact PT : EIP extended LTV code, To Be Assigned

Type : 3 bits, specifies the content of the CPT LTV including the format of the MCD element.

RES: Reserved, set to 00000

Ultra Compact (Type = 000)
MCD 24 Bits (3 bytes)   
Timestamp (8 bit) | Interface ID (12 bit) | Load (4 bit)

Compact (Type = 001)
MCD 32 Bits (4 bytes)
Timestamp (10 bit) | Interface ID (16 bit) | Load (4 bit) | Timeshift (2 bit) 

The MCD Stack has variable size. [draft-path-tracing] recommends 36 octets for a MCD of 3 bytes (12 MCDs).

In our case, taking into account the alignment requirements, we have the following recommendation.

When MCD is 3 bytes, we recommend 13 MCDs for a total of 39 bytes.
When MCD is 4 bytes, we recommend an even number of MCD, for example 10 for a total of 40 bytes or 12 for a total of 48 bytes.

### CPT Authenticated mode

~~~
    0                   1                   2                   3
    0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |0 0| Data Len  |         Compact PT            |Type |  RES    |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |                                                               |
   ~                          MCD  Stack                           ~
   |                                                               |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |                                                               |
   ~                             HMAC                              ~
   |                                                               |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
~~~

In the authenticated mode, an HMAC is appended at the end of the MCD Stack. The size of the HMAC is a multiple of 8 octects.

Each intermediate node calculates the HMAC for the whole CPT LTV, including the HMAC field. The newly calculated HMAC then overwrites the previous node's HMAC. The first node will just use an HMAC field set to all zeros.
An identifier for every single node is not needed, because it can be derived from the MCD Stack.
The destination node can reconstruct the different HMAC fields at heach hop to check if the final HMAC is consistent.


[draft-path-tracing] draft-filsfils-spring-path-tracing-00



# Conventions and Definitions

{::boilerplate bcp14-tagged}

# Security Considerations

TODO Security


# IANA Considerations

This document has no IANA actions.


--- back

# Acknowledgments
{:numbered="false"}

TODO acknowledge.
