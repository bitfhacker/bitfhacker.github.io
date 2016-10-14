---
layout: post
title: Configuring network of z/OS v10 running on Hercules (MAC OS)
---

After some days of trying, I was able to ping z/OS running on Hercules! Hurray!

# Here are the steps I took on the dinossaur!

[![Bitfhacker]({{ site.baseurl }}/images/dino.jpg)](https://bitfhacker.github.io/)

First of all, keep in mind that **SYS1.PROCLIB** is the standard *system procedure JCL*, but in ADCD (z/OS v10) the corresponding PROCLIB is **ADCD.Z110.PROCLIB**.

To configure the network, you must have a profile. To keep things simple and straight, browse the file SYS1.PROCLIB(TCPIP) and find the line with //PROFILE. It's something like this:

    //PROFILE  DD DISP=SHR,DSN=SYS1.TCPPARMS(PROF1)

Browse to SYS1.TCPPARMS (ADCD.Z110.TCPPARMS) and create a new file named MYPROF using PROF1 as source - that's where we will configure the network! Then change the above line to:

    //PROFILE  DD DISP=SHR,DSN=SYS1.TCPPARMS(MYPROF)

The rest of configuration, use the [good tips] of Soldier of Fortran. 

There is a typo in your SYS1.TCPPARMS(PROFILE), Soldier of Fortran! You need to break a line between E20 and LINK, like this: 

    DEVICE CTCA1 CTC E20
    LINK CTC1 CTC 1 CTCA1
    HOME
      192.168.1.200 CTC1
    GATEWAY
      192.168.1.1 = CTC1 1492 HOST
    DEFAULTNET 192.168.1.13 CTC1 1492 0
    START CTCA1



> **You can go to jail warning**: Keep in mind that using ADCD without license, can get you in trouble


## Stopping and starting TCPIP

You need to restart the TCPIP stack, so go the z/OS console and issue:

    P TCPIP

and then:

    S TCPIP


In a terminal, test the new settings:

    iMac:IBMZOS110 bitfhacker$ ping -c 3 192.168.1.200
    PING 192.168.1.200 (192.168.1.200): 56 data bytes
    64 bytes from 192.168.1.200: icmp_seq=0 ttl=64 time=0.676 ms
    64 bytes from 192.168.1.200: icmp_seq=1 ttl=64 time=0.651 ms
    64 bytes from 192.168.1.200: icmp_seq=2 ttl=64 time=0.660 ms

    --- 192.168.1.200 ping statistics ---
    3 packets transmitted, 3 packets received, 0.0% packet loss
    round-trip min/avg/max/stddev = 0.651/0.662/0.676/0.010 ms


   [good tips]: <http://mainframed767.tumblr.com/post/114411700159/hercules-on-mac-os-x-yosemite>
