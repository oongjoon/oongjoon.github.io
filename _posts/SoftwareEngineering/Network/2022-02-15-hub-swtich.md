---
title : "Hub and Switch"

excerpt: "Network System device"

categories:
  - Network
tags:
  - [SoftwareEngineering , Network ,Network Device]
# classes : wide
toc: true
toc_sticky: true
---
![image](https://user-images.githubusercontent.com/50165842/154070819-176a5078-c66d-4c77-ac30-cae03f26169f.png)

A point-to-point connection is possible with a cable. However, there is a limit to using only Point-to-Point when connecting more users. It is Hub and Switch that make this possible.



# Hub

![image](https://user-images.githubusercontent.com/50165842/154070931-6bd1b5d0-68ba-4d49-8221-85c69d41c304.png)



Hub is a physical layer device that connects multiple nodes.

![image](https://user-images.githubusercontent.com/50165842/154071101-99b05343-532c-40f3-af59-a2a1081b2fe3.png)

However, when Hub transmits data, all nodes receive this data. Therefore, if multiple nodes transmit data at once, this data is corrupted. This is called a collision domain.

When multiple systems try to transmit data, electric pulses affect the cable. When a collision domain occurs, data must be retransmitted, so you have to wait while retransmission.

This is why Hub is not used.



# Switch

![image](https://user-images.githubusercontent.com/50165842/154071211-836c7c2f-6542-46ed-ab2a-fa260a2088d7.png)

A switch is a device of the DataLink Layer.

The switch can check the Ethernet protocol. Thus, it is possible to know which source this data came from and which destination it is going to. Therefore, it is transmitted only to the destination, not to all systems.



As the collision domain is reduced, the retransmission rate is reduced.

# Reference

[Hubs and Switches  Coursera](https://www.coursera.org/learn/computer-networking/lecture/8rMWU/hubs-and-switches)
