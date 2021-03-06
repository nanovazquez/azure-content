﻿<properties linkid="dev-net-2-how-to-storage-store-apps" urlDisplayName="Using Storage with Windows Store Apps" pageTitle="Using Windows Azure Storage with Windows Store Apps" metaKeywords="Get started Azure storage   Azure blob   Azure queue   Azure table   Windows Store   Windows Runtime Windows Azure Storage Client Library for Windows Runtime" metaDescription="Learn how to develop Windows Store apps that make use of Windows Azure Storage." metaCanonical="" disqusComments="1" umbracoNaviHide="1" />



# How to use Windows Azure Storage in Windows Store Apps

This guide shows how to get started with developing a Windows Store app that makes use of Windows Azure Storage.

## Download Required Tools ##

- [Visual Studio 2012](http://msdn.microsoft.com/en-us/library/windows/apps/br211384) makes it easy to build, debug, localize, package, and deploy Windows Store apps.
- [Windows Azure Storage Client Library for Windows Runtime](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/11/05/windows-azure-storage-client-library-for-windows-runtime.aspx) contains types used to communicate with Windows Azure Storage, packaged in a Windows Runtime component.
- [WCF Data Services Tools for Windows Store Apps](http://www.microsoft.com/en-us/download/details.aspx?id=30714) extends the Add Service Reference experience with client-side OData support for Windows Store Apps in Visual Studio 2012.

## Develop Apps ##

<h3>Getting ready</h3>

Create a new Windows Store app project in Visual Studio 2012:

![store-apps-storage-vs-project][]

Next, add a reference to the Windows Azure Storage Client Library by right clicking on **References**, then choosing **Add Reference**, and browsing to the Storage Client Library for Windows Runtime that you downloaded:

![store-apps-storage-choose-library][]

<h3>Using the library with the Blob and Queue services</h3>

At this point, your app is ready to communicate with the Blob and Queue services. Add the following **using** statements so that Windows Azure Storage types can be referenced directly:

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    
Next, add a button to your page. Add the following code to its **Click** event and modify your event handler method with the [async keyword](http://msdn.microsoft.com/en-US/library/vstudio/hh156513.aspx):
    
    var credentials = new StorageCredentials(accountName, accountKey);
    var account = new CloudStorageAccount(credentials, true);
    var blobClient = account.CreateCloudBlobClient();
    var container = blobClient.GetContainerReference("container1");
    await container.CreateIfNotExistsAsync();
    
This code assumes that you have two string variables, *accountName* and *accountKey*, which represent the name of your storage account and the account key associated with that account.

Build and run the application. Clicking on the button will first check if a container named *container1* exists in your account and create it if not.

<h3>Using the library with the Table service</h3>

Types used to communicate with Table service depend on WCF Data Services for Windows Store Apps library. Next, add a reference to the required WCF libraries by using Package Manager Console:

![store-apps-storage-package-manager][]

Use the following command to point Package Manager to the location on your machine:
    
    Install-Package Microsoft.Data.OData.WindowsStore -Source "C:\Program Files (x86)\Microsoft WCF Data Services\5.0\bin\NuGet"

This command will automatically add all required references to your project. If you do not want to use the Package Manager Console, you can also add the WCF Data Services NuGet folder on your local machine to the list of Package Sources and then add the reference through the UI as described in [Managing NuGet Packages Using the Dialog](http://docs.nuget.org/docs/start-here/Managing-NuGet-Packages-Using-The-Dialog).

When you have referenced the WCF Data Services NuGet package, change the code in your button’s **Click** event:
    
    var credentials = new StorageCredentials(accountName, accountKey);
    var account = new CloudStorageAccount(credentials, true);
    var tableClient = account.CreateCloudTableClient();
    var table = tableClient.GetTableReference("table1");
    await table.CreateIfNotExistsAsync();
    
This code checks whether a table named *table1* exists in your account, and creates it if not.

You can also add a reference to Microsoft.WindowsAzure.Storage.Table.dll, available in the same package that you downloaded. This library contains additional functionality such as reflection based serialization and generic queries. Note that this library does not support JavaScript.



[store-apps-storage-vs-project]: ../../../DevCenter/dotNet/Media/store-apps-storage-vs-project.png
[store-apps-storage-choose-library]: ../../../DevCenter/dotNet/Media/store-apps-storage-choose-library.png
[store-apps-storage-package-manager]: ../../../DevCenter/dotNet/Media/store-apps-storage-package-manager.png