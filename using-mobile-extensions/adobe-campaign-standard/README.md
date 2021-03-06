# Adobe Campaign Standard

{% hint style="info" %}
**Before** you install or configure the Adobe Campaign Standard extension, read [_Getting Started_](../../getting-started/create-a-mobile-property.md) and [Configuring a mobile application using Adobe Experience Platform SDKs](https://helpx.adobe.com/campaign/kb/configuring-app-sdk.html).
{% endhint %}

{% hint style="danger" %}
If you participated in the Campaign Standard beta, to use the new Campaign Standard extension, use [launch.adobe.com](https://launch.adobe.com), instead of the Launch integration environment, .
{% endhint %}

## Configure the Campaign Standard extension in Launch

1. In Launch, click the **Extensions** tab.
2. On the **Catalog** tab, locate the **Adobe Campaign Standard** extension and click **Install**.
3. Provide the extension settings.
4. Click **Save**.
5. Follow the publishing process to update SDK configuration.

### Configure the Campaign Standard extension

![](../../.gitbook/assets/campaign-extension-config-v4.png)

#### ACS Endpoints

Provide endpoint URL\(s\) for your Adobe Campaign Standard instances. You can specify up to three unique endpoints for your development, staging, and production environments.

{% hint style="warning" %}
For this extension, these endpoint URLs should be typed in **without** the `http://` or `https://.`and **cannot** end with a forward slash.
{% endhint %}

#### pKey

A unique, auto-generated identifier for a mobile app configured in Adobe Campaign Standard. After you configured this extension in Launch, configure your Launch mobile property in Adobe Campaign Standard. When the configuration in Campaign is successful, pKey will be automatically generated, per Campaign Standard instance, and configured in Launch Campaign extension for successful validation.

#### MCIAS Region

Select an MCIAS region based on your customer's location or enter a custom endpoint. The SDK retrieves all in-app messaging rules and definition payloads from this endpoint.

{% hint style="warning" %}
For this extension, the custom MCIAS endpoint URL should be typed in **without** the `http://` or `https://` and **cannot** end with a forward slash.
{% endhint %}

#### Request Timeout

Time in seconds to wait for a response from the in-app messaging service before timing out. The default timeout value is 5 seconds, and the minimum timeout value is 1 second.

{% hint style="warning" %}
The **Request Timeout** value must be a non-zero number.
{% endhint %}

## Add Campaign Standard to your app

{% hint style="warning" %}
The Android version of this extension is currently in progress.
{% endhint %}

{% tabs %}
{% tab title="iOS" %}
{% hint style="warning" %}
This Campaign Standard extension requires the [Mobile Core](https://github.com/Adobe-Marketing-Cloud/aep-sdks-documentation/tree/08a65ca5e163ae9a213dc671e061216426a57435/using-mobile-extensions/adobe-campaign-standard/mobile-core/README.md) and [Profile](https://github.com/Adobe-Marketing-Cloud/aep-sdks-documentation/tree/08a65ca5e163ae9a213dc671e061216426a57435/using-mobile-extensions/adobe-campaign-standard/profile/README.md) extensions.
{% endhint %}

![](../../.gitbook/assets/acs-pods.png)

Add the Campaign Standard, [Mobile Core](https://github.com/Adobe-Marketing-Cloud/aep-sdks-documentation/tree/08a65ca5e163ae9a213dc671e061216426a57435/using-mobile-extensions/adobe-campaign-standard/mobile-core/README.md), and [Profile](https://github.com/Adobe-Marketing-Cloud/aep-sdks-documentation/tree/08a65ca5e163ae9a213dc671e061216426a57435/using-mobile-extensions/adobe-campaign-standard/profile/README.md) libraries to your project. You also need to add the following pods to your `Podfile`:

```text
use_frameworks!
pod 'ACPCampaign', '~> 1.0'
pod 'ACPUserProfile', '~> 2.0'
pod 'ACPCore', '~> 2.0'
```

or you can manually include the [Mobile Core](https://github.com/Adobe-Marketing-Cloud/acp-sdks/releases/tag/v2.0.1-ACPCore), [Campaign Standard](https://github.com/Adobe-Marketing-Cloud/acp-sdks/releases/tag/v1.0.2beta-ACPCampaign), and [Profile](https://github.com/Adobe-Marketing-Cloud/acp-sdks/releases/tag/v2.0.1-ACPUserProfile) extensions from Github.

In Xcode, import the Mobile Core, Campaign Standard, and Profile extensions:

#### Objective-C

```objectivec
#import "ACPCore.h"
#import "ACPCampaign.h"
#import "ACPUserProfile.h"
#import "ACPIdentity.h"
#import "ACPLifecycle.h"
#import "ACPSignal.h"
```

#### Swift

```swift
import ACPCore
import ACPCampaign
import ACPUserProfile
```
{% endtab %}
{% endtabs %}

### Register Campaign Standard with Mobile Core

{% tabs %}
{% tab title="iOS" %}
In your app's`application:didFinishLaunchingWithOptions:` method, register the Campaign Standard extension:

#### Objective-C   <a id="objective-c-1"></a>

```objectivec
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
    [ACPCampaign registerExtension];
    [ACPIdentity registerExtension];
    [ACPLifecycle registerExtension];
    [ACPSignal registerExtension];
    [ACPUserProfile registerExtension];
  // Override point for customization after application launch.
  return YES;
}
```

#### Swift

```swift
func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {
   ACPCampaign.registerExtension();
   ACPIdentity.registerExtension();
   ACPLifecycle.registerExtension();
   ACPSignal.registerExtension();   
   ACPUserProfile.registerExtension();
  // Override point for customization after application launch.
  return true;
}
```
{% endtab %}
{% endtabs %}

### Set up in-app messaging

{% hint style="info" %}
Need help creating an in-app message using Adobe Campaign? For more information, see [Preparing and sending an In-App message](https://helpx.adobe.com/campaign/standard/channels/using/preparing-and-sending-an-in-app-message.html).
{% endhint %}

For message types that allow you to target Adobe Campaign profiles \(CRM profiles\) that have subscribed to your mobile application, configure the personal attributes that are linked to their campaign profiles with the `setLinkageFields` API. For more information, see [Campaign API reference](adobe-campaign-standard-api-reference.md).

### Set up push messaging

{% hint style="warning" %}
iOS simulators do not support push messaging.
{% endhint %}

{% hint style="info" %}
Need help creating a push notification using Adobe Campaign? For more information, see [Preparing and sending a push notification](https://helpx.adobe.com/campaign/standard/channels/using/preparing-and-sending-a-push-notification.html).
{% endhint %}

After you follow [Apple's instructions](https://developer.apple.com/library/archive/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/HandlingRemoteNotifications.html#//apple_ref/doc/uid/TP40008194-CH6-SW1) to get your app ready to handle push notifications, set the push token by using the [`setPushIdentifier`](https://aep-sdks.gitbook.io/docs/using-mobile-extensions/mobile-core/identity/identity-api-reference#set-the-push-identifier) API:

{% tabs %}
{% tab title="iOS" %}
### setPushIdentifier

#### Objective-C

#### Syntax

```objectivec
+ (void) setPushIdentifier: (nullable NSData*) deviceToken;
```

#### Example

```objectivec
- (void) application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken {
  // Set the deviceToken that the APNS has assigned to the device
  [ACPCore setPushIdentifier:deviceToken];
  //...
}
```

#### Swift

```swift
ACPCore.setPushIdentifier(deviceToken)
```
{% endtab %}
{% endtabs %}

If everything is configured correctly, after installing your app on a mobile device, verify that the following debug logs are displayed:

### Debug log examples

The Campaign rules have been downloaded:

```text
2019-01-31 18:22:35.980872-0800 CampaignDemoApp[935:156012] [AMSDK DEBUG <com.adobe.module.campaign>]: Successfully downloaded Rules from 'https://mcias-va7.cloud.adobe.io/mcias/mcias.campaign-demo.adobe.com/PR8fdd35ee6cc84aa8bbdea8f92db3f55a/43583282444503123217621782542046274680/rules.zip
```

The request to demdex has been sent:

```text
2019-01-31 18:22:35.261676-0800 CampaignDemoApp[935:156015] [AMSDK DEBUG <com.adobe.module.identity>]: Sending request (https://dpm.demdex.net/id?d_rtbd=json&d_ver=2&d_orgid=B1F855165B4C9EA50A495E06@AdobeOrg&d_mid=43583282444503123217621782542046274680&d_blob=j8Odv6LonN4r3an7LhD3WZrU1bUpAkFkkiY1ncBR96t2PTI&dcs_region=9)
```

The push token:

```text
2019-01-31 18:22:34.881855-0800 CampaignDemoApp[935:155847] Push Token: c201fc7cc33243800802850ae65856f64f0cebc439c891eee8939682075afe75
```

{% hint style="warning" %}
Each `setPushIdentifier` call makes a new request to the demdex, which results in duplicated data that needs to be processed multiple times. To prevent system overload, do not call `setPushIdentifier` multiple times.
{% endhint %}

### Tracking for push and in-app messaging

To set up tracking postbacks for push and in-app messaging and create rules for in-app messaging tracking postbacks and push notifications tracking postbacks, go to [Create rules for push notifications tracking postback](https://helpx.adobe.com/campaign/kb/configuring-app-sdk.html#Step4Createrulesforpushnotificationstrackingpostback) and [Create rules for In-App tracking postback](https://helpx.adobe.com/campaign/kb/configuring-app-sdk.html#Step3CreaterulesforInApptrackingpostback) .

## Deleting mobile properties in Launch

{% hint style="danger" %}
Deleting your property in Launch might cause disruption to your recurring push and in-app messaging activities.
{% endhint %}

If you delete your mobile property in Launch, review your mobile property status in Campaign Standard and ensure that the property displays an updated **Deleted in Launch** status. For more information about deleting a property, see [Delete a Property](https://docs.adobelaunch.com/launch-reference/administration/companies-and-properties#delete-a-property). 

To remove the corresponding mobile app in Campaign Standard, click **Remove from ACS**. For more information, see [Configuring a mobile application using Adobe Experience Platform SDKs](https://helpx.adobe.com/campaign/kb/configuring-app-sdk.html).

{% hint style="warning" %}
Deleting your mobile property in Launch does not automatically delete your Campaign Standard mobile app.
{% endhint %}

