---
layout: post
title:  "Getting started with SignalR using ASPNET Core : Azure SignalR Service - Part 5"
author: Nemi Chand
categories: [ ASP.Net Core,SignalR,Angular,Azure ]
tags: [aspnetcore,aspnetcore-mvc,signalr,angular,azure,azure-signalr,azure-signalr-service]
#image: assets/images/2016/05/intro-to-docker_title.png
description: "In this article, we'll learn how to get started with Azure SignalR service to scale out the service in the cloud without any limitation and infrastructure problems."
featured: false
hidden: false
#rating: 4.5
---

In this article, we'll learn how to get started with Azure SignalR service to scale out the service in the cloud without any limitation and infrastructure problems.

## Introduction

To scale up SingalR, we have a couple of options, like Radis and Azure Service. At the time of writing this article, Azure Service is in Preview. Soon, it will be released as version 1.0. It is a fully managed service to add real-time functionality to your application.

## Azure SignalR Service

It is a fully managed service to add a real-time update to any of your apps, not only web applications. It could be any client, like TypeScript /JavaScript client (Angular, React, VueJS) and .NET client. This service is capable of handling unlimited concurrent connections. So, you can scale out any level of the SignalR fast and best performing real-time framework. SignalR supports the native development on ASP.NET Core cross-platform development. It has ease of use API, you are good to go after changing a couple of lines only.

This article is a part of the series on SignalR using ASPNET Core.

* Overview of New Stack SignalR on ASPNET Core : Introduction - Part 1
* Getting Started With SignalR Using ASP.NET Core : Using Angular 5 - Part 2
* Getting started with SignalR using ASPNET Core : Streaming Data using Angular 5 - Part 3
* Getting started with SignalR using ASPNET Core : Dynamic Hub - Part 4
* Getting started with SignalR using ASPNET Core : Azure SignalR Service - Part 5 (this article)
* Getting started with SignalR using ASPNET Core : Using MessagePack Protocol - Part 6
* Getting Started With SignalR Using ASP.NET Core : Using Xamarin Android - Part 7

This article demonstrates the following.

* Creating Azure SignalR Service instance.
* Accessing Azure Service keys and Connection string.
* Creating Project Template using .NET CLI.
* Consuming the Azure Service by Angular Client.
* Demo.

The source code is available at [GitHub](https://github.com/nemi-chand/ASPNETCore-SignalR-AzureService).

Before reading this article, I would highly recommend you to go through the other articles of the series which are mentioned above.

## Creating Azure SignalR Service

Now, we are going to create an Azure SignalR service instance. Go to the Azure portal by accessing [here](http://portal.azure.com). If you don't have an Azure subscription, then get started with a free Azure account [here](https://azure.microsoft.com/en-us/free/) and try out.

To create an instance of service, click on "Create a resource" (top-left,) then search for SignalR service and click to that service. It will open a window detailing about the service and you will find the "Create" button at the bottom side.

![Azure SignalR Creating Service]({{site.baseUrl}}/assets/images/2018/07/Azure-SignalR-Portal_create-service.png)

Now, you have to fill the required details like Resource Name, Subscription, Resource Group, Location, Pricing tier. Azure pricing tier offers you free instance for a tryout.

![Azure SignalR Choose Tier]({{site.baseUrl}}/assets/images/2018/07/Azure-SignalR-Portal_create-service.png)

Accessing Azure Service keys and Connection string

After creating Azure SignalR service instance, now we need a service key and a connection string to use that service. The key info is available "Keys" link under Settings option.

![Azure SignalR Connection String]({{site.baseUrl}}/assets/images/2018/07/Azure-SignalR-Portal_connection-string.png)

We are done with the Azure SignalR service. Now, we have to consume that service in the client.

## Creating Project Template using DOTNET CLI

Now, we are going to create a project template to use Azure SignalR Service. This time, we'll use .NET command line interface to create the project template. It uses "DOTNET" keyword to start any command on CLI.

Command - to create a new project.

`dotnet new angular --name ASPNETCore_SignalR_AzureService`

Command uses -

* dotnet - prefix keyword to use any command in .NET CLI.
* new - create/initialize .net projects.
* Angular - ASPNET Core with the Angular template.
* Name - Name of the project to be created. If you not mention the name of the project template than project created by the folder name.

Read more about .NET commands [here](https://docs.microsoft.com/en-us/dotnet/core/tools).

The project has been created successfully using dotnet cli command. See the image below for reference.

![Dotnet Cli Command]({{site.baseUrl}}/assets/images/2018/07/Azure-SignalR-Portal_command-new.jpg)

## Consuming the Azure Service by Angular Client

We are going to use Azure SignalR client in order to use Azure Service. We need to install Azure SignalR client SDK "Microsoft.Azure.SignalR".

CLI Command

`dotnet add package Microsoft.Azure.SignalR --version 1.0.0-preview1-10015`

Nuget Package

`Install-package Microsoft.Azure.SignalR --version 1.0.0-preview1-10015`

![Dotnet Cli Command]({{site.baseUrl}}/assets/images/2018/07/Azure-SignalR-Portal_command-install.jpg)

Azure SignalR SDK allows the ease of use API just to change a couple of APIs and ready to use Azure SignalR service. In order to use this service, we have to AddAzureSignalR in configure service method and pass the Azure connection string which is copied from the Azure portal. I'm passing the same using configuration file (appsettings.json).

<script src="https://gist.github.com/nemi-chand/1c55f5bee294becc8cb98b6d49413565.js"></script>

After adding Azure SignalR service, we need to use the Azure SignalR route pipeline to map the hub which is done in the configure method. Map the hub with URL to work correctly.

```csharp
// Azure SignalR service
app.UseAzureSignalR(routes =>  
{  
    routes.MapHub<ChatHub>("/chat");  
});
```

We have done with Azure SignalR SDK.

Chat SignalR Service

<script src="https://gist.github.com/nemi-chand/1dbc163429f60801d2c3e001f59e72fe.js"></script>

Home Component

<script src="https://gist.github.com/nemi-chand/be7789205f7b09753a959d81d4830225.js"></script>

Demo screen

![Demo Screen]({{site.baseUrl}}/assets/images/2018/07/Azure-SignalR-Portal_Demo-Screen.jpg)

## Summery

In this article, we have seen scaling out SignalR on Azure using ASPNET Core and Angular 5. We have learned the following.

* Creating Azure SignalR service instance and accessing keys.
* Project template creation using dotnet cli.
* Consuming azure signalr service by Angular client.

## References

* [SignalR Docs](https://docs.microsoft.com/en-us/aspnet/core/signalr)
* [SignalR Github](https://github.com/aspnet/aspnetcore/tree/master/src/SignalR)
* [Angular Version Code](https://github.com/nemi-chand/ASPNETCore-SignalR-Angular-TypeScript)
* [Azure SignalR service Code](https://github.com/nemi-chand/ASPNETCore-SignalR-AzureService)
