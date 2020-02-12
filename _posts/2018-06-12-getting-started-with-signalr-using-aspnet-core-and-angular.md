---
layout: post
title:  "Getting Started With SignalR Using ASP.NET Core And Angular : Part 2"
author: Nemi Chand
categories: [ ASP.Net Core,SignalR,Angular ]
tags: [aspnetcore,signalr,angular]
#image: assets/images/2016/05/intro-to-docker_title.png
description: "This article is part of a series called `Getting started with SignalR using ASP.NET Core`. In this article, we'll learn how to get started step by step with SignalR using angular 5. At the time of writing article .NET Core 2.1 has been released."
featured: false
hidden: false
#rating: 4.5
---


This article is part of a series called `Getting started with SignalR using ASP.NET Core`. In this article, we'll learn how to get started step by step with SignalR using angular 5. At the time of writing article .NET Core 2.1 has been released.

## Introduction

This is part of series of post on SignalR using Angular5 and ASP.NET Core.

* Overview of New stack SIgnalR on ASPNET Core [here]({{site.baseUrl}}/getting-started-with-signalr-using-aspnet-core) : Part 1.
* Working with streaming data here.
* Working with Azure service here.
* Working with Xamarin here.
* Working with dynamic hubs here.

## This article demonstrates the following tasks

* Creating Angular SignalR service.
* Creating SignalR Hub.
* Creating ChatRoom component.

## Prerequisite

You must have the following software.

* [.NET Core 2.1.0 SDK](https://www.microsoft.com/net/download/dotnet-core/sdk-2.1.300) or later.
* Visual Studio 2017 version 15.7 or later with the [ASP.NET and web development](https://www.visualstudio.com/downloads/) workload.

The source code is available at [GitHub](https://github.com/nemi-chand/ASPNETCore-SignalR-Angular-TypeScript).

At the end of this article, you will be able to use SignalR in your project.

## Why Angular

This is a hot and trending client-side JavaScript technology. It has many advantages over the client side. Everyone wants the combination of Angular and ASPNET Core so I've decided to do that.

If you want to upgrade your Angular application to the latest version. [Upgrade guide](https://update.angular.io/)

Let's get started to kick off the demo. We are going to create new Angular SPA(Single Page Application) template project.

Create New Project : File --> New --> Project

![Create New Project]({{site.baseUrl}}/assets/images/2018/06/SignalR-Angular_Choose-Project.jpg)

Choose the template: You have to choose Angular SPA template.

.NETCore --> ASP.NET Core 2.1 -- > Angular

![Choose Template]({{site.baseUrl}}/assets/images/2018/06/SignalR-Angular_Choose-Template.jpg)

You have created an Angular template project successfully. 

## SignalR Startup Configuration

You need to configure SignalR service in Configure Service Section and Map the hub in configure section. It automatically adds the SignalR reference for you.

Add SignalR service in Configure service method below.

<script src="https://gist.github.com/nemi-chand/1638c9aaaccc3dc2e8f9b514d65f14f1.js"></script>

Map the hub in Http pipeline in Configure method

```csharp
app.UseSignalR(route =>
    {  
        route.MapHub<ChatHub>("/chathub");  
    });
```

After Configuring SignalR in Startup. We need to install SignalR Javascript library using NPM Package Manager. Run the below command in Command Prompt

## NPM Commands

NPM Init command

`npm init -y`

npm init to initialize or set the project npm package. -y | --yes to skip the questionnaire.

Install NPM SignalR Package

`npm install @aspnet/signalr`

You have to run the above commands in Package manager console (Tools --> NuGet Package Manager --> Package Manager Console)

Creating SignalR Hub: to establish communication between client and server. You can call any client method via Hub and vice versa. I'm going to create a chathub class in a folder called 'Hubs'. Add reference of SignalR namespace 'Microsoft.AspNetCore.SignalR' in ChatHub class.

```csharp
public class ChatHub : Hub  
{  
    public async Task SendMessage(ChatMessage message)  
    {  
        await Clients.All.SendAsync("ReceiveMessage", message);  
    }  
}
```

Creating SignalR Angular Service: This service will take care of creating connection , starting connection and register ingthe server events to receive the messages. Create folder called 'Services'.

ClientApp --> src --> app --> Services

Create a service typescript class file named 'signalR.service.ts'. I'm using two model classes which are shown below.

<script src="https://gist.github.com/nemi-chand/fd38cf1488f188691c0c81c6ebe6f25f.js"></script>

I'd always prefer to create models/poco so let's create ChatMessage and Tab models . Create a new folder called 'Models'.

chatmessage.model.ts class : to contains the detail of chat message like message, room and user info.

<script src="https://gist.github.com/nemi-chand/41408fd8392f93d3cd1a2ea0a3ed88c6.js"></script>

tab.model.ts class to contains the detail of each tabs like heading, title and messages history

<script src="https://gist.github.com/nemi-chand/85a11623ca30806e06f4c1aa2dbdd371.js"></script>

After creating angular signalR service, now I have to add  providers in app module class. Just import the signalr service and supply to provider so that I can access it in the entire app.

Now I'm going to consume SignalR service into the home component which takes care of the UI like sent message and receive messages. By default, it's creating two chat rooms, like Lobby and SignalR room, but you can create more rooms and you can fetch the room details from the server as well. It's up to you, this is for demo purposes.

<script src="https://gist.github.com/nemi-chand/327d14d9d9d54209cba544d844514113.js"></script>

home.component.html

<script src="https://gist.github.com/nemi-chand/844a907045beb628af1c5ef6e12be186.js"></script>

## Demo Screen

The demo screens demonstrating Group chat examples. You can subscribe to multiple group and get the notifications.

Demo Screen 1

![Demo Screen 1]({{site.baseUrl}}/assets/images/2018/06/SignalR-Angular_Demo1.jpg)

Demo Screen 2

![Demo Screen 2]({{site.baseUrl}}/assets/images/2018/06/SignalR-Angular_Demo2.jpg)

Finally, we have completed the working Chat room demo with SignalR using ASPNET Core and Angular 5. Source code of this project is available @ Github, fork and you are free to play with it.

The source code @ [Github](https://github.com/nemi-chand/ASPNETCore-SignalR-Angular-TypeScript)

## Summary

In this we have seen the working demo of a new signalR stack on ASPNET Core using Angular 5 SPA template, also we have seen Angular signalR service to communicate with the hub. This is for demonstration purposes only, you may modify as per your requirements. I hope you have enjoyed this article and don't forgot to comment.

## References

* [SignalR](https://docs.microsoft.com/en-us/aspnet/core/signalr)
* [Github Source Code](https://github.com/nemi-chand/ASPNETCore-SignalR-Angular-TypeScript)
* [Getting Started With SignalR Using ASP.NET Core]({{site.baseUrl}}/getting-started-with-signalr-using-aspnet-core)
