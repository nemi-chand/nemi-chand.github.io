---
layout: post
title:  "Multiple File Upload in Angular using ASP.NET Core 2.1"
author: Nemi Chand
categories: [ Angular, ASP.Net Core ]
tags: [csharp, aspnetcore,dotnetcore,angular]
image: assets/images/2018/05/file-upload-banner.webp
description: "In this article, we'll learn how to upload multiple files using Angular 5 and ASP.NET Core 2.1. the purpose of article to demonstrate multiple file upload with progress bar without using any angular third party plug-in"
featured: true
hidden: false
#rating: 4.5
---

In this article, we'll learn how to upload multiple files using Angular 5 and ASP.NET Core 2.1. The purpose of the article is to demonstrate multiple file upload with progress bar without using any Angular third-party plug-in.

### Prerequisite :

1. .NET Core latest version 2.1: download from [here](https://www.microsoft.com/net/download)
2. Angular 5: upgrade steps [here](https://update.angular.io/).

The source code is available on GitHub [here](https://github.com/nemi-chand/Angular5MultipleFileUpload) .

In Angular 5, Http Client module has been introduced and Http module deprecated. The Http Client module has introduced a few nice features which help in achieving the goal of this article.

### Http Client Features

1. Progress Events for both Request upload and Response Download
2. JSON parsing by default , no need to explicitly json parsing.
3. Immutable Request/Response objects.
4. Performance Improvements.

A glimpse of Angular Http Events. You have to subscribe HTTP client request for events after that you will get all the events notification like HttpEventType enum below.

#### HttpProgressEvent Interface

Progess event type is either upload or download
````csharp
/** 
 * Base interface for progress events. 
 * 
 * @stable 
 */  
export interface HttpProgressEvent {  
    /** 
     * Progress event type is either upload or download. 
     */  
    type: HttpEventType.DownloadProgress | HttpEventType.UploadProgress;  
    /** 
     * Number of bytes uploaded or downloaded. 
     */  
    loaded: number;  
    /** 
     * Total number of bytes to upload or download. Depending on the request or 
     * response, this may not be computable and thus may not be present. 
     */  
    total?: number;  
}  
````

#### HttpEventType enum

Type enumeration for the different kinds of `HttpEvent`

````csharp
/** 
 * Type enumeration for the different kinds of `HttpEvent`. 
 * 
 * @stable 
 */  
export declare enum HttpEventType {  
    /** 
     * The request was sent out over the wire. 
     */  
    Sent = 0,  
    /** 
     * An upload progress event was received. 
     */  
    UploadProgress = 1,  
    /** 
     * The response status code and headers were received. 
     */  
    ResponseHeader = 2,  
    /** 
     * A download progress event was received. 
     */  
    DownloadProgress = 3,  
    /** 
     * The full response including the body was received. 
     */  
    Response = 4,  
    /** 
     * A custom event from an interceptor or a backend. 
     */  
    User = 5,  
}  
````

Let's get started the things in action. Let’s open the visual studio and create the new ASP.NET Core Web Application project. See the below screen

![Create Project]({{ site.baseurl }}/assets\images\2018\05\fileupload_createproject.png)

After that first, you have to select .NET Core version from top of the dropdown list so I have selected 2.1 version then choose the angular template which we want to create. See the below screen

![Choose Template]({{ site.baseurl }}/assets\images\2018\05\fileupload_chooseTemplate.png)

We have created angular template project successfully. The latest version of visual studio automatically creates the Angular 5 template so need to explicit update or install. Build the project so that it will download all node modules.

Let's open the command prompt and browse the clientApp folder in CMD.

Create new Component using Angular cli. You have to start command with ng for all angular CLI commands.

To check the installed version of angular.

#### Version Command
````shell
ng --version

or

ng -v
````
![Command]({{ site.baseurl }}/assets\images\2018\05\fileupload_command_angular-version-info.jpg)

#### Generate Component Command 
````shell
ng generate component upload

or

ng g c upload

// g --> generate
// c --> component

````
![Command]({{ site.baseurl }}/assets\images\2018\05\fileupload_command_generate-component.jpg)

#### Upload Component code

````typescript
import { Component } from '@angular/core';  
import { HttpClient, HttpRequest, HttpEventType, HttpResponse } from '@angular/common/http'  
  
import { Uploader } from '../entities/uploader';  
import { UploadQueue } from '../entities/uploadqueue';  
  
  
@Component({  
  selector: 'app-upload',  
  templateUrl: './upload.component.html'  
})  
  
  
export class UploadComponent {  
  
  //getter : get overall progress  
  get progress(): number {  
    let psum = 0;  
  
    for (let entry of this.uploader.queue) {  
      psum += entry.progress;  
    }  
  
    if (psum == 0)  
      return 0;  
  
    return Math.round(psum / this.uploader.queue.length);  
  };  
  public message: string;  
  public uploader: Uploader = new Uploader();  
  
  constructor(private http: HttpClient) {  
    this.message = '';      
  }  
  
  onFilesChange(fileList: Array<File>) {  
    for (let file of fileList) {  
      this.uploader.queue.push(new UploadQueue(file));  
    };    
  }  
  
  onFileInvalids(fileList: Array<File>) {  
    //TODO handle invalid files here  
  }  
    
  onSelectChange(event: EventTarget) {  
    let eventObj: MSInputMethodContext = <MSInputMethodContext>event;  
    let target: HTMLInputElement = <HTMLInputElement>eventObj.target;  
    let files: FileList = target.files;  
    let file = files[0];  
    if (file) {  
      this.uploader.queue.push(new UploadQueue(file));  
      //console.log(file);  
      console.log('Total Count:' + this.uploader.queue.length);        
    }  
      
  }  
  
  // upload   
    upload(id) {  
    if (id == null)  
            return;  
  
      let selectedFile = this.uploader.queue.find(s => s.id == id);  
      if (selectedFile) {  
        const formData = new FormData();  
        formData.append(selectedFile.file.name, selectedFile.file);  
  
        const uploadReq = new HttpRequest('POST', `api/upload`, formData, {  
          reportProgress: true,  
        });    
        
        this.http.request(uploadReq).subscribe(event => {  
          if (event.type === HttpEventType.UploadProgress) {             
            selectedFile.progress = Math.round(100 * event.loaded / event.total);  
          }  
          else if (event.type === HttpEventType.Response)  
            selectedFile.message = event.body.toString();  
        });  
      }  
  }  
  //upload all selected files to server  
  uploadAll() {  
    //find the remaning files to upload  
    let remainingFiles = this.uploader.queue.filter(s => !s.isSuccess);  
    for (let item of remainingFiles) {  
      this.upload(item.id);  
    }  
  }  
  
  // cancel all   
  cancelAll() {  
    //TODO  
  }  
} 
````

Now, I'm going to create some entities/poco classes just to make the separation of things

1. Uploader - contains the queue of uploadqueue
2. Uploadqueue - contains the property of uploading queue like a file, progress, issue, success etc.
3. Guid - to generate random guid string

#### Generate Class Command 
````shell
ng generate class uploadqueue
````
![Command]({{ site.baseurl }}/assets\images\2018\05\fileupload_command_generate-class.jpg)

UploadQueue Class source code
````typescript
import { Guid } from '../entities/guid';  
  
/** 
 * Represents an UploadQueue 
 */  
export class UploadQueue {  
  id: string;  
  file: File;  
  progress: number;  
  message: string;  
  isCancel: boolean;  
  isError: boolean;  
  get isSuccess(): boolean {  
    if (this.progress == 100)  
      return true;  
  
    return false;  
  };  
  
  constructor(file: File) {  
    this.file = file;  
    this.progress = 0;  
    this.id = Guid.newGuid();  
    this.message = '';  
    this.isCancel = false;  
    this.isError = false;  
  }  
}  
````

Similar other classes like uploader and guid, you will find in entities folder.

Now, I'm going to create an Angular directive for drag and drop support so that you can directly drop the files and also you can allow/restrict the file name extensions.

#### Generate Directive Command
````shell
ng generate directive --name upload

or

ng g d --name upload
````
![Command]({{ site.baseurl }}/assets\images\2018\05\fileupload_command_generate-directive.jpg)

Now we have to add the upload link to route root and nav bar so that we can browse the uplaod template and see the things in action

Add root in App module
````typescript
{ path: 'upload', component: UploadComponent }
````
We have done with Aangular part, now I'm going to create web API controller for uploading the files
Create the new ASPNET Core MVC controller called 'upload' controller 

````csharp
using System;  
using System.Collections.Generic;  
using System.IO;  
using System.Linq;  
using System.Net.Http.Headers;  
using System.Threading.Tasks;  
using Microsoft.AspNetCore.Hosting;  
using Microsoft.AspNetCore.Mvc;  
  
namespace Angular5MultipleFileUpload.Controllers  
{  
  
    [Produces("application/json")]  
    [Route("api/[controller]")]  
    public class UploadController : Controller  
    {          
        private IHostingEnvironment _hostingEnvironment;  
  
        public UploadController(IHostingEnvironment hostingEnvironment)  
        {  
            _hostingEnvironment = hostingEnvironment;  
        }  
  
        [HttpPost]  
        public IActionResult Index()  
        {  
            try  
            {  
                var file = Request.Form.Files[0];  
                string folderName = "Upload";  
                string webRootPath = _hostingEnvironment.WebRootPath;  
                string newPath = Path.Combine(webRootPath, folderName);  
                if (!Directory.Exists(newPath))  
                {  
                    Directory.CreateDirectory(newPath);  
                }  
                if (file.Length > 0)  
                {  
                    string fileName = ContentDispositionHeaderValue.Parse(file.ContentDisposition).FileName.Trim('"');  
                    string fullPath = Path.Combine(newPath, fileName);  
                    using (var stream = new FileStream(fullPath, FileMode.Create))  
                    {  
                        file.CopyTo(stream);  
                    }  
                }  
                return Json("Upload Successful.");  
            }  
            catch (Exception ex)  
            {  
                return Json("Upload Failed: " + ex.Message);  
            }  
        }  
    }  
} 
````

Demo Screens

![Command]({{ site.baseurl }}/assets\images\2018\05\fileupload_demo1.png)
![Command]({{ site.baseurl }}/assets\images\2018\05\fileupload_demo2.png)
![Command]({{ site.baseurl }}/assets\images\2018\05\fileupload_demo3.png)


### Summary

In this article, we have learned how to create multiple upload files using angular 5 and asp.net core 2.1

We have learned below Angular CLI commands

1. Generate : this command is used to create the new components, routes, services, and pipes
2. Class : to create a new class
3. Directive : to create the new directive
4. Component : to create the new component

### References

* [Angular Update](https://update.angular.io/)
* [Angular Http](https://angular.io/api/common/http)
* [HttpEventType](https://angular.io/api/common/http/HttpEventType)
* [Github Source Code](https://github.com/nemi-chand/Angular5MultipleFileUpload)