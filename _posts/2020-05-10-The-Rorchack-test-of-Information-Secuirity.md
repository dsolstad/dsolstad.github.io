---
layout: post
title: The Rorschach Test of Information Security
published: true
categories: [information-security]
description: Analysis of an image that popped up in my LinkedIn feed
comments: true
fullview: false
---

I follow a lot of information security professionals on LinkedIn, and some weeks ago I saw a post with an image in my feed. I will give you a moment take a look yourself before reading on. 

![_config.yml]({{ site.baseurl }}/images/lock.png)

The image followed a text saying that unlocking one lock will unlock the whole thing, and then drawing a parallel to cyber security, where an attacker would only need to compromise one system to gain access to an organization. This is indeed correct for the picture, but let's try to look at this with a non-security tunnel vision mindset.

First off, let's try to understand what these locks are protecting. It seems to be some sort of outdoor gate to reach a restricted area. The locks are of different kind and are numbered, suggesting there is one unique key for each lock. By example we can imagine that it's a gate to access a private zone with multiple holiday houses, and the gate is to access it by car, with each household having their own unique key. 

## Decentralized system
We can simplify the above image by replacing it with the following:

![_config.yml]({{ site.baseurl }}/images/chain1.png)

The same principle applies - if you break one lock, you break the whole chain, thus opening the gate. If a new person would need access to this restricted area, that person would need to get in touch with one of the key holders to open their lock, and the new person would add their lock to the chain. This new person could be an owner of a new house built in the restricted zone, or even the fire department. If anyone would lose their key, the person would buy a new lock and get in touch with anyone of the key holders to unlock the chain and to add the new lock. All this at a low cost and without a central key administration.
  
Another key point here is that if anyone forgets to lock the gate, it becomes evidently who was responsible.

## Centralized system
Now we can look at the opposite principle, with one lock and multiple cloned keys, one for each household, like the image below illustrates.

![_config.yml]({{ site.baseurl }}/images/chain2.png)

If a new house owner would need access, they would need to apply to get a new cloned key. This could take some time and they might need to go far away to get the new key. If someone loses their key, the administration would need to change the lock, clone new keys and locate households to distribute the new keys. Very cost and time inefficient.

You can argue that in this example the central administration could already have cloned keys ready for new households, and if someone loses their key, the administration wouldn't really care that much and just give them a new key. However, this might not be the case in other examples, where the lock is protecting something of more value.

The bottom line is: Avoid tunnel vision - Try to think from multiple perspectives, be more agile, and think about that it could be a reason for why things are like they are. 
