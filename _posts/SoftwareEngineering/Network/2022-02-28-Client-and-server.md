---
title : "Transport Layer Device : Client and Server"

excerpt: "Network System device at Transport Layer"

categories:
  - Network
tags:
  - [SoftwareEngineering , Network ,Network Device]
# classes : wide
toc: true
toc_sticky: true
---

There is the concept of Client and Server which are frequently used in the Transport Layer. This is called a networking device in Google IT support. The device is correct, but many clients and servers can run simultaneously in this device. The protocol used in the transport layer determines which client and which server it is, but this time, we are going to learn about this client and server.

# Client and Server



![image](https://user-images.githubusercontent.com/50165842/155990435-1780af59-bfa4-4502-88f3-3550110dc47c.png)



Simply put, the client is the role of requesting and receiving data, and the server is the role of sending the requested data back.

When each device is called a node, the node can be either a client or a server.

Briefly, a node can create a socket and open several ports, and each port can act as a server or a client. (Details will be explained later when dealing with the transport layer.)

![image](https://user-images.githubusercontent.com/50165842/155991286-bb5e84fe-a8d2-45e1-990b-65af46e370f8.png)

For example, a mail server may become a server by opening a port that responds to a client, or it may become a client making a request to a DNS server.



The expressions Server, Client may represent roles or may represent main roles.
