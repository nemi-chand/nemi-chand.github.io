---
layout: post
title:  "Getting Started With SignalR Using ASP.NET Core : Streaming Data Using Angular - Part 3"
author: Nemi Chand
categories: [ ASP.Net Core,SignalR,Angular ]
tags: [aspnetcore,signalr,angular]
#image: assets/images/2016/05/intro-to-docker_title.png
description: "In this article, we'll learn how to stream data to clients with SignalR using ASP.NET Core and Angular 5. We will go through the channel reader/writer which helps in reading/writing into a channel."
featured: false
hidden: false
#rating: 4.5
---


In this article, we'll learn how to stream data to clients with SignalR using ASP.NET Core and Angular 5. We will go through the channel reader/writer which helps in reading/writing into a channel.

## Introduction

The channels play a vital role in streaming data using SignalR. Streaming data is the type of consumer /producer pattern. Let's say the producer is writing data to a channel and the consumer is reading data from the channel. You can choose how to write and read the data from channels so it is memory efficient and high performance. Let us say you are producing data on the channel but somehow a consumer is no longer available to consume the data. In such a case, the memory will increase so you can restrict the channel context to be bound to some limit. Will discuss each of the channels in details below.

The channels were introduced in DOTNET Core 2.1 in [System.Threading.Channels](https://docs.microsoft.com/en-us/dotnet/api/system.threading.channels) namespace under the [CoreFX](https://github.com/dotnet/corefx) project. The channels are better than data flow and pipeline stream. They are more elegant performers in consumer producer queue patterns and with simple and powerful APIs.

This article is a part of the series on SignalR using ASPNET Core.

* Overview of New Stack SignalR on ASPNET Core : Introduction - Part 1
* Getting Started With SignalR Using ASP.NET Core : Using Angular 5 - Part 2
* Getting started with SignalR using ASPNET Core : Streaming Data using Angular 5 - Part 3 (this article)
* Getting started with SignalR using ASPNET Core : Dynamic Hub - Part 4
* Getting started with SignalR using ASPNET Core : Azure SignalR Service - Part 5
* Getting started with SignalR using ASPNET Core : Using MessagePack Protocol - Part 6
* Getting Started With SignalR Using ASP.NET Core : Using Xamarin Android - Part 7

This article demonstrates the following.

* Channel reader/writer
* Bounded/unbounded/Un Buffered Channels
* Creating angular service to read the stream
* Streaming Demo

## Prerequisite

You must have the following software.

* [.NET Core 2.1.0 SDK](https://www.microsoft.com/net/download/dotnet-core/sdk-2.1.300) or later.
* Visual Studio 2017 version 15.7 or later with the [ASP.NET and web development](https://www.visualstudio.com/downloads/) workload.

The source code is available at [GitHub](https://github.com/nemi-chand/ASPNETCore-SignalR-Angular-TypeScript).

## Channels

The System.Threading.Tasks.Channels library provides a set of synchronization data structures for passing the data between producers and consumers. Whereas the existing System.Threading.Tasks.Dataflow library is focused on pipelining and connecting together dataflow "blocks" which encapsulates both storage and processing. System.Threading.Tasks.Channels is focused purely on the storage aspect with data structures used to provide the hand-offs between the participants explicitly coded to use the storage. The library is designed to be used with async/await in C#.

## Core channels

* **Unbound Channel**
Used to create a buffered, unbound channel. The channel may be used concurrently by any number of readers and writers, and is unbounded in size, limited only by available memory.
* **Bound channel**
Used to create a buffered channel that stores at most a specified number of items. The channel may be used concurrently by any number of reads and writers.
* **UnBuffered Channel**
Used to create an unbuffered channel. Such a channel has no internal storage for T items, instead, it has the ability to pairing up writers and readers to rendezvous and directly hand off their data from one to the other. TryWrite operations will only succeed if there's currently an outstanding ReadAsync operation, and conversely, TryRead operations will only succeed if there's currently an outstanding WriteAsync operation.

At the end of this article, you will be able to achieve the below demo.

![Demo Screen]({{site.baseUrl}}/assets/images/2018/06/SignalR-Angular-stream_demo.jpg)

If you haven't read about `How to get started with SignalR using ASPNET Core and Angular 5` [click here]({{site.baseUrl}}/getting-started-with-signalr-using-aspnet-core-and-angular/).
In the previous article , we have learned how to create Angular SPA template and we have also seen the start up configuration and npm package installation. The purpose of this article is to work with streaming data in SignalR.

## SignalR Startup Configuration

You need to configure the SignalR service in `Configure Service` section and Map the hub in configure section. It automatically adds the SignalR reference for you.

Add SignalR service in Configure service method
<script src="https://gist.github.com/nemi-chand/44e50bfb5bbe3418c7c947e572e0493b.js"></script>

Configure Method

```csharp
app.UseSignalR(route =>
{  
        route.MapHub<ChatHub>("/chathub");  
        route.MapHub<StockTickerHub>("/stock");  
});
```

After configuring SignalR in Startup we need to install SignalR Javascript library using NPM Package Manager. Run the below command in Command Prompt.

## NPM Commands

NPM Init command

`npm init -y`

npm init to initialize or set the project npm package. -y | --yes to skip the questionnaire.

Install NPM SignalR Package

`npm install @aspnet/signalr`

You have to run the above commands in Package manager console (Tools --> Nuget Package Manager --> Package Manager Console)

Now, we are going to create a separate hub for data streaming so I'm going to create a stock ticker hub. The Hub is taking care of communication between client and server.

## Creating Stock Hub

We are going to create stock hub class which  inherits hub class. The hub has clients API to talk with clients so you can easily invoke. I've created a separate class for stock ticker which is take care about the list of stock and streaming data.

Stock Ticker Hub

<script src="https://gist.github.com/nemi-chand/a26a5996d17faea4d1e8abb971e29c41.js"></script>

Stock Ticker Class

Which is taking care about loading stocks and writing data to stream

<script src="https://gist.github.com/nemi-chand/ae63681133fdecade87262dd288f0c7a.js"></script>

## Creating Stock Angular Service

Now, I'm going to create stock signalR service for the Angular client to subscribe the stream and call any method. This service has three components.

* Create Connection - take care about creating connection with hub
* Register Server Events - registering on Events like receive message
* Start the connection and subscribe for stream

Stock.signalR.service.ts

<script src="https://gist.github.com/nemi-chand/66072da4069e570719708de07db9a608.js"></script>

After creating the service, we have to register with the provider to access in components. Either register for a global provider in app module or for specific one at self.

## Stock Component

The Component takes care of rendring the stream data.

stock.component.ts Class

<script src="https://gist.github.com/nemi-chand/446d87c9fa716d9cf6050c7713ee4b6a.js"></script>

stock.component.html

<script src="https://gist.github.com/nemi-chand/cb0ecc749b5701b37d931fdd958fd8aa.js"></script>

Finally, we are finished with the streaming demo with SignalR using ASPNET Core and Angular 5.

## Summary

In this article, we have seen the working demo of Streaming data with SignalR using ASP.NET Core and Angular 5. We have learned the following -

* EventEmmiter: to emit the custom event. Read more [here](https://angular.io/api/core/EventEmitter).
* HubConnection and HubConnectionBuilder : to represent SignalR hub connection and a builder for configuring.
* HubConnection instances.
* C# Channels : consumer / Producers based high performant channels.

## References

[SignalR Docs](https://docs.microsoft.com/en-us/aspnet/core/signalr)
[Github Source Code](https://github.com/nemi-chand/ASPNETCore-SignalR-Angular-TypeScript)
