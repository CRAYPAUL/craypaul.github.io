---
title:  "Auditing Wireless Networks - Part 2"
date:   2020-04-01
tags: [posts]
excerpt: "Breaking and Entering into Wireless Networks."
---
## Introduction
Before I start showing commands and screenshots, I want to start off with giving a little bit of information on the lab setup and the hardware I am using during these attacks. Instead of attacking my own network and potentially cause problems or affecting a device connected to it, I opted to quickly set up my own lab. You will also see in other screenshots that I am using the Kali Linux 2020.1 OS. Here is a list of those items:
  - __Router:__ Netgear N150 Simple Sharing Wireless Router (WNR1000)
  
    ![Router Box](http://momsownwords.com/wp-content/uploads/2013/09/netgear.jpg)
    
    _(source:http://momsownwords.com/wp-content/uploads/2013/09/netgear.jpg)_
    
    
  - __Wireless Card:__ Kali WiFi Atheros AR9271 Long-Range USB Adapter
    
    ![Kali Wireless Card](https://i.pinimg.com/originals/42/d5/bb/42d5bbf5b23d4ab1fe43aecae9c750d8.jpg)
    
    _(source: Aaron via Pintrest; Note: this is the same chipset as my actual card, the main difference is my card has the Kali Linux Dragon emblem. Thanks Connor!!)_
    
## Getting Started
So there are a few things that are necessary to understand before we can just dive right into attacking our network. __iwconfig__ is a very nice command line tool we can use to configure a wirless network interface. A quick note - virtually all of the command line tools I use come out of the __airgeddon__ suite of tools. Feel free to check it out [here](https://en.kali.tools/?p=249). Let's run _iwconfig_ to show us our interfaces:

![iwconfig cmd]

Here we can see that our wireless card's interface, wlan0, is up and running. Some other information that we can see is we are in Managed Mode and we are not associated with any Access Point (AP). A quick explantation of these 2 points:
  - __Mode: Managed__ - this mode is used to connect to networks.
  - __Access Point: Not-Associated__ - this means we cannot send traffic through the AP to other devices on the network. 

Not being associated with an AP is not an issue, but we need to configure the wireless card to be in a mode where we can passively monitor every packet on any frequency - this mode is called Monitor Mode. But even before we change modes, we need to confirm that any running processes will not interfere with us changing modes. 
