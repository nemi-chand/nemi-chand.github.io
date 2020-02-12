---
layout: post
title:  "Getting Started With SignalR Using ASP.NET Core : Dynamic Hub - Part 4"
author: Nemi Chand
categories: [ ASP.Net Core,SignalR,Angular ]
tags: [aspnetcore,signalr,angular]
#image: assets/images/2016/05/intro-to-docker_title.png
description: "In this article, we'll learn how to use Dynamic Hub in ASPNET Core SignalR. The newly written SignalR allows you to write a dynamic type of T Hub"
featured: false
hidden: false
#rating: 4.5
---

In this article, we'll learn how to use Dynamic Hub in ASPNET Core SignalR. The newly written SignalR allows you to write a dynamic type of T Hub.

## Introduction

The benefit of using dynamic hub is that the method will resolve to the runtime so that you can register any method at runtime and call it.

This article is a part of the series on SignalR using ASPNET Core.

* Overview of New Stack SignalR on ASPNET Core : Introduction - Part 1
* Getting Started With SignalR Using ASP.NET Core : Using Angular 5 - Part 2
* Getting started with SignalR using ASPNET Core : Streaming Data using Angular 5 - Part 3
* Getting started with SignalR using ASPNET Core : Dynamic Hub - Part 4 (this article)
* Getting started with SignalR using ASPNET Core : Azure SignalR Service - Part 5
* Getting started with SignalR using ASPNET Core : Using MessagePack Protocol - Part 6
* Getting Started With SignalR Using ASP.NET Core : Using Xamarin Android - Part 7

This article demonstrates the following tasks:

* Creating Dynamic Hub
* Creating T of Hub
* Consuming the Dynamic and T of Hub by the client
* Demo

The source code is available at [GitHub](https://github.com/nemi-chand/ASPNETCore-SignalR-Angular-TypeScript).

Before reading this article, I would highly recommend you to go through the other articles of the series which are mentioned above.

## Creating Dynamic Hub

This is a base class for SignalR hubs that uses dynamic keyword to represent client invocations. Basically, the idea behind Dynamic Hub is hidden in the dynamic keyword. DynamicHub is inherited from Hub class. This is an abstract class and it is implementing the DynamicHubClients which contains all the dynamic type properties.

From MSDN

`The dynamic type enables the operations in which it occurs to bypass compile-time type checking. Instead, these operations are resolved at runtime. The dynamic type simplifies access to COM APIs such as the Office Automation APIs, and also to dynamic APIs such as IronPython libraries, and to the HTML Document Object Model (DOM).`

A Hub contains the following properties.

* `Clients` : A clients caller abstraction for a hub, where you can push message to all connected clients
* `Context` : A context abstraction for accessing information about the hub caller connection. It contains the connection and claims principle information.
* `Groups` : A manager abstraction for adding and removing connections from groups. Ease of managing clients grouping.
* `Events` : It provide the event notification on the client connected or disconnected so that you can override these events and add your custom login.

## Dynamic Hub class

Gets or sets an object that can be used to invoke methods on the clients connected to this hub.

<script src="https://gist.github.com/nemi-chand/11ffde51f0d913083e75ad3f5db2e945.js"></script>

## DynamicHubClients Class

A class that provides **dynamic** access to connections, including the one that sent the current invocation.

<script src="https://gist.github.com/nemi-chand/fdf7838e10594b3c520b0a98490498e3.js"></script>

## Dynamic Chat Hub Class

In this hub, we are just sending a message to the client so in the case of Hub, we have to call SendAsync by passing the client method name which is listening to the client and message or other data to send. In case of Dynamic Hub, we can call any method which is bypassed by the compiler because it has dynamic keyword. But that method must be listened to on the client side to receive the data like Send method in above class.

```csharp
public class DynamicChatHub : DynamicHub  
{  
    public async Task SendMessage(ChatMessage message)  
    {  
        await Clients.All.Send(message);  
    }  
}
```

I have created a separate service and component for dynamic hub which is consuming Dynamic Hub for communication and showing the messages. You will find the same in the attached project.

## Creating T of Hub

`Hub<T>` is a base class for a strongly typed SignalR hub where T is type of client. The T typed can be used to invoke a method on clients connected. If you want your strongly typed custom methods, this is for you.

In a Hub, we have to tell in which method client is listening, so that while writing the name of the client method, you can avoid typos and other issues.

We are going to create an Interface called IChatClient.

```csharp
public interface IChatClient  
{  
    Task Send(string message);  
}
```

**TChatHub Class**

This class is Inherited from strongly typed IChatClient `Hub<IChatClient>`. In this hub class, you can call IChatClient's methods on the client. This is the beauty of this Stack. This is just for pushing messages to the connected client. You can send a custom object to the client like we did in the streaming article earlier [here]({{site.baseUrl}}/getting-started-with-signalr-using-aspnet-core-streaming-data-using-angular/).

```csharp
public class TChatHub : Hub<IChatClient>  
{  

    public Task Send(string message)  
    {  
        return Clients.All.Send(message);  
    }  

}
```

Finally, we are done with Dynamic and T typed hub in SignalR.

## Summary

In this, we have seen the working demo of dynamic and typed hub with signalR using ASP.NET Core and Angular 5. We have learned the following.

* **Dynamic Hub** Dynamic Hub uses the dynamic keyword, we all know the beauty of dynamic keyword.

* **Typed Hub** A Base class for a strongly typed SignalR hub.

## References

* [SignalR Docs](https://docs.microsoft.com/en-us/aspnet/core/signalr)
* [dynamic keyword](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/dynamic)
* [SignalR Github](https://github.com/aspnet/SignalR)
* [Project Source Code](https://github.com/nemi-chand/ASPNETCore-SignalR-Angular-TypeScript)
