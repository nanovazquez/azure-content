<properties linkid="develop-notificationhubs-tutorials-get-started-ios" writer="elioda" urlDisplayName="Get Started" pageTitle="Get Started with Windows Azure Notification Hubs" metaKeywords="" metaDescription="Learn how to use Windows Azure Notification Hubs to send push notifications." metaCanonical="" disqusComments="0" umbracoNaviHide="1" />


# Get started with Notification Hubs
<div class="dev-center-tutorial-selector sublanding"> 
	<a href="/en-us/manage/services/notification-hubs/getting-started-windows-dotnet" title="Windows Store C#">Windows Store C#</a>
	<a href="/en-us/manage/services/notification-hubs/get-started-notification-hubs-wp8" title="Windows Phone">Windows Phone</a>
	<a href="/en-us/manage/services/notification-hubs/get-started-notification-hubs-android" title="Android">Android</a>
    <a href="/en-us/manage/services/notification-hubs/get-started-notification-hubs-ios" title="iOS" class="current">iOS</a>
</div>	


This topic shows you how to use Windows Azure Notification Hubs to send push notifications to an iOS application. 
In this tutorial you create a blank iOS app that receives push notifications using the Apple Push Notification service (APNs). When complete, you will be able to broadcast push notifications to all the devices running your app using your notification hub.

This tutorial walks you through these basic steps to enable push notifications:

1. [Generate the certificate signing request] 
2. [Register your app and enable push notifications]
3. [Create a provisioning profile for the app]
4. [Configure your Notification Hub]
5. [Connecting your app to the Notification Hub]
6. [Send notifications from your back-end]

This tutorial demonstrates the simple broadcast scenario using notification hubs. Be sure to follow along with the next tutorial to learn how to use notification hubs to address specific users and groups of devices. This tutorial requires the following prerequisites:

+ [Mobile Services iOS SDK]
+ [XCode 4.5][Install Xcode] 
+ An iOS 5.0 (or later version) capable device
+ iOS Developer Program membership

   <div class="dev-callout"><b>Note</b>
   <p>Because of push notification configuration requirements, you must deploy and test push notifications on an iOS capable device (iPhone or iPad) instead of in the emulator.</p>
   </div>

Completing this tutorial is a prerequisite for all other notification hub tutorials for iOS apps. 

<div class="dev-callout"><strong>Note</strong> <p>To complete this tutorial, you must have an active Windows Azure account. If you don't have an account, you can create a free trial account in just a couple of minutes. For details, see <a href="http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Windows Azure Free Trial</a>.</p></div>

The Apple Push Notification Service (APNS) uses certificates to authenticate your mobile service. Follow these instructions to create the necessary certificates and upload it to your Mobile Service. For the official APNS feature documentation, see [Apple Push Notification Service].



<h2><a name="certificates"></a><span class="short-header">Generate CSR file</span>Generate the Certificate Signing Request file</h2>

First you must generate the Certificate Signing Request (CSR) file, which is used by Apple to generate a signed certificate.

1. From the Utilities folder, run the Keychain Access tool.

2. Click **Keychain Access**, expand **Certificate Assistant**, then click **Request a Certificate from a Certificate Authority...**.

  ![][5]

3. Select your **User Email Address**, type **Common Name** and **CA Email Address** values, make sure that **Saved to disk** is selected, and then click **Continue**.

  ![][6]

4. Type a name for the Certificate Signing Request (CSR) file in **Save As**, select the location in **Where**, then click **Save**.

  ![][7]
  
  This saves the CSR file in the selected location; the default location is in the desktop. Remember the location chosen for this file.

Next, register your app with Apple, enable push notifications, and upload this exported CSR to create a push certificate.

<h2><a name="register"></a><span class="short-header">Register your app</span>Register your app for push notifications</h2>

To be able to send push notifications to an iOS app from mobile services, you must register your application with Apple and also register for push notifications.  

1. If you have not already registered your app, navigate to the <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">iOS Provisioning Portal</a> at the Apple Developer Center, log on with your Apple ID, click **Identifiers**, then click **App IDs**, and finally click on the **+** sign to register a new app.

   ![][105] 

2. Type a name for your app in **Description**, and a value for **Bundle Identifier**, check the "Push Notifications" option in the "App Services" section, and then click **Continue**.

   ![][106]

   ![][107] 

   ![][108]
   

   This generates your app ID and requests that you submit the information. Click **Submit**.
   
   ![][109] 
   
   Once you click **Submit**, you will see the **Registration complete** screen, as shown below. Click **Done**.
   
   ![][110]

    <div class="dev-callout"><b>Note</b>
	<p>If you choose to supply a <strong>Bundle Identifier</strong> value other than <b>MobileServices.Quickstart</b>, you must also update the bundle identifier value in your Xcode project.</p>
    </div>

3. Locate the app ID that you just created, and click on its row. 

   ![][111]
   
   Clicking on the app ID will display details about the app and the app ID:
   
   ![][112] 
   
   ![][113]

4. Click **Edit**, then scroll to the bottom of the screen and click **Create Certificate...** under the section **Development Push SSL Certificate**.

   ![][114] 

   This displays the "Add iOS Certificate" assistant.
   
   ![][115] 


    <div class="dev-callout"><b>Note</b>
	<p>This tutorial uses a development certificate. The same process is used when registering a production certificate. Just make sure that you set the same certificate type when you upload the certificate to Mobile Services.</p>
    </div>

5. Click **Choose File**, browse to the location in which you saved the CSR file that you created in the first task, and then click **Generate**. 

  ![][116]
  
6. After the certificate is created by the portal, click **Download**, and then click **Done**.
 
  ![][118]

  ![][119]  
  

   This downloads the signing certificate and saves it to your computer in your **Downloads** folder. 

  ![][9] 

    <div class="dev-callout"><b>Note</b>
	<p>By default, the downloaded file a development certificate is named <strong>aps_development.cer</strong>.</p>
    </div>

7. Double-click the downloaded push certificate **aps_development.cer**.

   This installs the new certificate in the Keychain, as shown below:

   ![][10]

    <div class="dev-callout"><b>Note</b>
	<p>The name in your certificate might be different, but it will be prefixed with <strong>Apple Development iOS Push Notification Services:</strong>.</p>
    </div>

Later, you will use this certificate to generate a .p12 file and upload it to your notification hub to enable push notifications through APNS.

<h2><a name="profile"></a><span class="short-header">Provision the app</span>Create a provisioning profile for the app</h2>
 
1. Back in the <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">iOS Provisioning Portal</a>, select **Provisioning Profiles**, select **All**, and then click the **+** button to create a new profile. This displays the **Add iOS Provisioning Profile** Wizard.

   ![][120]

2. Select **iOS App Development** under **Development** as the provisioning profile type, and click **Continue**.

   ![][121]

3. Next, select the app ID for the Mobile Services Quickstart app from the **App ID** drop-down list, and click **Continue**.

   ![][122]

4. In the **Select certificates** screen, select the certificate you created earlier, and click **Continue**.
  
   ![][123]

5. Next, select the **Devices** to use for testing, and click **Continue**.

   ![][124]

6. Finally, choose a name for the profile in **Profile Name**, click **Generate**, and click **Done**.

   ![][125]
   
   ![][126]
	
  This creates a new provisioning profile.

7. In Xcode, open the Organizer, select the Devices view, select **Provisioning Profiles** in the **Library** section in the left pane, and import the provisioning profile you just created.

8. On the left, select your device, and again import the provisioning profile. 

9. In Keychain Access, right-click the new certificate, click **Export**, type a name for your certificate, select the **.p12** format, and then click **Save**.

   ![][18]

  Make a note of the file name and location of the exported certificate.

This ensures that the Xcode project uses the new profile for code signing. Next, you must upload the certificate to your notification hub.

<h2><a name="configure-hub"></a><span class="short-header">Configure your Notification Hub</span>Configure your Notification Hub</h2>

1. Log on to the [Windows Azure Management Portal], and click **+NEW** at the bottom of the screen.

2. Click on **App Services**, then **Service Bus**, then **Notification Hub**, then **Quick Create**.

   ![][27]

3. Type a name for your notification hub, select your desired region, and then click **Create a new Notification Hub**.

   ![][28]

4. Click the namespace you just created (usually ***notification hub name*-ns**), and then click the **Configure** tab at the top.

   ![][29]

5. Click the **Notification Hubs** tab at the top, and then click on the notification hub you just created.

   ![][210]

6. Select the **Configure** tab at the top, and then click **Upload** for the Apple notification settings. Then select the **.p12** certificate you exported earlier, and the password for the certificate. Make sure to select whether you want to use the **Production** (if you want to send push notifications to users that purchased your app from the store) or the **Sandbox** (during development) push service.

   ![][211]

7. Click the **Dashboard** tab at the top, and then click **Connection Information**. Take note of the two connection strings.

   ![][212]

Your notification hub is now configured to work with APNs, and you have the connection strings to register your app and send notifications.

<h2><a name="connecting-app"></a><span class="short-header">Connecting your app</span>Connecting your app to the Notification Hub</h2>

1. In XCode, create a new iOS project and select the **Single View Application** template.

   ![][31]

2. Under **Targets**, click your project name, then  expand **Code Signing Identity**, then under **Debug** select the provisioning profile.

   ![][32]

3. Download the Windows Azure Mobile SDK for iOS. Open the .zip file and drag the folder WindowsAzureMessaging.framework into the Framework folder in your XCode project. Select **Copy items in destination group's folder**, then click **OK**.

   ![][33]

4. In your AppDelegate.h file add the following import directive:

         #import <WindowsAzureMessaging/WindowsAzureMessaging.h>

5. In your AppDelegate.m file add the following code in the `didFinishLaunchingWithOptions` method:

         [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];

6. In the same file, add the following method:

	    (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *) deviceToken {    
		    SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:
		                              @"<connection string>" notificationHubPath:@"mynh"];
		    
		    [hub registerNativeWithDeviceToken:deviceToken tags:nil completion:^(NSError* error) {
		        if (error != nil) {
		            NSLog(@"Error registering for notifications: %@", error);
		        }
	    	}];
		}

7. *(optional)* Again, in the same file, add the following method to display an **UIAlert** if the notification is received while the app is active:


        - (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
		    NSLog(@"%@", userInfo);
		    UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
		    [[userInfo objectForKey:@"aps"] valueForKey:@”alert”] delegate:nil cancelButtonTitle:
		    @"OK" otherButtonTitles:nil, nil];
		    [alert show];
		}
	
8. Run the app on your device.

<h2><a name="send"></a><span class="short-header">Send notification</span>Send notification from your back-end</h2>

You can send notifications using Notification Hubs from any back-end using our <a href="http://msdn.microsoft.com/en-us/library/windowsazure/dn223264.aspx">REST interface</a>. In this tutorial we will send notifications with a .NET console app, and with a Mobile Service using a node script.

To send notifications using a .NET app:

1. Create a new Visual C# console application: 

   ![][213]

2. Add a reference to the Windows Azure Service Bus SDK with the <a href="http://nuget.org/packages/WindowsAzure.ServiceBus/">WindowsAzure.ServiceBus NuGet package</a>. In the Visual Studio main menu, click **Tools**, then **Library Package Manager**, then **Package Manager Console**. Then, in the console window type:

        Install-Package WindowsAzure.ServiceBus

    and press Enter.

2. Open the file Program.cs and add the following using statement:

        using Microsoft.ServiceBus.Notifications;

3. In your `Program` class add the following method:

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            var alert = "{\"aps\":{\"alert\":\"Hello from .NET!\"}}";
            await hub.SendAppleNativeNotificationAsync(alert);
        }

4. Then add the following line in your `Main` method:

         SendNotificationAsync();
		 Console.ReadLine();

5. Press the F5 key to run the app. You should receive an alert on your device. If you are using Wi-fi, make sure your connection is working.

You can find all the possible payloads in the Apple [Local and Push Notification Programming Guide].

To send a notification using a Mobile Service, follow [Get started with Mobile Services], then:

1. Log on to the [Windows Azure Management Portal], and select your Mobile Service.

2. Select the tab **Scheduler** on the top.

   ![][215]

3. Create a new scheduled job, insert a name, and select **On demand**.

   ![][216]

4. When the job is created, click the job name. Then click the tab **Script** in the top bar.

5. Insert the following script inside your scheduler function. Make sure to replace the placeholders with your notification hub name and the connection string for *DefaultFullSharedAccessSignature* that you obtained earlier. Click **Save**.

		var azure = require('azure');
		var notificationHubService = azure.createNotificationHubService('<Hubname>', '<SAS Full access >');
		notificationHubService.apns.send(
	    	null,
    		{"aps":
        		{
          		"alert": "Hello from Mobile Services!"
        		}
    		},
    		function (error)
    		{
	        	if (!error) {
    	        	console.warn("Notification successful");
        		}
    		}
		);


6. Click **Run Once** on the bottom bar. You should receive an alert on your device.

## <a name="next-steps"> </a>Next steps

In this simple example you broadcast notifications to all your iOS devices. In order to target specific users refer to the tutorial [Use Notification Hubs to push notifications to users], while if you want to segment your users by interest groups you can read [Use Notification Hubs to send breaking news]. Learn more about how to use Notification Hubs in [Notification Hubs Guidance] and on the [Notification Hubs How-To for iOS].

<!-- Anchors. -->
[Generate the certificate signing request]: #certificates
[Register your app and enable push notifications]: #register
[Create a provisioning profile for the app]: #profile
[Configure your Notification Hub]: #configure-hub
[Connecting your app to the Notification Hub]: #connecting-app
[Send notifications from your back-end]: #send
[Next Steps]:#next-steps

<!-- Images. -->
[5]: ../media/mobile-services-ios-push-step5.png
[6]: ../media/mobile-services-ios-push-step6.png
[7]: ../media/mobile-services-ios-push-step7.png

[9]: ../media/mobile-services-ios-push-step9.png
[10]: ../media/mobile-services-ios-push-step10.png

[17]: ../media/mobile-services-ios-push-step17.png
[18]: ../media/mobile-services-ios-push-step18.png

[103]: ../media/mobile-services-ios-push-03.png
[105]: ../media/mobile-services-ios-push-05.png
[106]: ../media/mobile-services-ios-push-06.png
[107]: ../media/mobile-services-ios-push-07.png
[108]: ../media/mobile-services-ios-push-08.png
[109]: ../media/mobile-services-ios-push-09.png
[110]: ../media/mobile-services-ios-push-10.png
[111]: ../media/mobile-services-ios-push-11.png
[112]: ../media/mobile-services-ios-push-12.png
[113]: ../media/mobile-services-ios-push-13.png
[114]: ../media/mobile-services-ios-push-14.png
[115]: ../media/mobile-services-ios-push-15.png
[116]: ../media/mobile-services-ios-push-16.png
[117]: ../media/mobile-services-ios-push-17.png
[118]: ../media/mobile-services-ios-push-18.png
[119]: ../media/mobile-services-ios-push-19.png

[120]: ../media/mobile-services-ios-push-20.png
[121]: ../media/mobile-services-ios-push-21.png
[122]: ../media/mobile-services-ios-push-22.png
[123]: ../media/mobile-services-ios-push-23.png
[124]: ../media/mobile-services-ios-push-24.png
[125]: ../media/mobile-services-ios-push-25.png
[126]: ../media/mobile-services-ios-push-26.png

[27]: ../media/notification-hub-create-from-portal.png
[28]: ../media/notification-hub-create-from-portal2.png
[29]: ../media/notification-hub-select-from-portal.png
[210]: ../media/notification-hub-select-from-portal2.png
[211]: ../media/notification-hub-configure-ios.png
[212]: ../media/notification-hub-connection-strings.png


[213]: ../media/notification-hub-create-console-app.png
[214]: ../media/notification-hub-windows-toast.png


[215]: ../media/notification-hub-scheduler1.png
[216]: ../media/notification-hub-scheduler2.png


[31]: ../media/notification-hub-create-ios-app.png
[32]: ../media/notification-hub-create-ios-app2.png
[33]: ../media/notification-hub-create-ios-app3.png
[34]: ../media/notification-hub-create-ios-app4.png

<!-- URLs. -->
[Mobile Services iOS SDK]: http://go.microsoft.com/fwLink/?LinkID=266533
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[WindowsAzure.com]: http://www.windowsazure.com/
[Get started with Mobile Services]: ../../../DevCenter/Mobile/Tutorials/mobile-services-get-started-ios.md
[Windows Azure Management Portal]: https://manage.windowsazure.com/
[Notification Hubs Guidance]: http://msdn.microsoft.com/en-us/library/jj927170.aspx
[Notification Hubs How-To for iOS]: http://msdn.microsoft.com/en-us/library/jj927168.aspx
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456

[Use Notification Hubs to push notifications to users]: ./tutorial-notify-users-aspnet.md
[Use Notification Hubs to send breaking news]: ./breaking-news-dotnet.md

[Local and Push Notification Programming Guide]: http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1
