
# BlueRange SDK

## What does this SDK do?
"BlueRange SDK" is a library for the Android and iOS platform that makes it easy to write mobile applications that interact with Bluetooth Low Energy beacons in general and Relution SmartBeacons in specific. The API is intended to provide an easy to use programming interface to develop time and location based applications that make use of BlueRange mesh networks. For more information about the BlueRange technology, have a closer look at this project on GitHub. 

The SDK is specifically designed to interact with beacons that are enrolled in the Relution Beacon Management platform. E.g. you can use the management platform to instruct beacons to send a message which can later trigger actions on an Android or iOS device using this SDK. At the moment the SDK supports iBeacon, Eddystone, BlueRange and Relution Tag messages. 

The idea behind Relution Tags is to assign each beacon a number of tags that will be sent out by the beacon in periodic intervals. A mobile app using this SDK receives these tags and individually interprets them by e.g. displaying product informations or triggering some other kind of app behavior. Since the app itself is responsible for mapping each tag to a specific behavior, there is no need of continuous internet availability. Although the concept of Relution tags does not allow you to change the app's behavior dynamically, Relution makes it possible to exchange the tags a beacon sends out and, thus, enables your app to have a partial dynamic behavior without the requirement of constant internet access. 

iBeacons, on the other side, are currently used to realize a concept, which we call "campaigns". A campaign allows you to define a set of actions that will be triggered within a mobile app, whenever the device is next to a beacon that takes part in the campaign. In contrast to the concept of Relution tags, the mapping of iBeacon and app behavior is not done by the app itself but by Relution. The drawback of this concept is that your app constantly needs internet access. The advantage, however, is, that the content and behavior that is associated with the action, will be instantly updated, whenever it it is changed in Relution.

## Features
Currently the BlueRange SDK supports iOS devices that run at least on iOS 8.0 and Android devices with at least API level 4.3. However, to enable advertising, Android devices must additionally support the Bluetooth LE peripheral mode and run at least on API level 21.

The BlueRange SDK is divided into two layers, a core and a service layer. The core layer consists of a set of message processing components that can be combined to a flexible event driven architecture. The service layer builds on top of the core layer and integrates the message processing components with the Relution platform. The current version supports the following features:

### Core
#### Advertising
- Sending advertising messages of arbitrary data.

#### Scanning
- The beacon scanner supports different types of message formats:
    - iBeacon message: Apple's standardized iBeacon format for BLE advertising.
    - Eddystone UID message: One of Google's standardized BLE beacon profile which consists of a 10-byte namespace UID and a 6-byte instance identifier.
    - Eddystone URL message: Another Eddystone beacon format which contains a URL.
    - Join Me message: This is an advertising format which can be used to analyze BlueRange beacons. Each packet contains information about the beacon id, its connectivity etc.
    - Relution Tag message: An advertising format supported by the Relution beacon management platform. It contains a list of Relution tags which can be mapped to actions a mobile device can trigger in offline scenarios.
- iBeacon messages, Relution Tag and BlueRange specific messages. 
- The scan procedure will be continued when the app is running in background, even when the user attempts to terminate the app.
- The energy consumption can be adjusted dynamically by changing scan properties like the scan interval.

#### Logging
- Messages can be saved persistently and postprocessed at a later time. The log will be persisted with a minimum amount of energy consumption.

#### Reporting
- Messages logged over a long period of time can be transformed to status reports and periodically sent to a mesh management system like the Relution SmartBeacon management system.

#### Triggering
- A stream of beacon messages can trigger an action which will be executed based on time and location based parameters. Actions can e.g. be executed after a predefined delay or locked for a specific duration. Moreover, actions can be defined to trigger only inside a certain distance to the beacon or can have a validation period.
- To make the execution of distance dependent actions more stable, the messages' received signal strength (RSSI) is averaged over time by using a linearly weighted moving average filter.

### Service
As mentioned above, the service layer builds on top of the core layer and provides some high level features to integrate the message processing components with the Relution SmartBeacon Management platform. Concretely, it enables you
- to calibrate iBeacon messages to improve the precision of distance dependent actions.
- to map Relution Tags to their names and descriptions defined in the Relution platform.
- to periodically send specific advertising messages, that will be used to generate and display a heatmap on the Relution SmartBeacon management platform.

## API reference
- All currently supported features are bundled in the ```RelutionIoTService``` class. However, if you want more flexibility, you can build your app on top of the core and service components. To get an overview of the SDK's packages and classes, please have a look at our documentation in the ```/docs``` directory.

## Getting started
### Android
- In order to get started with your own app, you first need to unzip the "BlueRangeSDK_Android.zip".
- Next, start the Android IDE of your choise (e.g. Android Studio) and import the project contained in the unpacked folder. To do this with Android Studio, just click on "Open an existing Android project" right after Android Studio has been started and choose the unpacked folder.
- After the project has been imported, just have a look at the project structure on the left side bar.
- As you can see, the ```com.mway.bluerange.android``` package contains two subpackages ```examples``` and ```sdk```.
- The ```sdk``` package contains the SDK's source code. So, if you are interested in the SDK implementation, you should have a closer look at these packages.
- In the ```examples``` package you can see how the SDK components can be used within your app. The ```systemtests``` package contains code examples that show you, how to use the SDK's core components.
- The ```nearyou``` package contains a reference application displaying you the currently received beacon messages and the executed actions. You can test the app, if you have a Bluetooth Low Energy capable device. In order to start the app, you only need to build, deploy and start the application. If you are using Android Studio, you can simply use the keyboard shortcut ```Shift+F10```.
- The best way to start writing your own app would be to replace all classes of the NearYou app and transform the code to your own needs.

### iOS
- To use the iOS SDK, first unzip the "BlueRangeSDK_iOS.zip".
- Next, start Xcode and import the project that is contained in the unpacked folder. To do this, just click on "Open another project" on the "Welcome to Xcode" screen and choose the BlueRange_SDK_iOS.xcodeproj contained inside the subfolder "bluerangesdk".
- After the project has been imported, just have a look at the project structure on the left side bar.
- As you can see, the "sdk" folder contains two subfolders called "examples" and "sdk".
- The "sdk" package contains the SDK's source code. So if you are interested in how we implemented the SDK components, you should have a closer look at the packages contained inside.
- In the "examples" folder you can see, how the SDK components can be used within your app code. The ```ExampleApplication```class contains some basic examples, of how the background scanning and advertising services can be started.
- The folder "nearyou" contains an example app, similar to the one contained in the Android SDK. You can test the app, if you have an iOS 8.0 device. In order to start the app, you only need to build, deploy and start the application by clicking on the "Run" button on the top left corner of Xcode. After the app has been built and deployed, you should see the example app on your device listing some sample product names how it could look in an app using this SDK.
- To experiment with the SDK, you can have a look at the source code of the ```ExampleApplication``` which shows you how to use the core components of the SDK.

## Reference application

The RelutionIoTApp is a reference application, which shows you the messages that are currently being received by the app and the actions being executed. 

Before you start the app, you must configure the ```RelutionIoTService``` class in the ```RelutionIoTAppDelegate``` class (iOS) or in the ```Application``` class (Android), so that the correct Relution organization will be used. Please, have a look at the "Sample code" section, to see, what the specific fields of the ```RelutionIoTService```'s configuration mean.

In order to calibrate an iBeacon, just place your device 1 meter away from the beacon, wait about 5 seconds, and click on the "Calibrate" button. To verify, whether the iBeacon has correctly been calibrated, the "Calibrated RSSI" value should have changed to a new value. For each iBeacon an estimated distance is calculated. The SDK currently supports two distance estimation algorithms. The analytical distance estimator computes the distance out of an averaged RSSI value based on a mathematical model. The empirical distance estimator on the other side is based on a distance estimation scheme implemented in the Android beacon library, which calculates distances based on measurements that where collected from different Android devices.

![alt tag](https://raw.githubusercontent.com/ehrlich89/images/master/02_iBeacons2.png)

Next to the tabs "iBeacon", "Relution Tags" and "JoinMe", the "Visited", "Content" and "Notification" tabs allow you to monitor actions, as you can see in the screenshot below. The "last update" field displays the last time, when a message was received being able to initiate the action. "Trigger threshold" shows you the distance threshold that you specified when you created the action in Relution. The bar besides "distance to trigger" shows you the distance to the trigger threshold. A negative distance means, that you are inside the radius, where actions will be triggered, whereas a positive distance indicates, that you are outside the activity region. Finally, the "Trigger locked" and "Trigger delayed" fields indicate the lock and delay duration of the action.

![alt tag](https://raw.githubusercontent.com/ehrlich89/images/master/05_Action2.png)

## Sample code
The following section shows you, how the most important SDK features can be integrated inside your app. As described above, you can use the ```RelutionIoTService``` class, if you do not need the flexibility of the underlying message processing architecture and just want to get informed about executed actions, incoming messages or just want to turn on/off features.

### Configuration
The first thing you have to do is to add the ```RelutionIoTService``` service to your ```AndroidManifest.xml```. Next, create an instance of this class inside the ```onCreate``` method of your  ```android.app.Application``` subclass. To start the service, you must call the ```startInForegroundMode``` or ```startInBackgroundMode``` method. If you start the service in background mode, the service will continue to process messages, even when the user tries to terminate the app. The minimum requirement before starting the service, however, is to call the ```setConfig``` method, where you pass the Relution URL, the organization UUID and the authentication data, you use to log in. You find the organization UUID in Relution, when you click on the information tab of your site. The organization UUID is the "API Key" in the list. Moreover, you can enable/disable features by calling the ```set...Enabled``` methods. By default, all features are enabled. However, having all features turned on, might decrease the overall performance of your application. The easiest way to improve the performance, is to set ```loggingEnabled``` to ```false```. This will stop logging debug messages to the Android logcat. ```setCampaignActionTriggerEnabled``` enables/disables the execution of campaign actions. If you enable ```heatmapGeneration```, the ```RelutionIoTService``` will constantly send specific advertising messages to the beacons. The beacons will collect this information and send it to Relution, where it will be used to generate and display the heatmap. Setting ```heatmapReporting``` to ```true```, will turn a status reporter on, that constantly collects BlueRange specific beacon messages and periodically sends status reports to Relution which can be analyzed and postprocessed at a later time.

```java
String baseUrl = "https://url-to-relution.com";
String organizationUuid = "11111111-2222-3333-4444-555555555555";
String username = "your_username";
String password = "your_password";

new RelutionIoTService()
    .setConfig(baseUrl, organizationUuid, username, password)
    .setLoggingEnabled(true)
    .setCampaignActionTriggerEnabled(true)
    .setHeatmapGenerationEnabled(true)
    .setHeatmapReportingEnabled(true)
    .startInForegroundMode(context.getApplicationContext());
```

### Beacon messages
If your app must react to incoming beacon messages, you can register a ```BeaconMessageObserver``` instance by calling ```addBeaconMessageObserver```, as shown below. Take in mind, however, that your observer will only receive messages that were processed by one of the messages processing components, the ```RelutionIoTService``` uses. If you need to scan for specific beacon messages, use the ```BeaconMessageScanner``` class.
```java
RelutionIoTService.addBeaconMessageObserver(new RelutionIoTService.BeaconMessageObserver() {
  @Override
  public void onMessageReceived(BeaconMessage message) {
    // Do something with the message.
    if (message instanceof IBeaconMessage) {
      // Do something with the iBeacon message.
      IBeaconMessage iBeaconMessage = (IBeaconMessage) message;
    }
  }
});
```

### iBeacon calibration
To calibrate an iBeacon inside your app (like in the reference application), just place your device 1 meter away from the beacon
and send an averaged RSSI value of the iBeacon message to Relution by calling ```calibrateIBeacon```, as shown below:
```java
RelutionIoTService.addBeaconMessageObserver(new RelutionIoTService.BeaconMessageObserver() {
  @Override
  public void onMessageReceived(BeaconMessage message) {
    // Do something with the message.
    if (message instanceof IBeaconMessage) {
      // Get the iBeacon message.
      IBeaconMessage iBeaconMessage = (IBeaconMessage) message;
      // User moves to a place 1 meter away from the beacon that sends the iBeacon message...
      // Calibrate the iBeacon message.
      RelutionIoTService.calibrateIBeacon(iBeaconMessage.getIBeacon(), iBeaconMessage.getRssi());
    }
  }
});
```

### Beacon actions
If beacons take part in a campaign, your app should react to the campaign's actions. Your app will get instantly notified about each action execution, when an observer has been registered for each specific type of action. Take in mind, however, that the app will take about 10 seconds to recognize changes that were made in Relution (adding iBeacon or Relution Tag messages to beacons).
```java
RelutionIoTService.addBeaconNotificationActionObserver(new RelutionIoTService.BeaconNotificationActionObserver() {
  @Override
  public void onNotificationActionExecuted(BeaconNotificationAction notificationAction) {
    // Do something...
  }
});
RelutionIoTService.addBeaconContentActionObserver(new RelutionIoTService.BeaconContentActionObserver() {
  @Override
  public void onContentActionExecuted(BeaconContentAction contentAction) {
    // Do something...
  }
});
RelutionIoTService.addBeaconTagActionObserver(new RelutionIoTService.BeaconTagActionObserver() {
  @Override
  public void onTagActionExecuted(BeaconTagAction tagAction) {
    // Do something...
  }
});
```

### Relution tags
If you want to make use of Relution Tags, register a ```RelutionTagObserver``` to get informed about all received Relution Tags. If you need to have access to the name or description of a Relution Tag, just call ```getTagInfoForTag```:

```java
RelutionIoTService.addRelutionTagObserver(new RelutionIoTService.RelutionTagObserver() {
  @Override
  public void onTagReceived(long tag, RelutionTagMessage message) {
    try {
      RelutionIoTService.getTagInfoForTag(tag);
    } catch (RelutionTagInfoRegistry.RelutionTagInfoRegistryNoInfoFound relutionTagInfoRegistryNoInfoFound) {
      // ...
    }
  }
});
```

### Scanning
If you want to scan specific beacon messages, you can create an instance of ```BeaconMessageScanner``` and
configure it, so that the scanner will detect the messages. Next, register a ```BeaconMessageStreamNodeReceiver``` whose ```onReceivedMessage``` will be called, whenever a matching message has been received. You can also add message types to the scanner's configuration after you have started the scanner. To do this, just call the appropriate methods of the ```BeaconMessageScannerConfig``` object.

```java
final BeaconMessageScanner beaconScanner = new BeaconMessageScanner(this);
final BeaconMessageScannerConfig config = new BeaconMessageScannerConfig(beaconScanner);
config.scanIBeacon("b9407f30-f5f8-466e-aff9-25556b57fe6d", 45, 1);
config.scanIBeacon("c9407f30-f5f8-466e-aff9-25556b57fe6d", 46, 2);
config.scanRelutionTags(new long[]{13, 2});
config.scanJoinMeMessage();
beaconScanner.setConfig(config);
beaconScanner.addReceiver(new BeaconMessageStreamNodeReceiver() {
  @Override
  public void onMeshActive(BeaconMessageStreamNode senderNode) {
    Log.d(Config.projectName, "onMeshActive");
  }

  @Override
  public void onReceivedMessage(BeaconMessageStreamNode senderNode, BeaconMessage message) {
    Log.d(Config.projectName, "onBeaconUpdate");
    Log.d(Config.projectName, message.toString());
  }

  @Override
  public void onMeshInactive(BeaconMessageStreamNode senderNode) {
    Log.d(Config.projectName, "onMeshInactive");
  }
});
beaconScanner.startScanning();
```


### Advertising
To periodically send advertising messages, just call one of the ```start``` methods of the ```BeaconAdvertiser``` class:
```java
public class AdvertisingService extends BlueRangeService {
    @Override
    public void onStarted() {
        try {
            BeaconAdvertiser advertiser = new BeaconAdvertiser(this.getApplicationContext());
            advertiser.startAdvertisingDiscoveryMessage();
        } catch (PeripheralAdvertisingNotSupportedException e) {
            Log.d("Advertiser", "This device does not support advertising in peripheral mode!");
            e.printStackTrace();
        }
    }
}
```

## Questions
If you have any questions, please send a mail to m.ehrlich@mwaysolutions.com
