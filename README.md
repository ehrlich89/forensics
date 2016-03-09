# BlueRange SDK for Android

## What does this SDK do?
"BlueRange SDK for Android" is a library for the Android platform providing an API to develop applications that interact with Bluetooth Low Energy beacons in general and BlueRange SmartBeacons in specific. The API is intended to provide an easy to use interface to develop time and location based applications that make use of BlueRange mesh networks. For more information about the BlueRange technology, have a closer look at this project on GitHub. 

The SDK also provides a seamless integration into an infrastructure based on the Enterprise Mobility Management platform Relution. Relution SmartBeacon Management is part of Relution and allows you to control and monitor a mesh of SmartBeacons. Using Relution you can assign each beacon a message that will be send out in regular intervals. At the moment Relution supports iBeacons and Relution Tag messages. 

The idea behind Relution Tags is to assign each beacon a number of tags that the beacon will send out periodically. A mobile app using this SDK scans for these tags and individually interprets the tags by e.g. executing an action, showing the user product information etc. Since the app itself is responsible for mapping each tag to a specific behavior, there is no need of continuous internet availability. Although the concept of Relution tags does not allow you to change the app's behavior dynamically, the platform allows you to exchange the tags of a beacon, which is not possible using the iBeacon technology. 

iBeacons, on the other side, are currently used to realize a concept, we call campaigns. A campaign allows you to define a set of actions that will be triggered by a mobile device, whenever the device is next to a beacon that takes part in the campaign. In contrast to the concept of Relution tags, the mapping of iBeacon messages and the campaign's actions is done by requesting Relution. When using campaigns inside the app, the app needs continuous internet access. However, in this case the content and behavior, that is associated with an action, will be instantly updated, whenever it will be changed.

## Features
Currently the BlueRange SDK supports Android 4.3 and iOS 8.0 devices. However, to enable advertising, Android devices must additionally support the Bluetooth LE peripheral mode and run at least on API level 21.
The BlueRange SDK is divided into two layers, a core and a service layer. The core layer consists of small message processing components that can be combined to a flexible message processing architecture. The service layer builds on top of the core layer and is responsible for the integration of the SDK components and the Relution platform. The current version supports the following features:

### Core
#### Advertising
- Sending advertising messages of arbitrary data.

#### Scanning
- Scanning iBeacon, Relution Tag and BlueRange specific messages. 
- The scan procedure will be continued when the app is running in background, even when the user attempts to terminate the app.
- The energy consumption can be adjusted dynamically by changing scan properties like the scan interval.

#### Logging
- Messages can be saved persistently and postprocessed at a later time. The log will be persisted with a minimum amount of energy consumption.

#### Reporting
- Messages logged over a long period of time can be transformed to status reports and periodically sent to a mesh management system like the Relution SmartBeacon management system.

#### Triggering
- A stream of beacon messages can trigger an action which will be executed based on different time and location based parameters. Actions can e.g. be executed after a predefined delay or locked for a specific duration. Moreover, actions can be defined to trigger only inside a certain range to the beacon or can have a validation period.
- To make the execution of distance dependent actions more stable, the messages' received signal strength is averaged over time by using a linearly weighted moving average filter.

### Service
As mentioned above, the service layer builds on top of the core layer and provides some high level features to integrate the message processing components with the Relution SmartBeacon Management platform. Concretely, it enables you
- to calibrate iBeacon messages to improve the precision of distance dependent actions.
- to map Relution Tags to their names and descriptions defined in the Relution platform.
- to periodically send specific advertising messages, that will be used to display a heatmap on the Relution SmartBeacon management platform.

## Documentation
- All currently supported features are bundled in the ```RelutionIoTService``` class. However, if you want more flexibility, you can build your app on top of the core and service components. To get an overview of the packages and classes, please have a look at our documentation in the ```/docs``` directory.

## Getting started
- In order to get started with your own app, you first need to unzip the "BlueRangeSDK_Android.zip".
- Next, start the Android IDE of your choise (e.g. Android Studio) and import the project contained in the unpacked folder. To do this with Android Studio, just click on "Open an existing Android project" right after Android Studio has been started and choose the unpacked folder.
- After the project has been imported, just have a look at the project structure on the left side bar.
- As you can see, the ```com.mway.bluerange.android``` package contains two subpackages ```examples``` and ```sdk```.
- The ```sdk``` package consists of the SDK's source code. So if you are interested in how the SDK components are implemented, you should have a closer look at these packages.
- In the ```examples``` package you can see how the SDK components can be used within your app. The ```systemtests``` package contains code examples that show you, how to use the SDK's core components.
- The ```nearyou``` package contains a reference application displaying the currently received beacon messages and the executed actions. You can test the app, if you have a Bluetooth Low Energy capable device. In order to start the app, you only need to build, deploy and start the application. If you are using Android Studio, you can simply use the keyboard shortcut ```Shift+F10```.
- The best way to start writing your own app would be to replace all classes of the NearYou app and transform the code to your own needs.

## Sample code
The following section shows you, how the most important SDK features can be integrated inside your app. As described above, you can use the ```RelutionIoTService``` service class, if you do not need the flexibility of the underlying message processing architecture and just want to get informed about executed actions, incoming messages or just want to turn on/off features.

### Configuration
The first thing you have to do is to add the ```RelutionIoTService``` service to your ```AndroidManifest.xml```. To start the service, you must call the ```startInForegroundMode``` or ```startInBackgroundMode``` method on an instance of this class. The minimum requirement, however, before starting the service, is to call the ```setConfig``` method, where you pass the URL, organization UUID and authentication data of your Relution organization. Moreover, you can enable/disable features by calling methods like ```setCampaignActionTriggerEnabled```. By default, all features are enabled. However, having all features turned on might decrease the performance of your application.

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
If you want to get informed about incoming messages, just register as an observer, as shown below:
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
and deliver an averaged RSSI to the Relution server by calling the ```calibrateIBeacon``` method, as shown below:
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
If actions have been assigned to beacons inside a campaign, your app should handle these actions by registering observers for each specific type of action:
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
If you want to implement your app based on Relution Tags, just register an observer to get informed about the tags of near beacons. If the name or description of the tags is important, you can obtain these informations by calling the ```getTagInfoForTag``` method:

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
If you just want to scan for specific beacon messages, you can create an instance of the ```BeaconMessageScanner``` class,
configure it and register a ```BeaconMessageStreamNodeReceiver``` to get informed about the incoming messages. You can also add message types to the scanner's configuration after you have started the scanner by calling the appropriate methods of the ```BeaconMessageScannerConfig``` object.

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
