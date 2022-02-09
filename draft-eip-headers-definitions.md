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

normative:

informative:


--- abstract

TODO Abstract


--- middle

# Introduction

The EIP headers could be carried in different ways inside the IPv6 Header. Likely at the end of the analysis/design phase one of the different mechanisms will be selected. For the time being for the purpose of research and experimentation we keep the different alternatives open.

# Work in progress definition of EIP Option for HBH EH

                                   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
                                   | Option type   |Opt Data Len=xx|
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |         EIP-TLVs (variable in number and length)              |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |                         ......                                |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |                         ......                                |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+


Option type
   +-+-+-+-+-+-+-+-+
   |0|0|1|EIP code |
   +-+-+-+-+-+-+-+-+
   
   First 3 bits in Option type field: (keep same formatting for inner TLVs)
   - 0 0 Skip if not implemented
   - 1 Content might change at every hop
   
   EIP code needs (eventually) to be allocated by IANA
   for the time being we use 11110, so overall the Option Type is
   
   0x3E	001	11110	RFC3692-style Experiment
   
   NB current IANA allocation for Option Types starting with 001 is
   (see https://www.iana.org/assignments/ipv6-parameters/ipv6-parameters.xhtml)
   
   32 possible Option Types starting with 001
   2 allocated by RFCs
   2 temporary allocated by Internet Drafts
   1 allocated for RFC3692-style Experiment
   27 not allocated
   
   Opt Data Len is the lenght in bytes of the rest of the Option
   
   Within the EIP Option Type, we have a TLV structure
   
      EIP-TLV a code that is specific of each EIP TLV
      TLV Len is the lenght in bytes of the rest of the TLV

Generic format for EIP TLVs

    0                   1                   2                   3
    0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |0| EIP-TLV code|TLV Data Len=xx|               |               |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |              TLV content (variable lenght)                    |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+

    0                   1                   2                   3
    0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |1|  EIP extended TLV code      |TLV Data Len=x |               |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |              TLV content (variable lenght)                    |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+



   
 HMAC TLV
   
   Alignment requirement: 8n

   The keyed Hashed Message Authentication Code (HMAC) TLV is OPTIONAL
   and has the following format:

    0                   1                   2                   3
    0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |   EIP extended TLV code       |     Length    |  RESERVED     |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |                      HMAC Key ID (4 octets)                   |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |                                                              //
   |                      HMAC (variable)                         //
   |                                                              //
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+

EIP extended TLV code:  HMAC = TBA

   Length:  The length of the variable-length data in bytes.

   RESERVED:  16 bits.  MUST be 0 on transmission.

   HMAC Key ID:  A 4-octet opaque number that uniquely identifies the
      pre-shared key and algorithm used to generate the HMAC.

   HMAC:  Keyed HMAC, in multiples of 8 octets, at most 32 octets.  
   
   --- Timestamps TLV:

    0                   1                   2                   3
    0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |0|EIP-TLV code | TLV Data Len  |  Timestamps TLV Parameters    |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |           Timestamps TLV content (variable lenght)            |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+

EIP-TLV code: Timestamps = TBA

Timestamps TLV Parameters

    0                   1           
    0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |  Timestamps TLV Parameters    |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+


    0                   1           
    0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |    Type       | Parameters    |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+

Type : further characterizes the format and the content of Timestamps

Timestamp Type: Basic = 1

    0                   1           
    0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |    Type=1     |LEN|Format |   |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+

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


Example of a Timestamp TLV of type basic, that carries 8 timestamps 
of length 2 bytes, each one representing a time granularity of 10 us. 

    0                   1                   2                   3
    0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
                                   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
                                   |0|0|1|   EIP   |Opt Data Len=20|
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   | Timestamps    |  TLV Len=18   |    Type=1     |0 1|0 1 0 1|0 0|
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |               1               |               2               |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |               3               |               4               |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |               5               |               6               |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |               7               |               8               |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+

NB using this granularity (10 us) and timestamp size (2 bytes), assuming that 
all node clocks are synchronized with a maximum error E [ms] it is possible to 
correctly evaluate hop-by-hop delays up to (655-E) [ms]

The entire timestamp will be reconstructed at last node using system
time and subctracting intermediate timestamps.

# Work in progress definition of EIP TLV for SRH

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
