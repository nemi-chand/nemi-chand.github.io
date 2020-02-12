---
layout: post
title:  "Introducing CookieManager Wrapper for Cookies in ASP.Net Core"
author: Nemi Chand
categories: [ ASP.Net Core ]
tags: [csharp, aspnetcore,httpcookie,aspnetcookie,aspnetcorecookie]
image: assets/images/2017/05/working-with-cookie_banner.jpg
description: "ASP.Net Core Abstraction layer on top of Http Cookie. ASP.NET Core Wrapper to read and write the cookie, object and how to secure cookie data."
featured: true
hidden: false
#rating: 4.5
toc: false
---

ASP.Net Core Abstraction layer on top of Http Cookie. ASP.NET Core Wrapper to read and write the cookie, object and how to secure cookie data.

## Introduction

In this article, you will learn how to work with cookies in an ASP.NET Core style (in the form of an interface), abstraction layer on top of cookie object and how to secure cookie data.
For more about working with cookie click here.

Source Code - [https://github.com/nemi-chand/CookieManager](https://github.com/nemi-chand/CookieManager)

CookieManager is a .NET Core library to extend the cookie object and secure the data, which is encryped by the machine key, using [IDataProtector](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotector) dataprotector. 

### Features

#### 1. Strongly Typed
CookieManager interface allows you to play with generic object. You don't have to care about casting or serialization.
````csharp
// get the myCookie object
MyCookie objFromCookie = _cookieManager.Get<MyCookie>("Key");
````

#### 2. Secure Cookie Data
The cookie data is protected with the machine key, using security algorithm. For more about [data protection](https://docs.microsoft.com/en-us/aspnet/core/security/data-protection/) . The encrypted data stored in browser if someone steal user data from cookie but stil data are safe. The attacker can not decreapt data and The Encryption key is stored at server where the application code is running.

#### 3. Configuration Options
There are easy options to configure CookieManager. Just add the CookieManager in Configure Service.
There are plenty of configuration Options

````csharp
//add CookieManager with options
services.AddCookieManager(options => 
{
  //allow cookie data to encrypt by default it allow encryption
  options.AllowEncryption = false;
  //Throw if not all chunks of a cookie are available on a request for re-assembly.
  options.ThrowForPartialCookies = true;
  // set null if not allow to devide in chunks
  options.ChunkSize = null;
  //Default Cookie expire time if expire time set to null of cookie
  //default time is 1 day to expire cookie 
  options.DefaultExpireTimeInDays = 10;
});
````

a. **AllowEncryption** : It allows you to encrypt cookie data.The default value is `True`. if you don't want data to be encreapted. just set it to `false`.

b. **ThrowForPartialCookies** : Throw if not all chunks of a cookie are available on a request for re-assembly. default value is `true`.

c. **ChunkSize** : The maximum size of cookie to send back to the client. If a cookie exceeds this size it will be broken down into multiple cookies. Set this value to null to disable this behavior. The default is 4090 characters, which is supported by all common browsers.Note that browsers may also have limits on the total size of all cookies per domain, and on the number of cookies per domain.

d. **DefaultExpireTimeInDays** : Default Cookie expire time if expire time set to null. Default time is 1 day(24 hours) to expire cookie.
 
#### 3. Func<TResult> support
Encapsulates a method, which returns a value of the type specified by the TResult parameter


#### 4. Ease to use
The interfaces allows to ease use of read and write http cookie.

### ICookieManager API's
Cookie Manager is an abstraction layer on top of ICookie Interface. This extends the Cookie behavior in terms of `<TSource>` generic support, `Func<TResult>`.This is implemented by DefaultCookieManager class . ICookie Interface is a depedenacy of this class. The list of APIs are given below in this interface.

1. Get API : Gets T object of cookie associated with key

2. GetOrSet API : Gets and Sets the cookie object. Get the object from cookies if cookie not present then set the cookie and get the object.

3. Set API :This is API uses to Set the cookie value.

4. Contains API : To check if the cookie exists in browser or not.

5. Remove API : Delete The cookie from browser.

### ICookie API

`ICookie` Interface is an abstraction layer on top of http cookie object, which secures the data. This is implemented by HttpCookie class. The list of APIs available in this interface are given below.

````csharp
public interface ICookie  
{  
    ICollection<string> Keys { get; }
    /// Gets a cookie item associated with key            
    string Get(string key);
    /// Sets the cookie
    void Set(string key, string value, int? expireTime);
    /// Sets the cookie
    void Set(string key, string value, CookieOptions option);  
    /// Contain the key
    bool Contains(string key);
    /// delete the key from cookie
    void Remove(string key);  
}   
````

### Usages

Add CookieManager in startup Configure Service

````csharp
// This method gets called by the runtime. Use this method to add services to the container.  
        public void ConfigureServices(IServiceCollection services)  
        {  
            // Add framework services.  
            services.AddMvc();  
  
            //add CookieManager  
            services.AddCookieManager();  
        } 
````

Access the CookieManager API.

````csharp
//Get or set <T>  
//Cookieoption example  
MyCookie myCook =  _cookieManager.GetOrSet<MyCookie>("Key", () =>   
{  
    //write fucntion to store  output in cookie  
    return new MyCookie()  
    {  
            Id = Guid.NewGuid().ToString(),  
            Indentifier = "value here",  
            Date = DateTime.Now  
    };  

},new CookieOptions() { HttpOnly = true,Expires = DateTime.Now.AddDays(1) });  

// expire time example  
MyCookie myCookWithExpireTime = _cookieManager.GetOrSet<MyCookie>("Key", () =>  
{  
    //write fucntion to store  output in cookie  
    return new MyCookie()  
    {  
        Id = Guid.NewGuid().ToString(),  
        Indentifier = "value here",  
        Date = DateTime.Now  
    };  

}, 60);
````


Source code is available on [Github](https://github.com/nemi-chand/CookieManager)

I hope, this wrapper helps you to play around with httpcookie in an ASP.NET Core. The source code is available for download at GitHub.

#### References

* [IHttpContextAccessor](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.http.ihttpcontextaccessor)
* [CookieManager](https://github.com/nemi-chand/CookieManager)