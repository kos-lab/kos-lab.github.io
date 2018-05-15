---
layout: post
title: Hello World and Intelbras Ncloud vulnerability disclosure
author: Pedro Aguiar
---

Author: Pedro Aguiar (pedro.aguiar@kryptus.com)

Welcome to the first post in this blog, written by the Kos-Lab team, the Cyber Security unit of [Kryptus](https://kryptus.com), a brazilian Cyber Defense company.

A few weeks ago, we got our hands in a Intelbras Ncloud 300 device, an interesting wireless router, with features like a Torrent Client and USB storage, after a few days taking a look at it, the following was discovered:

## Unprotected Serial port (UART)

Connecting to the serial interface, a root shell was obtained, using this method, the root password hash was also obtained:
`root:uax./Fu15Jqo6:0:0:Adminstrator:/:/bin/sh`

John the Ripper quickly cracked that hash, the password is: cary

![Ncloud0.jpeg]({{ site.baseurl }}/images/Ncloud0.jpeg)

## Unauthenticated configuration backup

When a request to /cgi-bin/ExportSettings.sh is done, a configuration "backup" is retrieved, which includes the web interface username and password: 

![Ncloud1.png]({{ site.baseurl }}/images/Ncloud1.png)

The following CVE was assigned to this issue: CVE-2018-11094

## Unauthenticated wireless password disclosure

When a request to /goform/updateWPS is done, the wireless network password is disclosed:

![Ncloud2.png]({{ site.baseurl }}/images/Ncloud2.png)

The URL /goform/vpnBasicSettings is also vulnerable to this, being possible to enable or disable the VPN.

## Unauthenticated device reboot

When a request to /goform/RebootSystem is done, the device reboots:

![Ncloud3.png]({{ site.baseurl }}/images/Ncloud3.png)

Timeline:  
9 May, 2018 - Vendor was notified via email about the vulnerability  
10 May, 2018 - Vendor responded to the email asking for more details  
14 May, 2018 - Vendor called us and informed us that the product was discontinued and there was no possibility of a patch being developed, they also agreed with full disclosure.  