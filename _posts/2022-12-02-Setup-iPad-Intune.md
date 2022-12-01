---
layout: post
published: false
title: Setup and Manage iPads in Intune
categories: []
tags: [intune]
description: Fully enroll iPads in Intune to force configurations and push appls 
---

# Setup and Manage iPads in Intune

The objective: Enroll iPads in Intune to be able to fully manage them and to force configurations and automatically push apps. 

This guide is created from reading multiple guides and forum posts, with also some assistance from support. And most of all, trying and failing. And since my knowledge about Apple and Intune was non-existent before this journey, it made things harder. Overall, the core of this guide is very loosely based on the following official guide: https://learn.microsoft.com/en-us/mem/intune/enrollment/apple-configurator-enroll-ios

The whole connection and interplay with Apple Business Manager, Intune and Apple Configurator is complex and confusing, with multiple different settings and various tokens to tie things together. Intune itself can also be perplexing due to it not being a single concrete solution, but an umbrella term of multiple different parts. There is also a problem with calling support, because if you call Microsoft, they don’t know anything about Apple and if you call Apple they don’t know anything about Intune. However, one time I called Apple support and randomly the guy on the phone had worked with Intune before and was of great help. Thank you Sven!

At first I thought enrolling iPads in Intune would be easy, by just installing Intune Company Portal on the iPads and signing in, like many guides and Youtube videos suggested. This was in fact easy, but this method provided extremely limited ability to enforce configurations. By gathering information Online, it came to my understanding that the intended way of enrolling Apple devices in an organization is to define a reseller in Apple Business Manager and buy devices through them where the devices are shipped pre-enrolled in the organization. 

After some more reading I came across a tool called Apple Configurator. The tool is free, but I had to get a personal AppleID and a Macbook to run it, because it's only available through Apple Store. With this tool you can enroll devices by connecting them via USB-C to a Macbook with this software. I first tried the Direct Enrollment method, which didn't require a disk wipe, but this method was also not sufficient enough to enforce the configurations I wanted. It was after doing the Setup Assistant enrollment method I finally got the results that I wanted, but it did require a disk wipe.

Note that I have already enrolled one iPad, so don't be confused when it shows two devices. New device with serial number H is the iPad which gets enrolled in this guide.

# Setup and Enrollment
Follow these steps consecutively and keep in mind that there will be a lot of back-and-forth between the different tools. Also keep in mind that you will be logged out of Apple Business Manager after a very short time idling, so I recommend refreshing the page from time to time.

## 1. Apple Business Manager - Enrolling
Before anything else, enroll your organization in Apple Business Manager. This is completely free and can be done by clicking ‘enroll now’ on business.apple.com. The form needs something called a DUNS number. What this is, how to request it, and how to look it up can be found here: developer.apple.com/support/D-U-N-S/ 
You can also expedite this whole process by calling Apple Support, but should not be needed.

## 2. Intune - MDM Push Certificate
For Intune to be able to manage Apple devices, a MDM push certificate, signed by Apple, needs to be uploaded to Intune. 

![_config.yml]({{ site.baseurl }}/images/intune01.png)

This procedure is straightforward by just following the steps shown when clicking on ‘Apple MDM Push certificate’. Download the signing request file from Intune, which you then upload to Apple to get a certificate back to upload to Intune.

![_config.yml]({{ site.baseurl }}/images/intune02.png)

## 3. Intune - Enrollment Program Token
There are two options under Bulk enrollment methods, and we will only be using Enrollment program tokens for this guide, even though we will be using Apple Configurator, as intuitively that may sound.

![_config.yml]({{ site.baseurl }}/images/intune03.png)

First we need to create a token to establish trust with Intune and Apple Business Manager. Under Enrollment program tokens, click on Add. You will see the following screen.

![_config.yml]({{ site.baseurl }}/images/intune04.png)

Grant permission, download the public key and insert your Apple ID. Keep this window open while we get the Apple token from ABM. 

## 4. Apple Business Manager - Setting up MDM
In ABM, click on your username bottom left and go to Preferences. Here, add a new MDM Server. Call it e.g. Intune. Upload the public key downloaded from Intune in the previous step and download the token from ABM. Go back to Intune and upload the Apple token to finish the token creation process.

![_config.yml]({{ site.baseurl }}/images/intune05.png)

While in ABM, we can set up automatic MDM assignments. Under Preferences > MDM Server Assignment, it’s possible to automatically enroll devices into different MDMs. In this example I have set up so that all iPads will be assigned to Intune. However, I don’t think I have gotten this automatic assignment to work, because it’s needed to manually assign the iPads later.

![_config.yml]({{ site.baseurl }}/images/intune06.png)

## 5. Intune - Enrollment Profile
Under Enrollment Program Tokens, click on the token created earlier. Under Profiles, create a new profile with the following settings.

![_config.yml]({{ site.baseurl }}/images/intune07.png)

We will go back here and assign the profile to the device after we have prepared the device with Apple Configurator.

## 6. Apple Configurator - Prepare
Before setting enrolling devices, make sure you have an AppleID associated with the organization. This can be created in Apple Business Manager.

This application is used to directly enroll Apple devices to the organization via USB-C. It is only available for MacOS and can be downloaded from the Apple Store.

Inside Apple Configurator, click the top left corner and choose Preferences. Add organization and choose ‘Generate a new supervision identity’. Then go to the Servers tab and create a dummy entry. Call it ‘test’ and leave the URL as default.

Connect an Apple device and click on prepare. Make sure to select Manual Configuration and deselect ‘Activate and complete enrollment’.

![_config.yml]({{ site.baseurl }}/images/intune08.png)

The device will now be formatted and enrolled.

An excellent video of these steps can be found here: https://www.youtube.com/watch?v=x05S3pbkrSw

## 7. Apple Business Manager - Verify
Under Preferences, you will see the new device under the Apple Configurator MDM Server. This means we have enrolled the device in Apple Business Manager. However, it is not yet connected to Intune.

![_config.yml]({{ site.baseurl }}/images/intune09.png)

## 8. Apple Business Manager - Change MDM
Find the new device and click on Edit MDM Server, choose Intune.

![_config.yml]({{ site.baseurl }}/images/intune10.png)

Now you will see that the device is moved to Intune, as such:

![_config.yml]({{ site.baseurl }}/images/intune11.png)

## 9. Intune - Assign Profile
In Intune, you will now see the new device under Enrollment program tokens > Intune > Devices. Select it and click on ‘Assign Profile’ and select the profile created earlier.

![_config.yml]({{ site.baseurl }}/images/intune12.png)

Next, under Enrollment program tokens > Intune > Profiles you will find the profile. Click on it and go to Assign devices. When you click on Add Devices, you will find the new Device. Click add and save.

![_config.yml]({{ site.baseurl }}/images/intune13.png)

Why we need to add the profile with both these methods, I don’t know. It was not possible to enroll the device in Intune without the first method, and the device didn’t show as assigned to a profile without the second.

## 10. Apple Configurator - Prepare, again
Now we need to prepare the device again to enroll it to the Intune MDM. The only difference here is to select ‘Activate and complete enrollment’.

![_config.yml]({{ site.baseurl }}/images/intune14.png)

## 11. Intune - Verify
If everything went well, you should now see the device in Intune. Make sure it has a profile assigned and that on ‘Removed from ABM’ says ‘No’. If it doesn’t have a profile assigned, go back to step 9.

![_config.yml]({{ site.baseurl }}/images/intune15.png)
