---
layout: post
title:  "Creating Sub Areas In ASP.NET Core MVC"
author: Nemi Chand
categories: [ ASP.Net Core ]
tags: [csharp, aspnetcore,razor,sub-area,aspnetcore-mvc]
image: assets/images/2017/05/sub-area-in-aspnet-mvc_folderStructure.jpg
description: "In this article, we'll learn how to create sub area in ASP.Net Core mvc using IViewLocationExpander. IViewLocationExpander takes care to modify view locations."
featured: false
hidden: false
---


In this article, we'll learn sub area, using IViewLocationExpander. IViewLocationExpander takes care to modify view locations, how the view engine searches for the path.

The source code is available @ [Github](https://github.com/nemi-chand/SubArea.ASPNetCoreMVC)

Areas are ASP.NET MVC Core features, which are used to organize the functionality into the groups. Read more about areas here.

### Sub area in MVC

Steps to create the Sub area in MVC are given below.

1. Sub area folder structure.
2. SubArea RouteValueAttribute.
3. SubAreaViewLocationExpander.
4. Configure Razor View Engine option in startup class.
5. Attribute usages.
6. SubArea Routes.

#### Folder structure

Area refers to MVC features, which are used to restructure or group the functionality, separate the Controller and Views of each area. In ASP.NET Core MVC, we can create n-level of sub areas under an area.

Create the folder structure, as shown below.

![Folder Structure]({{ site.baseurl }}/assets\images\2017\05\sub-area-in-aspnet-mvc_folderStructure.jpg)

#### SubArea Attribute

It is a key-value pair RouteValueAttribute, which specifies the containing Controller or an action. This is a must to decorate the Controller with this attribute.

This is similar to an area attribute. To more about it, [click here](https://github.com/aspnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/AreaAttribute.cs).

The route key is fixed for subarea routes and route value changes as sub area is requested. This attribute usage is restricted to a class and a method.

```csharp
/// <summary>  
    /// this is simillar to this https://github.com/aspnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/AreaAttribute.cs  
    /// </summary>  
    [AttributeUsage(AttributeTargets.Class | AttributeTargets.Method, AllowMultiple = false, Inherited = true)]  
    public class SubAreaAttribute : RouteValueAttribute  
    {  
        public SubAreaAttribute(string subAreaName)  
            : base("subarea", subAreaName)  
        {  
            if (string.IsNullOrEmpty(subAreaName))  
            {  
                throw new ArgumentException("Sub area name cannot be null or empty", nameof(subAreaName));  
            }  
        }  
    }  
```

#### Sub Area View location Expander

This is implementing the IViewLocationExpander interface to expand the sub area view location. You can set view location. The route key is set to subarea and gets the same key value from RazorviewEngine, which routes to place in runtime location path. View engine string format follows as {0} --> action Name , {1} --> Controller name and {2} --> name of the area, if it exists. The order of view locations matters because the engine search for the path is added into the order.

```csharp
/// <summary>  
    /// sub area view location expander  
    /// </summary>  
    public class SubAreaViewLocationExpander : IViewLocationExpander  
    {  
        private const string _subArea = "subarea";  
  
        public IEnumerable<string> ExpandViewLocations(ViewLocationExpanderContext context, 
            IEnumerable<string> viewLocations)  
        {  
            //check if subarea key contain  
            if (context.ActionContext.RouteData.Values.ContainsKey(_subArea))  
            {  
                string subArea = RazorViewEngine.GetNormalizedRouteValue(context.ActionContext, _subArea);  
                IEnumerable<string> subAreaViewLocation = new string[]  
                {  
                "/Areas/{2}/SubAreas/"+subArea+"/Views/{1}/{0}.cshtml"  
                };  
  
                viewLocations = subAreaViewLocation.Concat(viewLocations);  
  
            }  
  
            return viewLocations;  
        }  
  
        public void PopulateValues(ViewLocationExpanderContext context)  
        {  
            string subArea = string.Empty;  
            context.ActionContext.ActionDescriptor.RouteValues.TryGetValue(_subArea, out subArea);  
  
            context.Values[_subArea] = subArea;  
        }  

```


#### Configure Razor View Options

In the startup class, you have to configure Razor view engine options in Configure Services method.

```csharp
services.Configure<RazorViewEngineOptions>(o => 
{  
    o.ViewLocationExpanders.Add(new SubAreaViewLocationExpander());  
});
```

#### Attribute usages in Controller
Just decorate your Controller with an area and subarea attribute.

```csharp
[Area("Department")]  
[SubArea("IT")]  
public class HomeController: Controller {  
    /// <summary>    
    /// GET /Department/IT/Home/Index    
    /// </summary>    
    /// <returns></returns>    
    public IActionResult Index() {  
        return View();  
    }  
} 
```

#### Routes

Route key is for sub area, which is similar to an area route. Sub area route key will be same in view location expander. 

```csharp
app.UseMvc(routes =>    
    {    
        routes.MapRoute(    
            name: "subAreaRoute",    
            template: "{area:exists}/{subarea:exists}/{controller=Home}/{action=Index}/{id?}");    
        routes.MapRoute(    
            name: "areaRoute",    
            template: "{area:exists}/{controller=Home}/{action=Index}/{id?}");    
        routes.MapRoute(    
            name: "default",    
            template: "{controller=Home}/{action=Index}/{id?}");    

    });
```

#### Demo Screens

##### Department Area screen
```csharp
//GET Deparment/Home/Index
```
![Deparment area]({{ site.baseurl }}/assets\images\2017\05\sub-area-in-aspnet-mvc_department.jpg)

##### IT Sub-Area screen
```csharp
//GET Deparment/IT/Home/Index
```
![IT Sub Area]({{ site.baseurl }}/assets\images\2017\05\sub-area-in-aspnet-mvc_it.jpg)

##### Marketing Sub-Area screen

```csharp
//GET Deparment/Marketing/Home/Index
```
![Marketing sub area]({{ site.baseurl }}/assets\images\2017\05\sub-area-in-aspnet-mvc_marketing.jpg)

The source code is available @ [GitHub](https://github.com/nemi-chand/SubArea.ASPNetCoreMVC)

In this article you have learned how you can create a sub area under the area with n-level. IViewLocationExpander helps to modify the view location paths.