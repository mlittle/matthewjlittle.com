---
layout: post
title: ".NET IPAddress Class"
date: 2010-12-05 12:00
comments: true
categories: programming
tags: [.NET, C#, MSDN, Programming]
---
I've been working on a C# version of a COM component our applications at work use to integrate with an Avaya predictive dialing system. While implementing the code for the communication piece, I found a quirk about the .NET IPAddress class I had not run into before.

The communication is being done with asynchronous sockets. The Socket class requires an IPEndPoint, which in turn takes an IPAddress and port (integer).  Fair enough.  For my purposes I just needed to connect using the IP not the host name.   So the next logical step in my mind would be to new up an instance of IPAddress with the IP I'm attempting to connect to.  The problem I ran into is that the IPAddress constructor doesn't accept an IP! At least not in the [Dot-decimal notation](http://en.wikipedia.org/wiki/Dot-decimal_notation/) (x.x.x.x). Instead, I would need to know the byte array, int64, or long representation of the address.

{% img center /files/IPAddress1-e1280089688435.png %}

Well it turns out you have to call the static Parse() method on the IPAddress class passing in the IP which in turn will return an instance of the class.  Parse() takes dotted-quad IPv4 or colon-hexadecimal IPv6. It seems obvious now and only took a minute or so digging around in the MSDN documentation to figure out.   Just wasn't what I was expecting.  Apparently I need to brush up on my "converting ip address to byte[]" in my head skills.
