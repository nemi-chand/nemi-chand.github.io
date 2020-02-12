---
layout: post
title:  "Customizing Razor View Location in ASPNET Core MVC"
author: Nemi Chand
categories: [ ASP.Net Core ]
tags: [csharp, aspnetcore,razor]
image: assets/images/2017/05/working-with-cookie_banner.jpg
description: "In this article, we'll learn, how the View engine searches for path. Here is the best practice to set custom razor view location in MVC."
featured: false
hidden: false
---

In this article, we'll learn, how to customize Razor Engine View location using IViewLocationExpander . IViewLocationExpander takes care of modifying View locations , how the View engine searches for path. Here is the best practice to set custom razor view location in MVC. This feature was introduced in ASP.NET Core MVC.

You can create sub area in MVC. Read [here]({{site.baseUrl}}/creating-sub-areas-in-aspnet-core-mvc/) for more details.

There are couple of APIs in IViewLocationExpander Interface.

#### IViewLocationExpander APIs

1. ExpandViewLocations (ViewLocationExpanderContext, IEnumerable<String>) : this is invoked by Razor View Engine to determine the possible View locations . View Engine searches path in order it is added in the View locations, so the order of View locations does matter.
2. PopulateValues(ViewLocationExpanderContext) : this is called every time so as to populate the route values .

#### Project Solution Explorer
Here, we have MyViews folder instead of Views and folder structure would be the same. MyViews folder is highlighted in red circle.

![Custom View Folder]({{ site.baseurl }}/assets/images/2017/05/custom-razor-view_solution_explorer.jpg)

#### MyViewLocationExpander

ExpandViewLocations gets invoked while calling the action view each time. It provides Context and list of possible View locations path. You have to change all the possible locations with new location. Here, I'm changing the "Views" to "MyViews".

```csharp
/// <summary>  
    /// My view location expander  
    /// </summary>  
    public class MyViewLocationExpander : IViewLocationExpander  
    {  
  
        public IEnumerable<string> ExpandViewLocations(ViewLocationExpanderContext context,   
            IEnumerable<string> viewLocations)  
        {  
  
            //replace the Views to MyViews..  
            viewLocations = viewLocations.Select(s => s.Replace("Views", "MyViews"));  
  
            return viewLocations;  
        }  
  
        public void PopulateValues(ViewLocationExpanderContext context)  
        {  
            //nothing to do here.  
        }  
    }  
```

Register the MyViewLocationExpander in Startup class. You have to configure RazorViewEngineOptions into IServiceCollection and add custom View Expander to ViewLocationExpanders collections. 

```csharp
// This method gets called by the runtime. Use this method to add services to the container.  
    public void ConfigureServices(IServiceCollection services)  
    {  
        //register the MyViewLocationExpander into ViewLocationExpanders  
        services.Configure<RazorViewEngineOptions>(o => {  
            o.ViewLocationExpanders.Add(new MyViewLocationExpander());  
        });  

        // Add framework services.  
        services.AddMvc();  
    }  
```

#### Summery

In this write-up, we learned how we can customize the Razor View location and do awesome things with Razor View Engine.
