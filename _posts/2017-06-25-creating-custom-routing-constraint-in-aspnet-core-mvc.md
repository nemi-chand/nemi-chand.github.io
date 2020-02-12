---
layout: post
title:  "Creating Custom Routing Constraint In ASP.NET Core MVC"
author: Nemi Chand
categories: [ ASP.Net Core ]
tags: [aspnetcore]
#image: assets/images/2016/05/intro-to-docker_title.png
description: "In this article, we learn the best practice to register the generic interface in new ASP.NET Core framework."
featured: false
hidden: false
#rating: 4.5
---

In this article, we'll learn how to create custom routing constraint in ASP.NET Core MVC. The routing plays a vital role in MVC framework.

## Introduction

 It takes care of how a request is handled and mapped to the route handler. This is configured in the start up class of configure method. Routing constraint is responsible for matching the constraint rules with incoming request. It returns yes/no (true /false ) response depending on the incoming request. If the request is matched, then it serves with the appropriate response/content; else it returns 404 error.

## Constraint Usages 

1. It helps to avoid the unnecessary requests on url where we are restricted to route value pattern. Let's say - in action parameter, only integer values are allowed; so except integers, all the requests are rejected by the route constraint and a 404 (Page not found ) response is returned to users.
2. To validate the route values : - routing constraint validates the incoming requests before passing them to Controller action.

## IRouteConstraint interface

It defines the contract that a class must implement in order to check whether a URL parameter value is valid for a constraint or not.

This interface contains only single API in order to match the constrains.

```csharp
//https://github.com/aspnet/Routing/blob/dev/src/Microsoft.AspNetCore.Routing.Abstractions/IRouteConstraint.cs
namespace Microsoft.AspNetCore.Routing  
{  
    /// <summary>  
    /// Defines the contract that a class must implement in order to check whether a URL parameter  
    /// value is valid for a constraint.  
    /// </summary>  
    public interface IRouteConstraint  
    {  
        /// <summary>  
        /// Determines whether the URL parameter contains a valid value for this constraint.  
        /// </summary>  
        /// <param name="httpContext">An object that encapsulates information about the HTTP request.</param>  
        /// <param name="route">The router that this constraint belongs to.</param>  
        /// <param name="routeKey">The name of the parameter that is being checked.</param>  
        /// <param name="values">A dictionary that contains the parameters for the URL.</param>  
        /// <param name="routeDirection">  
        /// An object that indicates whether the constraint check is being performed  
        /// when an incoming request is being handled or when a URL is being generated.  
        /// </param>  
        /// <returns><c>true</c> if the URL parameter contains a valid value; otherwise, <c>false</c>.</returns>  
        bool Match(  
            HttpContext httpContext,  
            IRouter route,  
            string routeKey,  
            RouteValueDictionary values,  
            RouteDirection routeDirection);  
    }  
} 
```

The Match method has 5 parameters . Let's discuss each, in details.

1. `HttpContext` encapsulates all http specific information about an http request like request, response, sessions, and more. You can check the httpcontext as well if the request is authenticated or not, and take the appropriate action accordingly.

2. `IRouter` is the router which belongs to constraints.

3. `RouteKey` is the name of the parameter that is being checked. The same name is defined in route template.

4. `Routevalues` contain the parameters of the URL.

5. `RouteDirection` indicates whether ASP.NET routing is processing a URL from an HTTP request or generating a URL. This is an enum class that has two directions.

    - IncomingRequest : A URL from a client is being processed.

    - UrlGeneration : A URL is being created based on the route definition.

## ConstraintMap

It is a key value pair dictionary which contains the list of route constraints. The routing framework adds the list of default constraints with their types. You can learn more about default constraints here on github.

You have to add custom route constraint in this dictionary with the help of route options.

Let's take an example of creating alphanumeric route constraints. This example is just for demonstration purposes; there are many ideas to use the constraints. You can use the regex constraints in case of any pattern matching.
Here, I'm going to create an alphanumeric constraint which checks if the incoming request parameter contains alphanumeric value or not.

Alphanumeric route constraint is implemented from the `IRouteConstraint` interface.

```csharp
public class AlphaNumericConstraint : IRouteConstraint  
    {  
        private static readonly TimeSpan RegexMatchTimeout = TimeSpan.FromSeconds(10);  
  
        public bool Match(HttpContext httpContext,   
            IRouter route,   
            string routeKey,   
            RouteValueDictionary values,   
            RouteDirection routeDirection)  
        {  
            //validate input params  
            if (httpContext == null)  
                throw new ArgumentNullException(nameof(httpContext));  
  
            if (route == null)  
                throw new ArgumentNullException(nameof(route));  
  
            if (routeKey == null)  
                throw new ArgumentNullException(nameof(routeKey));  
  
            if (values == null)  
                throw new ArgumentNullException(nameof(values));  
  
            object routeValue;  
  
            if(values.TryGetValue(routeKey, out routeValue))  
            {  
                var parameterValueString = Convert.ToString(routeValue, CultureInfo.InvariantCulture);  
                return new Regex(@"^[a-zA-Z0-9]*$",   
                                RegexOptions.CultureInvariant   
                                | RegexOptions.IgnoreCase, RegexMatchTimeout).IsMatch(parameterValueString);  
            }  
  
            return false;  
        }  
    }
```

I have alphanumeric regex to check the incoming request parameter that contains the a-z or 0-9.  

Add your constraint into ConstraintMap using route option in configure services method in the startup class. Here, you have to add route key with the type which is created above.

```csharp
public void ConfigureServices(IServiceCollection services)  
        {  
            // Add framework services.  
            services.AddMvc();  
  
            // add here your route constraint   
            services.Configure<RouteOptions>(routeOptions =>   
            {  
                routeOptions.ConstraintMap.Add("alphanumeric", typeof(AlphaNumericConstraint));  
            });  
        }  
```

Add your constraint in route template here. You have to use ":" seperator in between route parameter and constraints. The format looks like  {parameter name: route constraint name }.

```csharp
app.UseMvc(routes =>  
    {  
        routes.MapRoute(  
            name: "default",  
            template: "{controller=Home}/{action=Index}/{id:alphanumeric}");  
    });  
```

## Summary

In this article, you have learned how to create custom route constraints. I've demonstrated an example for the same to help you understand it more clearly.
