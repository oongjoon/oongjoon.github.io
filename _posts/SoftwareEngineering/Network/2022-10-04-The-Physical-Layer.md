---
title : "The Phyiscal Layer"

excerpt: "TCP 5 layer model-phyiscal"

categories:
  - Network
tags:
  - [SoftwareEngineering , Network ,TCPIP]
# classes : wide
toc: true
toc_sticky: true
---
In a previous post, I explained the TCP-IP Model. The TCP-IP Model consists of 5 layers. Today, I will explain the Physical Layer Model, which is the lowest layer.

# Physical Layer?

![image](https://user-images.githubusercontent.com/50165842/192401951-1c3511fd-0043-4d22-84a4-ccca355c97fc.png)

The Physical Layer sends information called bits from multiple devices to other networks. Bits are representations of data that computers can understand. It has a value of 0,1.



The copper network cable goes through a process called modulation to create and send bits. Modulation means varying the voltage of the electric charge moving along the cable.
![image](https://user-images.githubusercontent.com/50165842/192905671-59927d83-99a7-4192-a317-c08583b6b058.png)


In Computer Networking, this modulation is called line coding. In modern networking, the word 10 gigabit network comes up a lot, which means that each cable processes 10 billion bits (0,1) in 1 second.


# Twisted Pair Cabling

![image](https://user-images.githubusercontent.com/50165842/192906277-22635c2f-4c75-4f5f-9ba2-06b0fc052f38.png)

A twisted pair means that the copper wire is twisted. It is called twisted pair cable because it consists of a twisted cable pair. Twisting the cable like this reduces electromagnetic interference, that is, crosstalk.
For example, cat6 cable uses 8 wires, i.e. 4 pairs. It determines how many pairs to use according to the transmission technology.
# Duplexing

In modern networking, cables use duplex communication or simplex communication.
![image](https://user-images.githubusercontent.com/50165842/192907374-7139d5a2-fa5c-4ab9-9c73-eb429b4f7593.png)

simplex means a communication method that transmits information in only one direction. As shown in the figure, you can see that one device receives or transmits unilaterally. Typical examples are radio or TV. A broadcasting station transmits a radio wave, and the listener only receives the radio wave.
![image](https://user-images.githubusercontent.com/50165842/193703834-b4a8bc16-a6b1-45cb-93ed-7761ef8dc4c8.png)

Duplex is a method that allows the client and server to communicate in both directions. If so, how is this possible?
A cable consists of several pairs, and each pair is reserved in advance in only one direction. If two-way communication is possible at exactly the same time, it is called full duplex.

![image](https://user-images.githubusercontent.com/50165842/192907219-b76b3246-9525-473f-a3f5-52338320cef1.png)

If there is a problem with the network, the network reports that the link is degraded and does half-duplex communication.
At this time, the network can communicate in both directions, but only in one direction at a time.

# Network Ports and panel

Twisted Pair network cables have a plug at the end.

![image](https://user-images.githubusercontent.com/50165842/193705135-31a0b36a-c130-4664-861c-a2d8071ecb6e.png)

The most popular plug is the RJ45 plug (Registered Jack 45).
![image](https://user-images.githubusercontent.com/50165842/193705565-1e8ffa88-ec48-40a4-9dbb-c64140880ae7.png)
![image](https://user-images.githubusercontent.com/50165842/193705655-9bafd997-c574-4eb7-ba11-c963cbcda4dc.png)
As mentioned before, Switch has multiple ports, while Server or Desktop has 1 or 2 ports.
![image](https://user-images.githubusercontent.com/50165842/193705381-c4cb44c5-1763-43dd-80cb-555e3f94757c.png)


All network ports have two ports: link led and activity led. LinkLed turns on when the two devices are well connected. activityLed blinks when data is being actively transmitted.
These days, computer networks are so fast, Activity LEDs don't really mean anything other than whether there's traffic or not.

![image](https://user-images.githubusercontent.com/50165842/193705887-78fbb6cf-3513-46a8-a44d-f4fd93120f73.png)
Occasionally, there is a network port attached under the desk. These ports reach the patch-panel through the network cable, over the wall. There are many ports in the patch-panel, but it is just a container of ports. The cables from this patch panel are routed to a switch, router or computer.

# References

[Google-IT-support][Google-It-support]

[Google-It-support]: "https://www.coursera.org/learn/computer-networking/lecture/Nihjd/moving-bits-across-the-wire" 

[Twisted-pairwiki][Twisted-pairwiki]

[Twisted-pairwiki]: "https://en.wikipedia.org/wiki/Twisted_pair"


[Simplex][Simplex]

[Simplex]: "https://en.wikipedia.org/wiki/Simplex_communication"

[Duplex][Duplex]

[Duplex]: "https://en.wikipedia.org/wiki/Duplex_(telecommunications)"