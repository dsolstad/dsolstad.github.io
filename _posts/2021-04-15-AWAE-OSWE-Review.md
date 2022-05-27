---
layout: post
published: true
title: Offensive Security AWAE/OSWE Review
categories: [certifications]
tags: [offsec, awae, web-300, oswe, review]
description: My review of Offensive Security's AWAE/OSWE
fullview: false
---

I started the AWAE course before the <a href="https://www.offensive-security.com/offsec/awae-2020-update/">2020 update</a> and bought the upgrade after my first exam attempt, which really improved the course, in regards to more modules, techniques and the introduction to a lab. Thus, I will focus this review on the post-update course.
  
I want to start this by saying that the course was extremely good and I learned a ton, and I would recommend it to every pentester and bug bounty hunter out there, and even developers. The course takes you through real world zero day exploits in open source software, where they show how to chain small security issues into full unauthenticated RCE exploits to get a shell. However, the most fun part of the course is the lab with three computers for you to have a go at by yourself, like in the PWK labs.  

## Complaints
The biggest complaint I have heard for this course is that the main focus is whitebox testing, even after the upgrade where they added one blackbox module. This is true, but I personally don't find the whitebox focus to be a bad thing. The course will teach you what is going on at the backend and what mistakes there might be, which will give you ammunition when performing a blackbox assessment. Also, I think more pentesters should start asking for the source code when performing real world security assessments, to make the assessment more efficient and thorough.

The second complaint I have heard is that you will need to read a lot of source code and that the course doesn't really go deep into teaching you how to efficiently look for vulnerabilities. I also had this impression after my first exam attempt, which I will talk about next, but overall I think the course teach you enough to pass the exam, but of course it will also depend on your prior skillset.  
  
My only wish is that the course would cover even more advanced techniques, and especially client-side attacks.
  
## Exam
There is no secret that the exam exist of two machines, where you will need to find a way to bypass authentication and then find a way to execute code to get a shell. On my first exam attempt I took down one of the machines after a couple of hours, and then used the remaining time on the other machine without getting any results. After this, I was irritated and felt that the machine was unfairly difficult. When I looked back at the course PDF, I saw that it would have helped me if I did _all_ the extra mile assessments, because I only did the ones I thought were relevant and fun.  
  
On my next exam, I got different machines and I got enough points to pass within the first day, and finished the last objective the morning after. I really enjoyed the exam and felt that they were on the perfect level of difficulty. They were indeed hard, but still doable, and also a lot of fun and realistic. Only the first couple of hours were stressful, because you have no clue what to do, but after a while you start to understand and it gets enjoyable. The next day I spent many hours on writing a huge professional report, which was probably overkill, but I'm one of those weird persons that enjoy writing nice reports.

## Tips
- The exam requires you to write the whole attack chain for each machine in one script that automates the whole process, so make sure you are very comfortable in at least one language.
- Do all the extra mile assessments in the course.
- Try to find all the ways to exploit the machines in the course lab and write a full PoC script for them.
- If you fail your first exam attempt, don't give up. Have another go.
- Try to enjoy the course and the journey.
