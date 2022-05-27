---
layout: post
title: Offensive Security PWK/OSCP Review
categories: [certifications]
tags: [offsec, pwk, oscp]
description: My review of Offensive Security's PWK/OSCP.
---

# Intro
Aside from an ethical hacking class at the university, I had no other experience with internal network penetration testing before hand, so I was quite fresh when starting at the PWK labs. Over the coarse of about six months I had 90 days of lab time, but real work and personal life took away much of the time, rendering me only with about 30 productive days in the lab.

My methodology doing the PWK lab was to focus 100% on each machine until it was rooted, before going to the next. In the beginning I just started to attack the first machine in the subnet and just followed the IP range naturally, but this soon stopped being possible. This is because the difficulty level does not follow the IP range. After this I started to go for low hanging fruit around the subnet instead. I used about half a day to a day on each machine, except the really easy ones, which took some minutes. This was a concern for me, because I was afraid I would not have enough time to finish enough machines on the exam, with only 24 hours available.

I decided to seek hints from the forums when I was stuck for more than an hour. If you are able to read between the lines among the replies saying "try harder" and "enumerate more", you can maybe extract an idea that will move you forward. I felt that this was better than using days stuck in the wrong rabbit hole, leading you nowhere, but maybe you would learn more from that, I don't know. This might be a huge discussion for another day.

I signed up for my first exam 16. of February 2018, which was the first available Friday. The exam started at 0800AM and at 1100AM I had root shell on the buffer overflow machine. I was feeling good and motivated! However, after 12 hours I only had one local shell more to show for. At this point I didn't see the light in the tunnel. My mindset now was to just do the rest of the exam for fun and learning. Around 0400AM, four hours before deadline, things changed. I was high on caffeine and I was in my ultra focused state of mind, which I get at night time. Programmers are probably familiar with the <a href="https://swizec.com/blog/why-programmers-work-at-night/swizec/3198">phenomenon</a>. The machines went down one by one and when the time was up, I only had one 25 point machine left. I was a little frustrated I couldn't root it, but overall I was happy I had enough points to pass. You are allowed to use Metasploit on one machine, and at this time I hadn't used it yet, but there was no time left. Instead of going to sleep, I immediately started to go over my notes to improve and supplement them. Better to do this now when the machines are fresh in memory, rather than after waking up.

After about 7 hours of sleep, I started to port my notes and screenshots from KeepNote to LibreOffice Writer. If I would do it again, I would strive to get a copy of Microsoft Word instead, for a more trivial word processor to work with. Uploading the exam and lab report was the worst part. Offensive Security are very strict about the formating. They state that they will fail you are not compliant with their policy. So after checking everything ten times I finally uploaded it.

On Monday morning an email from OffSec popped up on my phone. I was not expecting an answer this early, since it stated it would take up to three business days.

>Dear Daniel,
>
>We are happy to inform you that you have successfully completed the Penetration Testing with Kali Linux certification exam and have obtained your Offensive Security Certified Professional (OSCP) certification.

That message was a relief and a perfect way to start the week.

# Review
I was overall very happy with the PWK course. It was an amazing learning experience. It was indeed very hard and challenging, but also very fun. You are not only learning about technical penetration testing and methodology, but you'll get a better understanding about computers and networks in general. 

The videos in the course material is of the highest quality, and above everything you will find on YouTube. The exercises that comes in the PDF is part of my biggest complaint about the course and the exam. You get 5 extra points if you write a report for at least ten lab machines and answering all the lab exercises. First off, I think the exam should be totally independent and not grant extra points for doing other things. One could just have copied someone else's report and answers to get 5 free points, or did it themselves with infinite amount of time. It doesn't prove anything. The only good thing about following the exercises is that it will guide you to low hanging fruits in the lab. It is also a fine starting place if you are a complete beginner, but when people can get 5 points for free, no matter the skill level, they will probably do it. And doing the exercises while you have precious lab does not feel great. Some of the exercises are good, but others feels like a waste of time and backwards to do.

The pricing of the course is a great part about it. It's only $800 for 30 days lab time, course material and exam attempt. This makes it much easier for hobby pentesters to take the certification without the backing of the company they do not work for yet. Instead of partaking in one SANS course, you can actually take all the online Offsec courses (PWK, CTP and WiFu) and still have a lot of money left. These certifications never expire, does actually prove something, are highly respected and recognized, compared to a SANS certification.

# Resources, tips and tricks

For Linux privilege escalation I would strongly recommend using <a href="https://github.com/sleventyeleven/linuxprivchecker/blob/master/linuxprivchecker.py">linuxprivchecker.py</a>. Upload it to after you have initial local shell on a Linux host. Read the output carefully, but don't always trust the exploit suggestions at the bottom. Use Google instead.

Windows privilege escalation is a little different. There are tools like linuxprivchecker.py for Windows also, but I never had any success running them. But all you really ever need are found on the following links:
* <a href="http://www.fuzzysecurity.com/tutorials/16.html">http://www.fuzzysecurity.com/tutorials/16.html</a>
* <a href="https://toshellandback.com/2015/11/24/ms-priv-esc/">https://toshellandback.com/2015/11/24/ms-priv-esc/</a>

You will need to be able to build basic buffer overflow exploits yourself. The lab exercises are great resources for this. Besides that, I strongly recommend that you grab a Windows 7 machine from <a href="https://developer.microsoft.com/en-us/microsoft-edge/tools/vms/">Microsoft</a> and try <a href="https://github.com/justinsteven/dostackbufferoverflowgood">dostackbufferoverflowgood</a>. Write an exploit yourself and look at the walkthrough afterwards. It will teach your tricks PWK didn't.

If I would go into the lab again now, I would try out <a href="https://github.com/kostrin/Pillage">Pillage</a> to save time on the initial enumeration phase. I haven't tried it much myself, so I can't guarantee its quality, but I think it's worth testing.

The course suggests using KeepNote, that ships with Kali, to document the lab machines, but the project seems dead, and has not been updated since 2012. I would look into <a href="https://www.giuspen.com/cherrytree/">CherryTree</a> or <a href="https://evernote.com">Evernote</a> if I would take the course again.

An important thing to remember is to always revert a machine before you start on it. I have been burned by this many times and wasted so much time. 

I would recommend hanging around on the IRC channel with the other students. You are not allowed to discuss lab machines, but you can discuss the course in general and mingle about other things.

Lastly, read the documentation around the <a href="https://support.offensive-security.com">PWK course and the OSCP exam</a> in great detail. 
