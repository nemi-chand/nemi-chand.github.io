---
layout: post
title:  "Register Generic Interface In ASP.NET Core"
author: Nemi Chand
categories: [ ASP.Net Core ]
tags: [aspnetcore]
image: assets/images/2016/05/intro-to-docker_title.png
description: "In this article, we learn the best practice to register the generic interface in new ASP.NET Core framework."
featured: false
hidden: false
#rating: 4.5
---

In this article, we learn the best practice to register the generic interface in new ASP.NET Core framework.

## Introduction

ASP.NET Core provides the built in Dependency Injection framework support with the framework. This is the most likely feature of Core framework and this is my favourite too. This injects the dependencies and resolves the same at runtime. Also, it provides the lifetime of dependencies.

You have to register the dependencies in IServiceCollection in startup class. ASP.NET Core supports the constructor Injections to resolve the injected dependencies.

Here, register the interface and their implementation into DI, using the add method of different lifetimes.

Register the non-generic interface, just simply add lifetime method with the interface and their implementaion

```csharp
//register the interface   
services.AddTransient<ILogger, FileLogger>();  

```

Register the generic interface i.e. the ILogger<T> into transient lifetime or you can register in any of lifetime method.

```csharp
//register the generic interface   
// this is not best way to register generic dependency  
 services.AddTransient<ILogger<T>, FileLogger<T>>();  

```

Best practice to register generic interface ILogger<> without T.

```csharp
// best practice  
 services.AddTransient(typeof(ILogger<>),typeof(FileLogger<>));  

```

## Summary

You have learned, how you can register generic interface with best practices in ASP.NET Core. The reference is given below to know more.

## References

* [Dependency Injection](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/dependency-injection)

