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
EIP provides a common solution which can be extended/tailored for the different use cases.

The design of the EIP header takes into account the requirement to be
efficient and "hardware friendly". 

The benefits of having EIP as a common header and framework to support 
multiple use cases will be discussed in this document.

The EIP header could be carried in different ways inside the IPv6 Header.
Likely at the end of the analysis/design phase one of the different mechanisms will be selected.
For the time being for the purpose of the discussion we keep different alternatives open:
1) EIP Option for Hop-by-Hop Extension Header; 2) EIP TLV for SRH

# Benefits of a common EIP header for multiple use cases.

1) The number of available Option Type in HBH header is limited, likewise the number of available TLVs in the Segment Routing Header (SRH) is limited. Defining multiple Option Types or SRH TLVs for multiple use case is not scalable and puts pressure on the allocation of codepoints.

2) The definition and standardization of specific EIP Information Elements (TLVs in the EIP Option for HBH EH or sub-TLVs for SRH TLV) for the different use cases is much simpler.

3) Different use case will share the EIP Information Elements.

4) Efficient and Hardware Friendly mechanisms can be defined when the different EIP Information Elements are carried inside the same EIP header.

# Definition of EIP Option for HBH EH (work in progress)

~~~
                                   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
                                   | Option type   |Opt Data Len=xx|
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |         EIP-TLVs (variable in number and length)              |
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
   for the time being we use 11110, so overall the Option Type is
   
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

Generic format for EIP LTVs

Considering that we can have codes of different size, we use LTV (Length-Tag-Value)
instead of TLVs, as shown in the following figure.
The LTV Data Len is in octets (up to 255 bytes for the LTV length).

~~~
    0                   1                   2                   3
    0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |LTV Data Len=xx|0| EIP-TLV code|               |               |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |              LTV content (variable lenght)                    |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+

    0                   1                   2                   3
    0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |LTV Data Len=xx|1|  EIP extended TLV code      |               |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |              LTV content (variable lenght)                    |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
~~~

LTV Data Len in octets (up to 252 bytes for the LTV)

We can use another approach to differentiate between different LTV codes.
The first two bits of the Len field can differentiate the code length,
and the remaining 6 bits will specify the length in 32 bits units (4-octets units).
The LTV length can be up to 63x4=252 bytes.

~~~
    0                   1                   2                   3
    0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |0 0| Data Len  | EIP-LTV code  |               |               |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |              LTV content (variable lenght)                    |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+

    0                   1                   2                   3
    0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |0 1| Data Len  |    EIP extended LTV code      |               | 
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |              LTV content (variable lenght)                    |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+

    0                   1                   2                   3
    0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |1 0| Data Len  |       EIP double-extended LTV code            | 
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |              LTV content (variable lenght)                    |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
~~~

In this way, we can have 256 codes of one byte, 65536 codes of 2 bytes
and 2^24 codes of 3 bytes.

# Definition of EIP LTVs
   
## HMAC LTV
   
   Alignment requirement: 8n

   The keyed Hashed Message Authentication Code (HMAC) TLV is OPTIONAL
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

OLD APPROACH
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

Type : further characterizes the format and the content of Timestamps

Timestamp Type: Basic = 1

~~~
    0                   1           
    0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |    Type=1     |LEN|Format |RES|
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
~~~

LEN: the lenght of each timestamp

00 1 byte

01 2 bytes

10 4 bytes

11 8 bytes

Format: indicates the format of the timestamp

0001 :   1 ns

0010 :  10 ns

0011 : 100 ns

0100 :   1 us

0101 :  10 us 

0110 : 100 us 

0111 :   1 ms

1000 : NTP (only for 8 bytes)

1001 : Linux epoch (only for 8 bytes)

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
time and subctracting intermediate timestamps.

# Definition of EIP TLV for SRH (work in progress) 

First we need to define the EIP TLV. A generic TLV in the SRH is defined as follows.

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

Then we need to define a sub-TLV inside the EIP TLV.
We present only the LTV approach (first the Length, then the type)

~~~
    0                   1                   2                   3
    0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
                                   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
                                   |1|   EIP       |    Length     | 
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |0 0| Data Len  | EIP-LTV code  |               |               |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |              LTV content (variable lenght)                    |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+

    0                   1                   2                   3
    0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
                                   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
                                   |1|   EIP       |    Length     | 
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |0 1| Data Len  |    EIP extended LTV code      |               | 
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |              LTV content (variable lenght)                    |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
~~~



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
