---
layout: post
title:  "Getting Started With SignalR Using ASP.NET Core : Using Message Pack - Part 6"
author: Nemi Chand
categories: [ ASP.Net Core,SignalR,Angular,Message Pack ]
tags: [aspnetcore,aspnetcore-mvc,signalr,angular,messagepack]
#image: assets/images/2016/05/intro-to-docker_title.png
description: "In this article, we'll learn how to use message pack protocol with SignalR using ASP.NET Core and Angular. The message pack is used for binary data transmission over the protocol and binary data is lightweight in comparison to text data."
featured: false
hidden: false
#rating: 4.5
---

In this article, we'll learn how to use message pack protocol with SignalR using ASP.NET Core and Angular. The message pack is used for binary data transmission over the protocol and binary data is lightweight in comparison to text data.

## Introduction

`MessagePack is an efficient binary serialization format. It allows you to exchange data among multiple languages like JSON. But it is faster and smaller. Small integers are encoded into a single byte, and typical short strings require only one extra byte in addition to the strings themselves.`

The binary data transmission has a significant performance benefit over the protocol. It is like a JSON but small and fast.

In this article, we'll see how to enable message pack on both Server and client side. It is mandatory to enable on both sides to get it to work because while negotiating between the client and server this feature must be enabled.

This article is a part of the series on SignalR using ASPNET Core.

* Overview of New Stack SignalR on ASPNET Core : Introduction - Part 1
* Getting Started With SignalR Using ASP.NET Core : Using Angular 5 - Part 2
* Getting started with SignalR using ASPNET Core : Streaming Data using Angular 5 - Part 3
* Getting started with SignalR using ASPNET Core : Dynamic Hub - Part 4
* Getting started with SignalR using ASPNET Core : Azure SignalR Service - Part 5
* Getting started with SignalR using ASPNET Core : Using MessagePack Protocol - Part 6  (this article)
* Getting Started With SignalR Using ASP.NET Core : Using Xamarin Android - Part 7

This article demonstrates the following things

* Why Message Pack?
* Configuring message pack on the server
* Configuring message pack on Typescript client
* Configuring message pack on .NET Client
* Demo

The source code is available at GitHub [here](https://github.com/nemi-chand/ASPNETCore-SignalR-Angular-TypeScript) and [here](https://github.com/nemi-chand/ASPNETCore-SignalR-AzureService).

## Why Message Pack

* It is small in size, compact and efficient.
* What you can do with JSON, you can do with Message Pack.
* Creates an application of a specific type.
* It is supported by over 50 programming languages.

![Message Pack Structure]({{site.baseUrl}}/assets/images/2018/08/aspnet-core-angular-msgpack.png)

(image source https://msgpack.org)

In the above picture, you can see the difference between JSON and MessagePack. It is smaller than JSON so it has better performance over the transmission.

You can read more about MessagePack at the [official website](https://msgpack.org/).

## Configure Message pack on Server

You are ready to use MessagePack (binary data) with SignalR just by changing one line of code on the server side. We are going to use SignalR Protocol MessagePack client in order to use server side. We need to install MessagePack signalr client sdk `Microsoft.AspNetCore.SignalR.Protocols.MessagePack`.

## CLI Command

`dotnet add package Microsoft.AspNetCore.SignalR.Protocols.MessagePack --version 1.0.0`

## Nuget Package

`Install-Package Microsoft.AspNetCore.SignalR.Protocols.MessagePack`

After installing this SDK, we have to add SDK in start up services to use MessagePack. Add AddMessagePackProtocol method after AddSignalR method in configure services in startup class.

```csharp
services.AddSignalR().AddMessagePackProtocol();
```

AddMessagePackProtocol allows you to configure the options to add formatter resolvers so you can create your custom IFormatterResolver in order to add custom resolver. There are plenty of prebuilt formatter resolvers available like standard resolver and unself binary resolver.

```csharp
services.AddSignalR().AddMessagePackProtocol(configure =>  
{  
    configure.FormatterResolvers = new List<IFormatterResolver>()  
    {  
        MessagePack.Resolvers.UnsafeBinaryResolver.Instance  
    };  
});
```

## Configure message pack on Client

We have successfully added MessagePack to the server side, now we have to add on the client side as well. In this article, we will see two types of client implementations.

* TypeScript/Angular Client
* .Net Client

## Typescript/Angular Client

Need to install NPM Package for a messagepack protocol.

NPM Command

`npm install @aspnet/signalr-protocol-msgpack`

After installing this pack, we are going to use MessagePackHubProtocol class. HubConnectionBuilder class has a method called `withHubProtocol`. It takes IHubProtocol implementation so we are passing MessagePackHubProtocol instance to it. HubConnectionBuilder will configure it to use the Message Pack protocol.

```typescript
import { MessagePackHubProtocol } from '@aspnet/signalr-protocol-msgpack';  
  
const hubConnection = new HubConnectionBuilder()  
      .withUrl('/chat')  
      .withHubProtocol(new MessagePackHubProtocol())  
      .build();
```

We have successfully added Messagepack protocol to TypeScript client. Now it's sending and receiving the Binary data over the protocol.

## .NET Client

This is .NET standard 2.0 client so the purpose of this client to consume SIgnalR hub which is must be built on or above 2.1 ASP.NET Core SignalR. To enable Message Pack on .NET Client , install the NuGet Pack `Microsoft.AspNetCore.SignalR.Protocols.MessagePack`.

CLI Command

`dotnet add package Microsoft.AspNetCore.SignalR.Protocols.MessagePack --version 1.0.0`

Nuget Package

`Install-Package Microsoft.AspNetCore.SignalR.Protocols.MessagePack`

Add method AddMessagePackProtocol to HubConnectionBuilder class while creating hub connection.

```typescript
var hubConnection = new HubConnectionBuilder()  
                        .WithUrl("/chat")  
                        .AddMessagePackProtocol()  
                        .Build();
```

## Demo

The hub connection is started with messagepack protocol with binary frame in the above image.

![Message Pack Demo]({{site.baseUrl}}/assets/images/2018/08/aspnet-core-angular-msgpack_demo.png)

## Summary

In this article, we have seen MessagePack protocol implementation with SignalR using ASP.NET Core and Angular 5. We have learned the following things:

* Why message pack gives better performance.
* Enabling MessagePack on the server side.
* Enabling MessagePack on multiple clients (Typescript/Angular and .NET client).

## References

* [SignalR Docs](https://docs.microsoft.com/en-us/aspnet/core/signalr)
* [SignalR Github](https://github.com/aspnet/aspnetcore/tree/master/src/SignalR)
* [Angular Version Code](https://github.com/nemi-chand/ASPNETCore-SignalR-Angular-TypeScript)
* [Azure SignalR service Code](https://github.com/nemi-chand/ASPNETCore-SignalR-AzureService)
