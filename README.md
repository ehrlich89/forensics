
# BlueRange SDK

## Overview
The BlueRange SDK is a library for Android and iOS that can be used to write mobile apps that interact with beacons.
The SDK is specifically designed to interact with beacons managed by the Relution IoT platform. This beacon management platform allows you to monitor, update and control beacons that run the FruityMesh firmware. Beacons that use FruityMesh build up a robust network and can be managed and updated a lot easier.

## Features
Currently the BlueRange SDK supports iOS devices that run at least on iOS 8.0 and Android devices with at least API level 4.3. However, to enable advertising, Android devices must additionally support the Bluetooth LE peripheral mode and run at least on API level 21.

The BlueRange SDK has two layers, a core and a service layer.
The core layer contains a set of components used to process streams of beacon messages like iBeacon or Eddystone messages. These message processing components can be combined to a flexible event driven architecture. 
The service layer builds on top of the core layer and integrates the message processing components to realize all services of the Relution IoT system. 
Currently the following features are supported:

### Core
#### Advertising
- Sending BLE advertising messages of arbitrary data.

#### Scanning
- Scanning beacon messages of different beacon types:
    - iBeacon message: Apple's standardized iBeacon format for BLE advertising which contains an identifier triple (UUID, major and minor).
    - Eddystone UID message: One of Google's standardized BLE beacon formats which consists of a 10-byte namespace UID and a 6-byte instance identifier.
    - Eddystone URL message: Another Eddystone beacon format that contains a URL.
    - Join Me message: FruityMesh beacons broadcast these messages to establish a beacon network. Each packet holds the beacon id, its connectivity and some other useful information that can be used to analyze the mesh.
    - Relution Tag message: An advertising message format only supported by the Relution beacon management platform. It contains a list of tags that can be mapped to app specific actions in offline scenarios.
- The scan procedure will be continued when the app is running in background, even when the user attempts to terminate the app.
- The energy consumption can be adjusted dynamically by changing scan properties like the scan interval.

#### Logging
- Messages can be saved persistently and postprocessed at a later time. The log will be persisted while consuming a minimum amount of energy.

#### Reporting
- Messages logged over a long period of time can be transformed to status reports and periodically sent to a beacon management platform. A webservice e.g. can use these reports to estimate the device's position and generate a heatmap.

#### Aggregating
- A sequence of identical becon messages can be aggregated to average the RSSI. The averaging algorithm uses a linearly weighted moving average filter.

#### Triggering
- A stream of beacon messages can trigger actions based on different time and location based parameters. Currently supported action parameters are:
  - Action delay: Actions will be executed after a predefined delay.
  - Action lock: Actions will not be executed for a specific time after they have been triggered.
  - Activation distance: Actions will only be triggered, if the device has a distance below a predefined threshold.
  - Validation period: An action can have a start and end validation time.

### Service
The service layer builds on top of the core layer and provides some high level services used to integrate your app with Relution. Specifically, it enables you
- to calibrate beacons to improve the beacon distance estimation.
- to execute actions defined in the Campaigns section of Relution.
- to send heatmap reports to Relution.

## API reference
- All currently supported features are combined in the ```RelutionIoTService``` class. However, if you want to build your app independently from Relution, you can build on top of the core components. To get an overview of the SDK's packages and classes, please have a look at the documentation in the ```/docs``` directory.

## Getting started
### Android
#### AAR import
- The easiest way to import the BlueRangeSDK is to add a dependendency to your project's build.gradle file, after you have imported the bluerangesdk-release.aar which you can find under the path bluerangesdk/build/outputs/aar.
```gradle
dependencies {
    compile project(':bluerangesdk')
}
```

#### Manual import
- Start the Android IDE of your choise (e.g. Android Studio) and import the project contained in the unpacked folder. To do this with Android Studio, just click on "Open an existing Android project" right after Android Studio has been started and choose the unpacked folder.
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
The following section shows you the most important SDK features.

### Relution IoT Services
#### Service configuration
If your app needs a full integration with Relution, just start the ```RelutionIoTService```. As shown below, you first have to login with your Relution account. Before you start the service you can turn on/off the features your app needs. Currently the RelutionIoTService supports the following features:
- Campaigns: Turn on this feature, if you want to get informed about executed actions that you defined in Relution.
- Analytics: Turn on this feature, if you want the SDK to periodically send reports to Relution, which can be used to generate heatmaps.
- Heatmap: Turn on this feature, if you want the beacons to estimate the number of devices and generate a heatmap from out of this information.
- Logging: Turn logging on, if you want the SDK to log to the console. Turn this off to increase the performance.

```java
String baseUrl = "https://iot.relution.io";
String username = "your_username";
String password = "your_password";

RelutionIoTService.LoginObserver observer = new RelutionIoTService.LoginObserver() {
    @Override
    public void onLoginSucceeded() {

    }

    @Override
    public void onLoginFailed() {

    }

    @Override
    public void onRelutionError() {

    }
};

new RelutionIoTService()
    .setLoginData(baseUrl, username, password, observer)
    .setLoggingEnabled(true)
    .setCampaignActionTriggerEnabled(true)
    .setHeatmapGenerationEnabled(true)
    .setHeatmapReportingEnabled(true)
    .startAsThread(context.getApplicationContext());
```

#### Relution beacon calibration
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

#### Relution campaigns
Get informed about triggered actions by registering the appropriate listeners:

```java
// Get informed about to campaign actions.
RelutionIoTService.addBeaconNotificationActionObserver(new RelutionIoTService.BeaconNotificationActionObserver() {
    @Override
    public void onNotificationActionExecuted(RelutionNotificationAction notificationAction) {
        // Do something...
    }
});
RelutionIoTService.addBeaconContentActionObserver(new RelutionIoTService.BeaconContentActionObserver() {
    @Override
    public void onContentActionExecuted(RelutionContentAction contentAction) {
        // Do something...
    }
});
RelutionIoTService.addBeaconTagActionObserver(new RelutionIoTService.BeaconTagActionObserver() {
    @Override
    public void onTagActionExecuted(RelutionTagAction tagAction) {
        // Do something...
    }
});
```

#### Relution tags
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

### Core
#### Scanning
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


#### Advertising
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
