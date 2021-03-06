---
layout: post
title: Hello World and Intelbras Ncloud vulnerability disclosure
author: Pedro Aguiar
---

Author: Pedro Aguiar (pedro.aguiar@kryptus.com)

Welcome to the first post in this blog, written by the Kos-Lab team, the Cyber Security Unit of [Kryptus](https://kryptus.com), a brazilian Cyber Defense company.

A few weeks ago, we got our hands in a Intelbras Ncloud 300 device, an interesting wireless router, with features like a Torrent Client and USB storage, after a few days taking a look at it, the following was discovered:

## Unprotected Serial port (UART)

Connecting to the serial interface, a root shell was obtained, using this method, the root password hash was also obtained:

{% highlight conf %}
root:uax./Fu15Jqo6:0:0:Adminstrator:/:/bin/sh
{% endhighlight %}

John the Ripper quickly cracked that hash, the password is: cary

Those credentials can be used on the telnet port, this is particularly critical because it is possible for the attacker to see any files the user has stored in the USB device connected.

In cases where the user root does not work, it was replaced with the web interface credentials, which can be obtained exploring the next vulnerability:

![Ncloud0.jpeg]({{ site.baseurl }}/images/Ncloud0.jpeg)

## Unauthenticated configuration backup

Even though the web interface of the device has a login system, it is not properly enforced, therefore, when a request to /cgi-bin/ExportSettings.sh is done, a configuration "backup" is retrieved, which includes the web interface username and password: 

![Ncloud1.png]({{ site.baseurl }}/images/Ncloud1.png)

The following CVE was assigned to this issue: CVE-2018-11094

## Unauthenticated wireless password disclosure

When a request to /goform/updateWPS is done, the wireless network password is disclosed:

![Ncloud2.png]({{ site.baseurl }}/images/Ncloud2.png)

The URL /goform/vpnBasicSettings is also vulnerable to this, being possible to enable or disable the VPN.

## Unauthenticated device reboot

When a request to /goform/RebootSystem is done, the device reboots:

![Ncloud3.png]({{ site.baseurl }}/images/Ncloud3.png)

## PoC
An [exploit](https://www.exploit-db.com/exploits/44637/) was created to demonstrate the issues described in this post:

![Ncloud4.png]({{ site.baseurl }}/images/Ncloud4.png)

Timeline:  
9 May, 2018 - Vendor was notified via email about the vulnerability  
10 May, 2018 - Vendor responded to the email asking for more details  
14 May, 2018 - Vendor called us and informed us that the product was discontinued and there was no possibility of a patch being developed, they also agreed with full disclosure.  
