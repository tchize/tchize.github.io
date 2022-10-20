---
layout: single
title: DOS attack on Spring's StompBrokerRelayMessageHandler
permalink: /security/findings/spring-stomp-dos/
author_profile: true
toc: true
---

## Summary
 
 |       |    |
 | --------------|------------|
 | Discovery Date | 04-2022 | 
 | Notification date | 11-05-2022 |
 | Status | Full disclosure | 
 | Type | DOS attack | 
 | Disclose date | 27-09-2022 |
 | Public Documentation | [CVE-2022-22971](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2022-22971) <br> [VMWare report](https://tanzu.vmware.com/security/cve-2022-22971)| 
 | Credits | My colleagues at [HMS Networks](https://www.hms.se) and especially Rémy Vermeiren who helped during analysis |
 

## History of discovery

This bug was discovered during a routine investigation of a bug with my colleagues.
After multiple analysis, we came to the conclusion on of our client application was doing something fishy.
We nailed the issue to an error recovery code that was not cleaning everything.

After that, investigations came in on why a rogue client can put down one of our service when
misbehaving.

Once identified, the issue was immediatly raise to my company security manager. A workaround was 
designed and a report made for he Spring teams. Communication with VMWare team was quick, polite and very
professional. Those guys are used to handling security issue and put the proper priority on fixing it.


## Overview

In spring Framework, before 5.2.21 and 5.3.19, If you are exposing STOMP websockets using the Spring StompRelayMessageBroker,
you are vulnerable to a simple DOS attack. By forcing the StompBrokerRelayMessageHandler to open multiple connections to the backend
broker, you create permanent backend connection and sessions which are never reclaimed. In a few seconds,
a browser client with little resources can overload your backend and bring it to a halt. 
The effect of the DOS remain event after the is closed.  

It's important to note that, despite CVE mentionning it requires authentication, it all depends on how you have setup your websocket.
If your Websocket doesn't require authentication (eg it's a public api), exploitation of this issue does not require authentication.


## Are you vulnerable?

Two key elements must be activated in a vulnerable version of Spring Framework to be vulnerable:

* You have enabled websocket support in Spring (eg using @EnableWebSocketMessageBroker)
* You have enabled StompBrokerRelay (eg via MessageBrokerRegistry.enableStompBrokerRelay)

TBD: exploit test

## Technical behind this attack

### Stomp protocol

A WebSocket is basically a raw permanent connection between the browser and the client. Any side can send anything inside it.
Well nearly anything, the browser still has a tell on headers and how to negociate the websocket. And the server
obviously has the right to find the behavior inacceptable and close the connection.
Stomp is a textual protocol that can sit on top of WebSocket. It define verbs and a way to behave in a message
oriented architecture. It's not limited to websocket, server can implement Stomp directly on top TCP connections for example.

STOMP will basically let you subscribe to Queue, Receive and Send messages.

The interesting part here is that Stomp has a notion of Session, and that is what is abusable. 
The STOMP protocol mandates that the client issues a CONNECT or a STOMP command prior to any SUBSCRIBE operation.
To the best of my knowledge, it does not clearly state if multiple CONNECT or STOMP commands are allowed 
inside a single connection or the expected behavior of server in such condition.

A Gray area in protocol, a security risk for implementation.

### Spring Relay implementation

In Spring, the StompBrokerRelay has basically one Job: act as a Relay between the WebSocket and another third party STOMP
server in the backend. This let you add Spring Message interceptors for security, cleaning message, audit logs, 
restrict user queues access, whatever your business rules require to acheive the features you want to expose.

When you issue a CONNECT or STOMP command, the relay will establish a new TCP connection to the backend STOMP.
It will also generate a new session id that will be used to track that connection and associate it with your WebSocket. 
If you close the WebSocket or the Session, the TCP connection will be closed too.

Remember the Gray area about multiple CONNECT in the same WebSocket? Well, one part f the code doesn't care about the
problem and will happily create new TCP connection while the other part assume there can be only one CONNECT and thus
TCP Connection per WebSocket. The result is that, when you issue a second CONNECT in the same WebSocket, the previous
TCP Connection stays open, but is not referenced anywhere. 

### How to exploit it

A git is available with all exploit code at XXXXXX

#### Setup

Assume you have Spring acting as a STOMP relay to a backend RabbitMQ. We are going
to use some javascript to create CONNECT Messages in loop. 

Initial situation is like this. On the browser, we open a single Websocket connection and issue a simple CONNECT
[![Opened webbrowser with developper tab showing a Single websocket connection](/assets/images/stomp-dos/initial-1.png)](/assets/images/stomp-dos/initial-1.png)

As you can see, on RabbitMQ you find 2 Connections: one for the WebSocket, the other one is the control
connection used internally by the Broker on startup.
[![Opened rabbitmq console showing 2 active stomp connections](/assets/images/stomp-dos/initial-2.png)](/assets/images/stomp-dos/initial-2.png)

Close the browser tab and the delegate connection disappear in RabbitMQ
[![Opened rabbitmq console showing a single active stomp connection](/assets/images/stomp-dos/initial-3.png)](/assets/images/stomp-dos/initial-3.png)


#### Flood the system

Using the test javascript, ask the test page to make 3000 CONNECT per websocket 
and reduce the delay to 2ms. Open 4 Websocket by clicking 4 times on test button. 
After about 20 seconds and 9999 CONNECT requests, the server stops responding.
[![Opened webbrowser with developper tab showing multiple websocket connection](/assets/images/stomp-dos/spam-websocket.png)](/assets/images/stomp-dos/spam-websocket.png)

RabbitMQ show 10000 connections
[![Opened rabbitmq console showing 10000 active stomp connections](/assets/images/stomp-dos/rabbitmq-full.png)](/assets/images/stomp-dos/rabbitmq-full.png)

Spring console show pool exhaustion
[![Spring console showing a stacktrace with message Failed to connect: Pool#acquire(Duration)](/assets/images/stomp-dos/spring-stacktrace.png)](/assets/images/stomp-dos/spring-stacktrace.png)

Even after closing tab or browser, the connections between Spring and third party STOMP
service remain. This mean the adverse party attacking this point does not have to keep
the attack running. 


## Workaround

If you can't upgrade your Spring (seriously? Upgrade it), you can work around the issue
by forcing Spring to ignore additional CONNECT on the same WebSocket connection.
You can achieve this by using the Spring's Channel interceptor connected to the InboundChannel.
In this interceptor track session ID's and CONNECT/DISCONNECT to ignore additional CONNECT and 
not pass them to the StompBrokerRelay. 
This should not be considered however as a permanent solution.
A sample code is provided in git at XXXXXX

