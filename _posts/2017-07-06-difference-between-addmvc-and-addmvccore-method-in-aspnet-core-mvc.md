---
layout: post
title:  "Difference Between AddMvc And AddMvcCore Method In ASP.NET Core MVC"
author: Nemi Chand
categories: [ ASP.Net Core ]
tags: [aspnetcore]
#image: assets/images/2016/05/intro-to-docker_title.png
description: "In this article, we learn the best practice to register the generic interface in new ASP.NET Core framework."
featured: false
hidden: false
#rating: 4.5
---

In this article you will learn the differences between AddMVC() method and AddMvcCore() in ASP.NET Core MVC.

## Introduction

ASP.NET Core is a modular complete rewrite of the .NET cross-platform framework. This is a new framework with many awesome features. One of my favorite features is Dependency Injection. Every thing is on demand which means you have to enable whatever you need. There are a couple of methods to configure the MVC framework. Let's discuss each in detail.

## AddMvcCore Method

Adding this method enables the minimum dependency required to run the MVC framework. It adds essential MVC services to the specified method.

What is in this method?

* ApplicationPartManager : Manages the parts and features(list of controllers) of an MVC application.

* DefaultFeatureProviders : Adds the controller feature providers

* Default services : Adds the routing

* CoreServiecs : These are the basic services to run the MVC framework with minimum dependencies.

```csharp
/// <summary>  
    /// Adds essential MVC services to the specified <see cref="IServiceCollection" />.  
    /// </summary>  
    /// <param name="services">The <see cref="IServiceCollection" /> to add services to.</param>  
    /// <returns>An <see cref="IMvcCoreBuilder"/> that can be used to further configure the MVC services.</returns>  
    public static IMvcCoreBuilder AddMvcCore(this IServiceCollection services)  
    {  
        if (services == null)  
        {  
            throw new ArgumentNullException(nameof(services));  
        }  

        var partManager = GetApplicationPartManager(services);  
        services.TryAddSingleton(partManager);  

        ConfigureDefaultFeatureProviders(partManager);  
        ConfigureDefaultServices(services);  
        AddMvcCoreServices(services);  

        var builder = new MvcCoreBuilder(services, partManager);  

        return builder;  
    }
```

Let's try this method and see. I have created my MVC application. Now, I'm going to add this method in configure services in start up class.

```csharp
// This method gets called by the runtime. Use this method to add services to the container.  
    public void ConfigureServices(IServiceCollection services)  
    {  
        // Add framework services.  
        services.AddMvcCore();  
    }
```

Now, I have created two action methods on Home Controller to see the things in action.

```csharp
public class HomeController : Controller  
    {  
        public IActionResult AddMvcCore()  
        {  
            return Content("AddMvcCore method called");  
        }  
  
        public IActionResult AddMvc()  
        {  
            return View();  
        }  
    }
```

Now, I'm going to browse both the actions in the browser to see the magic.

First, we are browsing the AddMvcCore action and you are able to see the content returned from Controller action.

URL : http://localhost:1105/home/addmvccore

![AddMVCCore]({{ site.baseurl }}/assets\images\2017\07\addMvcCore.jpg)

Now, browse the Addmvc action and you will get an error page. If you want to use MVC features, then you have to use the AddMvc method instead of AddMvcCore.

URL : http://localhost:1105/home/addmvc

![AddMVC]({{ site.baseurl }}/assets\images\2017\07\addMvc.jpg)

## AddMvc Method 

In order to use MVC framework, you have to enable or you have to take the MVC project template . This method adds all dependencies in the project.

## What is in this Method?

This is the complete dependency for MVC framework to work all the features.

* **MvcCore** - This is the core dependency of MVC framework . These are the minimum dependencies required to run the MVC framework. The list of below features will not be available in this method.
* **ApiExplorer** - This enables the API help pages
* **Authorization** - Middleware for security and authorization of web apps.
* **DefaultFrameworkParts** - This adds some application part dependencies. Application part manager takes care of application parts and application features.
* **FormatterMappings** - A filter that will use the format value in the route data or query string to set the content type on an Object Result.
* **Views** - This enables the Views feature and makes it work.
* **RazorViewEngine** - Parser and code generator for CSHTML files are used in View pages for MVC web apps.
* **RazorPages** - Razor Pages is a new feature of ASP.NET Core MVC that makes coding page-focused scenarios easier and more productive.
* **CacheTagHelper** - Tag Helpers enable server-side code to participate in creating and rendering HTML elements in Razor files
* **DataAnnotations** - Data annotations enable validations and decorates the model.
* **JsonFormatters** - This enables the JSON data formatters .
* **Cors** - This enables the Cross-origin resource sharing (CORS), which is a mechanism that allows restricted resources .

```csharp
/// <summary>  
/// Adds MVC services to the specified <see cref="IServiceCollection" />.  
/// </summary>  
/// <param name="services">The <see cref="IServiceCollection" /> to add services to.</param>  
/// <returns>An <see cref="IMvcBuilder"/> that can be used to further configure the MVC services.</returns>  
public static IMvcBuilder AddMvc(this IServiceCollection services)  
{  
    if (services == null)  
    {  
        throw new ArgumentNullException(nameof(services));  
    }  

    var builder = services.AddMvcCore();  

    builder.AddApiExplorer();  
    builder.AddAuthorization();  

    AddDefaultFrameworkParts(builder.PartManager);  

    // Order added affects options setup order  

    // Default framework order  
    builder.AddFormatterMappings();  
    builder.AddViews();  
    builder.AddRazorViewEngine();  
    builder.AddRazorPages();  
    builder.AddCacheTagHelper();  

    // +1 order  
    builder.AddDataAnnotations(); // +1 order  

    // +10 order  
    builder.AddJsonFormatters();  

    builder.AddCors();  

    return new MvcBuilder(builder.Services, builder.PartManager);  
}
```

## Summary

In this article, we have learned the differences between both methods. This will help you to understand the internal picture of MVC frameworkand how it works.
