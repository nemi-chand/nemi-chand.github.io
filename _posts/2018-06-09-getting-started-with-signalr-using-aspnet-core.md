---
layout: post
title:  "Getting Started With SignalR Using ASP.NET Core : Introduction"
author: Nemi Chand
categories: [ ASP.Net Core,SignalR ]
tags: [aspnetcore,signalr]
#image: assets/images/2016/05/intro-to-docker_title.png
description: "In this article, we learn the best practice to register the generic interface in new ASP.NET Core framework."
featured: false
hidden: false
#rating: 4.5
---

In this article, we'll kick things off with SignalR using Angular and ASPNET Core. This is a new version of SignalR which is written in ASPNET Core and released in ASPNET Core version 2.1

## Introduction

This is part of a series of posts on SignalR using ASPNET Core with Angular and Android as well.

This article is part of series of post on SignalR using ASPNET Core.

* Overview of New stack SIgnalR on ASPNET Core : Part 1 (this article)
* Getting Started With SignalR Using ASP.NET Core And Angular 5 : Part 2 [here]({{site.baseUrl}}/getting-started-with-signalr-using-aspnet-core-and-angular/)
* Working with dynamic hubs here
* Working with Azure SignalR service here
* Working with xamarin
* Streaming data server to client here

## This article demonstrates the following things

* What is SignalR?
* Features of SignalR
* SignalR Hubs
* Transport Protocols

## What is SignalR

SignalR is framework/Library which enables real-time communication between the client and the server. Earlier the server was unable to communicate with the client so SignalR solves this problem. SignalR uses transport protocols while communicating like Web socket, Server-Sent Event, and Long pooling. It has a fallback mechanism.

## Features

* SignalR is no longer dependent on Jquery
* Ease of group connection
* No more ping-pong mechanism
* Broadcasting messages to all clients once
* Broadcasting message to group clients once.
* Scalable
* Redis support
* It supports text and binary protocol messages ( e.g. JSON and Message Pack)
* Typed hub

## Hub

The SignalR hub is used to communicate between client and server. The server can call to a specific client or transmit the data at any time. Currently, SignalR allows transmitting text and binary data using JSON and Message pack.

According to the website: "MessagePack is an efficient binary serialization format. It lets you exchange data among multiple languages like JSON. But it's faster and smaller. Small integers are encoded into a single byte, and typical short strings require only one extra byte in addition to the strings themselves."

SignalR allows you to create and map multiple Hubs in a single application so that the client can communicate to multiple hubs by just creating separate hub connections. Let's say your application has a chat room and stock ticker functionality; in this case you can create a separate hub for a chat and stock ticker. It allows you to manage separate lifecycle for both of the hubs. A hub takes care of clients, connection management like onConnected, OnDisConnnected, dispose and group management.

![SignalR Hub]({{site.baseUrl}}/assets/images/2018/06/signalR_hub.png)

You have to inherit hub class from Hub to get working.

```csharp
public class ChatHub : Hub  
    {  
        public async Task SendMessage(ChatMessage message)  
        {  
            await Clients.All.SendAsync("ReceiveMessage", message);  
        }  
    }
```

## Transport

SignalR uses the optimal transport protocol for communication between client and server. It uses the three transport protocols; e.g., Web socket, server-sent events and long pooling.

It automatically detects the optimal transport based on the client and server features. It has a fallback mechanism to other protocols. SignalR first looks for a Web socket connection, if it does not establish one then it moves to Server-sent events (HTML 5), if not then it moves to long pooling.

![SignalR Transport Performance]({{site.baseUrl}}/assets/images/2018/06/signalR_Transport_performance.png)

You have seen the above performance image. The web socket wins the performance race. This is the hot choice communication over TCP connection. That's why SignalR first look for web socket if it is available then establish the connection otherwise moves to other protocols as a fallback mechanism.

## Summary

In this article, we have learned about SignalR and its applications. SignalR Hub plays a vital role, it takes care of connection management and group management. We have seen the performance of transport protocols, and Web socket wins the race.

## References

* [SignalR Docs](https://docs.microsoft.com/en-us/aspnet/core/signalr)
* [SignalR Github](https://github.com/aspnet/AspNetCore/tree/master/src/SignalR)
