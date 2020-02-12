---
layout: post
title:  "Working With Cookies in ASP.NET Core"
author: Nemi Chand
categories: [ ASP.Net Core ]
tags: [csharp, aspnetcore,httpcookie,aspnetcookie,aspnetcorecookie]
image: assets/images/2017/05/working-with-cookie_banner.jpg
description: "This article explains how ASP.NET Core deals with cookies. Cookies are key-value pair collections where we can read, write and delete using key. HTTP Cookie is some piece of data which is stored in the user's browser."
featured: false
hidden: false
#rating: 4.5
---

This article explains how ASP.NET Core deals with cookies. Cookies are key-value pair collections where we can read, write and delete using key. HTTP Cookie is some piece of data which is stored in the user's browser.

## Introduction
 
HTTP Cookie is some piece of data which is stored in the user's browser. HTTP cookies play a vital role in the software world. We can store users' related information in cookies and there are many other usages. In asp.net core working with cookies is made easy. I've written a couple of abstraction layers on top of Http cookie object. Cookies are key-value pair collections where we can read, write and delete using key.
 
In ASP.NET, we can access cookies using httpcontext.current but in ASP.NET Core, there is no htttpcontext.currently. In ASP.NET Core, everything is decoupled and modular.
 
`Httpcontext` is accessible from Request object and the [IHttpContextAccessor](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.http.ihttpcontextaccessor) interface which is under `Microsoft.AspNetCore.Http` namespace and this is available anywhere in the application. 
 
I have written a wrapper on top of Http Cookie which helps you to ease of use and secure the cookie data. CookieManager is an ASPNET Core Abstraction layer on top of cookie. Read more [here](https://github.com/nemi-chand/CookieManager) .
 
#### Reading Cookie 
 
HttpCookie is accessible from Request.Cookies. Given below is the sample code. 

````csharp

//read cookie from IHttpContextAccessor  
string cookieValueFromContext = _httpContextAccessor.HttpContext.Request.Cookies["key"];  

//read cookie from Request object  
string cookieValueFromReq = Request.Cookies["Key"];

````

#### Writing cookie 
 
Here is the code snippet to write cookies. Set method to write cookies. CookieOption is available to extend the cookie behavior.

````csharp
/// <summary>  
/// set the cookie  
/// </summary>  
/// <param name="key">key (unique indentifier)</param>  
/// <param name="value">value to store in cookie object</param>  
/// <param name="expireTime">expiration time</param>  
public void Set(string key, string value, int? expireTime)  
{  
   CookieOptions option = new CookieOptions();  

   if (expireTime.HasValue)  
         option.Expires = DateTime.Now.AddMinutes(expireTime.Value);  
   else  
         option.Expires = DateTime.Now.AddMilliseconds(10);  
   
   Response.Cookies.Append(key, value, option);  
}
````

#### Remove Cookie
 
Delete the cookie by key name.

````csharp
/// <summary>  
/// Delete the key  
/// </summary>  
/// <param name="key">Key</param>  
public void Remove(string key)  
{  
      Response.Cookies.Delete(key);  
}
````

#### CookieOptions
 
It extends the cookie behavior in the browser.
 
**Options**
1. Domain - The domain you want to associate with cookie
2. Path - Cookie Path
3. Expires - The expiration date and time of the cookie
4. HttpOnly - Gets or sets a value that indicates whether a cookie is accessible by client-side script or not.
5. Secure - Transmit the cookie using Secure Sockets Layer (SSL) that is, over HTTPS only.  

Here is the complete code example to read, write and delete the cookie.

````csharp
public class HomeController : Controller  
{  
    private readonly IHttpContextAccessor _httpContextAccessor;  
  
    public HomeController(IHttpContextAccessor httpContextAccessor)  
    {  
        this._httpContextAccessor = httpContextAccessor;  
    }  
    public IActionResult Index()  
    {  
        //read cookie from IHttpContextAccessor  
        string cookieValueFromContext = _httpContextAccessor.HttpContext.Request.Cookies["key"];  
        //read cookie from Request object  
        string cookieValueFromReq = Request.Cookies["Key"];  
        //set the key value in Cookie  
        Set("kay", "Hello from cookie", 10);  
        //Delete the cookie object  
        Remove("Key");  
        return View();  
    }  
    /// <summary>  
    /// Get the cookie  
    /// </summary>  
    /// <param name="key">Key </param>  
    /// <returns>string value</returns>  
    public string Get(string key)  
    {  
        return Request.Cookies[Key];  
    }  
    /// <summary>  
    /// set the cookie  
    /// </summary>  
    /// <param name="key">key (unique indentifier)</param>  
    /// <param name="value">value to store in cookie object</param>  
    /// <param name="expireTime">expiration time</param>  
    public void Set(string key, string value, int? expireTime)  
    {  
        CookieOptions option = new CookieOptions();  
        if (expireTime.HasValue)  
            option.Expires = DateTime.Now.AddMinutes(expireTime.Value);  
        else  
            option.Expires = DateTime.Now.AddMilliseconds(10);  
            Response.Cookies.Append(key, value, option);  
    }  
    /// <summary>  
    /// Delete the key  
    /// </summary>  
    /// <param name="key">Key</param>  
    public void Remove(string key)  
    {  
        Response.Cookies.Delete(key);  
    }  
}
````

I hope you learned how to work with cookies in ASP.NET Core. I have shown you an example of reading, writing and removing cookie objects.

#### References

* [IHttpContextAccessor](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.http.ihttpcontextaccessor)
* [CookieManager](https://github.com/nemi-chand/CookieManager)