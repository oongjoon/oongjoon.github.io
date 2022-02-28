---
title : "Network layer device :Rotuer"

excerpt: "Network System device at Network Layer"

categories:
  - Network
tags:
  - [SoftwareEngineering , Network ,Network Device]
# classes : wide
toc: true
toc_sticky: true
---
When connecting computers through a single network, that is, when making a LAN, a switch or hub is used. But, what do you do when you connect a network?

# What is a router?

A router is a device that determines how to transfer data between networks. A switch is used in layer 2, a hub is used in layer 1, but a router is used in layer 3.

The switch examines the destination mac address of an ethernet frame to determine where to send the data, the router examines the ip address.

Inside the router is a table, which contains how to route traffic to different networks around the world.



# Core Router vs home/small router

Routers used by home or small businesses do not include routing tables. The purpose of this router is to receive traffic from the lan and forward it along the isp. Therefore, there is no detailed routing table.

As traffic comes to the ISP, it arrives at a more complex router. The Core Router is responsible for how traffic is sent on the Internet or what data it receives from the Internet.

The difference between the core router and the home/small router is that the core router sends more data and makes more complex decisions about where to send it.

# BGTP

The router uses a protocol called bgtp, which learns the optimal path to send traffic.

