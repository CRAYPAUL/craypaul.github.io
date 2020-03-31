---
title:  "Attacking Wireless Networks: The Road to Achieving True WiFu - Part 1"
date:   2020-03-30
tags: [posts]
excerpt: "Setting the Stage for Attacking Wireless Networks"
---
## Introduction
Being my first post on GitHub, I was nervous about how to approach this topic. I was very eager to dive into how to attack certain networks, how to utilize certain tools, etc.; but before I even started typing, I read a blog post also about how to break into wireless networks. While reading parts of it, I was definitely impressed with how this person described how wireless networks worked. However, once I was reading the portion of this person showing off how he attacked the network, I kept asking myself this: _does this person REALLY understand what is going on during the attacks?_ Sure, it's clear this person was able to use the attacks correctly, but when the post presents terms such as 'fragmentation attack', 'ARP request replay attack', 'fake authentication attack', there is no clear explanation of what these techniques do or why they are essential to the process of successfully attacking a network. Here I will do my best to break down every part of my journey of achieving true WiFu!

__NOTE__ - _This post is only for the sake of learning and should not be used on any network without permission or being in a lab setup. Part 1 is also going to be mainly background information so if you are familiar with the IEEE 802.11 protocols and amendents along with the different encryption and authentication methods, feel free to skip to Part 2._

## Background Information
It is essnential to have a good knowledge-base of networks, their security, and some more before being able to implement attacks on one. While this information can seem daunting, I consider it very important to understand how wireless protocol has evolved over it's lifetime. Let's start with a timeline some of the main wireless protocols:

  - __Legacy 802.11:__  Released in 1997, the original IEEE 802.11 ran on the 2.4GHz band had rates of 1-2 Mbit/s utilizing  the radio frequency modulations [Direct-Sequencing Spread-Spectrum (DSSS)](https://en.wikipedia.org/wiki/Direct-sequence_spread_spectrum) & [Frequency Hopping Spread-Spectrum (FHSS)](https://en.wikipedia.org/wiki/Frequency-hopping_spread_spectrum). 
    - DSSS is a technique used to reduce overall signal interference by making the transmitted signal wider in bandwidth than the information bandwidth. With DSSS, the message bits are modulated by a "random" bit sequence. This resulted in some of the following benefits: resistance to jamming and sharing of a single channel among several users. 
    - FHSS is another transmitting technique that rapidly changed the carrier freqency among certain frequencies resulting in it occupying a large spectral band. Since these frequency changes are in a code only known to the transmitter and reciever, FHSS has the advantage of avoiding interference by hopping to different bands and it is more difficult to intercept since the hopping-pattern is not known.  More can be read about these specific modulations on their respective links.
  
  - __802.11b:__  This IEEE protocol was released in 1999 uses DSSS modulation like its predecessor, but also implented [Complementary Code Keying (CCK)](https://en.wikipedia.org/wiki/Complementary_code_keying) coding, while providing rates of 5-11Mbit/s on the 2.4GHz band. CCK was able to get faster transfer rates from 2Mbit/s, but at the cost of range. This band was divided into 14 overlapping channels, which can be seen in the following figure:
  
![802.11b frequency](http://seth.mattinen.org/images/80211bchannels.gif)

(source: http://seth.mattinen.org/images/80211bchannels.gif)
 
  - __802.11a:__  Also released in 1999, The 802.11a protocol was very expensive to implement with the current hardware and ended up not having sucess, but did introduce a new concept for networks: using the 5GHz band. The 2.4GHz band is very crowded with almost every wireless device using it (bluetooth, wireless phones, even microwave ovens use the 2.4GHz band!), along with several more channel options on the 5GHz band gave this protocol more serious advantages over its predecessors. It also introduced [Orthogonal Frequency-Division Modulation (OFDM)](https://en.wikipedia.org/wiki/Orthogonal_frequency-division_multiplexing) modulation and could give transfer rates of up to 54Mbit/s.
  
  - __802.11g:__  Falling back to the 2.4GHz band, the 802.11g wireless protocol used OFDM modulation, just like 802.11a did, and was able to give the same speeds as well (54Mbit/s). The range was slightly better, and was backwards compatible with the 802.11b protocol; when a 802.11b device connected, it would fall back to the slower rates of 802.11b and could also switched to CCK modulation.
  
  - __802.11n:__  This protocol took about 5 years to develop when the idea was introduced in 2004. Aiming to increase the range and speed of both the 2.4GHz and 5GHz networks, the 2 original proposals could reach speeds up to 74Mbit/s and 300Mbit/s. When it was finally release in 2009, the 802.11n protocol could reach a whopping 600Mbit/s thanks to its unique setup of multiple dedicated antennas (called Multiple-Input Multiple Output). It used the strange feature of radio waves bouncing off walls and doors, coupled with up to 4 anntenas being used simulateonusly to greatly increase transfer rates.
  
  - __802.11ac:__  Even though the newest protocol to be released for wireless is 802.11ax (WiFi 6), this is the protocol most frequently used by home routers and many business still. This is due to WiFi 6 still being a very new protocol. Some of the new features this protocol impelements is the doubling of its MIMO to 8 antennas and it's new modulation technique [(256-QAM modulation)](https://en.wikipedia.org/wiki/Quadrature_amplitude_modulation). 802.11ac is also fully backwards-compatible, so if you are lazy like me and still have a 802.11n dongle in your computer, this is a nice feature.
  
## Wireless Encrpytion
Like I said before, the information shown isn't _vital_ to initiate wireless attacks. But if you are like me, it's neat to see how far wireless technology has come along! Now to the security portion of wireless networks, and the different encrpytion methods: 

  - __Wireless Equivalent Privacy (WEP):__   WEP was created right alongside the legacy 802.11 protocol, which gave similar privacy to wired networks. There are 2 types of WEP authentication methods; Open Authentication and Shared-Key Authentication (SKA). 
    - Open networks allow any device to join the network. To make things worse, the traffic is also not encrpyted, making it very vulnerable. 
    - SKA was implemented to try and mitigate this privacy issue by requiring the station and AP have the same WEP key to authenticate. This 64-bit key originally used a 24-bit initialized vector (IV), which made the actual size of the key 40 bits. This was raised to 128-bit key, but still used the same 240bit IV. It uses RC4 encryption method, which creates a steam of bits that are XOR'd with the cleartext to form its encypted data.
  
  - __WiFi Protected Access (WPA):__   WPA, aka WPA1, was created due to how easy the WEP encryption was cracked, and it uses a new encrpytion protocol: Temporal Key Integrity Protocol (TKIP). It used the same encrpytion method as WEP, but covered some of the issues such as: IV sequencing to mitiage replay attacks, packet key mixing, and more. There isn't a lot to talk about here since it was eventually superceded by WPA2, which was much more secure.
  
  - __WiFi Protected Access 2 (WPA2):__   WPA2 was created at the same time as WPA1, but took much longer to creates its encryption protocol: CBC-MAC (CCMP), which is an AES alorithm. Since 2006, any new device is required to use the WPA2 certification. The authentication process is given a name called 4-way handshake, which I will discuss further on. Both WPA1 and WPA2 have 2 variants: WPA Personal & WPA Enterprise. 
    - WPA Personal uses a passphrase shared by everyone on the network, called [Pre-Shared Key Authencation (WPA-PSK)](https://en.wikipedia.org/wiki/Pre-shared_key), which is more than likely the authentication method your home network uses. 
    - WPA Enterprise uses 802.1x with a radius server for an authentication authority.
    
  - __WiFi Protection Access 3 (WPA3):__ WPA3 is a relatively new encryption method that has been released. Thanks to the AES alorgithm, the WPA2 key was very difficult to crack. This made the weakness the PSK passphrase that I explained previously. Not only does WPA3 aim to mitigate this weakness, and takes a step up in its Enterprise mode with a 192-bit cryptographic strength standard (AES-256 and SHA-384). Personal Mode still uses the AES-128 standard for encrpytion however. Back to the passphrase, WPA3 replaces the PSK key with a new Simultaneous Authentication of Equals (SAE) authentication method. 
    - The WiFi alliance has stated the new autentication method, SAE, is [resistant to passive and active attacks, as well as dictionary attacks.](https://ieeexplore.ieee.org/document/4622764)
  
  
To break this information down a bit, WEP was replaced because of how fast the key could be cracked, in come cases it could be cracked in under a minute. WPA/WPA2 encryption is much more powerful thanks to its AES algorithm, which means on Pre-Shared Key Authentication, realistically our only option is going to be brute forcing the passphrase. Unfortunately this level of protection gives people a false sense of security and they either use the default passphrase or a weak one. WPA3 can aim to mitigate these passphrase weakness, but is still relatively new. I will not be demonstrating any attacks on WPA3 or on WPA2 with Enterprise mode. 
