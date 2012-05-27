---
layout: post
title: "PL/SQL Implementation of PKCS #7 Padding"
date: 2011-07-02 12:00
comments: true
categories: Programming
tags: [AES, Oracle, Padding, PKCS7, pl/sql, SQL]
---
I've been working on an Oracle package for encrypting/decrypting data using AES256.  I need the package to be compatible with a separate C# implementation of AES256 I'm also working on.  In the .NET AESManaged Class, the default padding mode is PKCS #7, which the Oracle DBMS_CRYPTO package does not support.

So I've thrown together an implementation of PKCS7 padding in PL/SQL to use on the Oracle side.  Details about the PKCS7 padding are described in [RFC 5652](http://tools.ietf.org/html/rfc5652#section-6.3).

###fn_PKCS7_Pad:
Pads the raw data out to a multiple of the block size. Block size is in bytes.

{% gist 2814946 %}

Note: If the data is already a multiple of the block size then the data is padded with one full block. This is per the RFC.

###fn_PKCS7_Trim:
Function trims the padding off of the incoming raw data.

{% gist 2814959  %}

The trim function is currently making some fairly broad assumptions about the data. One assumption is that the data is in fact padded.  Also it is trimming off the number of bytes dictated by the last byte without actually checking to see if the previous bytes have the same value and the correct number of pad bytes are present.  When I get some time, I'll go back and add some sanity checks.


###Test Code:

{% gist 2814993 %}


###Output:
```text 
------------------------------------
IN:         AABBCC
BLOCK SIZE: 8
PADDED:     AABBCC0505050505
TRIMMED:    AABBCC
------------------------------------
------------------------------------
IN:         AABBCCDDEEFF
BLOCK SIZE: 8
PADDED:     AABBCCDDEEFF0202
TRIMMED:    AABBCCDDEEFF
------------------------------------
------------------------------------
IN:         AABBCCDDEEFFAABB
BLOCK SIZE: 8
PADDED:     AABBCCDDEEFFAABB0808080808080808
TRIMMED:    AABBCCDDEEFFAABB
------------------------------------
```

[Full Code in Text File](/files/plsql_pkcs7_padding.txt)
