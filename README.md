# BlueRange SDK for Android

[Image]

## What does this SDK do?
"BlueRange SDK for Android" is a library for the Android platform providing an API to develop applications that interact with Bluetooth Low Energy beacons in general and BlueRange SmartBeacons in specific. The API is intended to provide an easy to use interface to develop time and location based applications that make use of BlueRange mesh networks. For more information about the BlueRange technology, have a look at this project on github. The SDK also provides components to seamlessly integrate the mobile app into an environment based on the Enterprise Mobility Management platform Relution. The Relution SmartBeacon Management allows you to control and monitor a mesh of SmartBeacons and assign actions to these that will be triggered, when the mobile device is next to the beacons.

## Features
Currently the BlueRange SDK supports Android 4.3 and iOS 8.0 devices. However, to enable advertising, Android devices must additionally support the Bluetooth LE peripheral mode and run at least the API level 21.
At the moment the BlueRange SDK contains a core and a service layer. The core layer consists of small message processing components that can be combined to build a flexible message processing architecture. The service layer builds on top of the core layer and is responsible for the integration of the BlueRange SDK and the Relution platform. The current version supports the following features:

### Core
#### Advertising
- Advertising arbitrary data in foreground and background.

#### Scanning
- Scanning iBeacon, Relution Tag and BlueRange specific messages. 
- The scan procedure will be continued when the app is running in background, even when the user attempts to terminate the app.
- The energy consumption can be adjusted dynamically by changing scan properties like the scan interval.

#### Logging
- Messages can be saved persistently and postprocessed at a later time. The log will be persisted with a minimum amount of energy consumption.

#### Reporting
- Messages logged over a long period of time can be transformed to status reports and periodically sent to a mesh management system like the Relution SmartBeacon management system.

#### Triggering
- A stream of beacon messages can trigger an action which will be executed by considering different time and location based parameters. E.g. actions can be exectuted after a predefined delay or locked for a specific time. Moreover, actions can be defined to trigger only inside a certain distance range from the beacon or can have a validation period.
- To make the execution of distance dependent actions more stable, the received signal strength of beacon messages is averaged by using a linearly weighted moving average filter.

### Service
As mentioned above, the service layer builds on top of the core layer and provides some high level features that make use of the Relution platform.
- iBeacon messages can be calibrated to improve the precision of distance dependent actions.
- Relution Tags can be mapped to their names and description.
- It is possible to periodically send specific advertising messages to generate data, that can be used to display a heatmap on the Relution SmartBeacon management platform.

## Documentation
- All currently supported features are condensed in the RelutionIoTService class. However, if you want more flexibility, you can build your app on top of the classes contained inside the core and service packages. To get an overview of the classes, please have a look at the documentation [LINK].

## Getting started
- In order to get started with your own app, you first need to unzip the "BlueRangeSDK_Android.zip".
- Next, start the Android IDE of your choise (e.g. Android Studio) and import the project contained in the unpacked folder. To do this with Android Studio, just click on "Open an existing Android project" right after Android Studio has been started and choose the unpacked folder.
- After the project has been imported, just have a look at the project structure on the left side bar.
- As you can see the "com.mway.bluerange.android" package contains the two subpackages "examples" and "sdk".
- The "sdk" package consists of the SDK's source code. So if you are interested in how the SDK components are implemented, you should have a closer look at these packages.
- In the "examples" package you can see how the SDK components can be used within your app. The "systemtests" package contains code examples that show you, how to use the SDK's core components.
- The "nearyou" package contains a reference application displaying the currently received beacon messages and an overview of the actions and when they will be executed. You can test the app, if you have a Bluetooth Low Energy capable device. In order to start the app, you only need to build, deploy and start the application. If you are using Android Studio, you can simply use the keyboard shortcut "Shift+F10".
- The best way to start writing your own app would be to replace all classes of the NearYou app with your own code.

## Reference application

## Sample code
In the following section we show you, how to define a service that runs in background and listens to a specified set of beacons.
### Scanning beacon messages
In order to start scanning, you must create a class that inherits the ```BlueRangeService``` class. In the ```onStarted``` method
you can instantiate the beacon scanner and configure it, as shown below.
```java
public class ScanService extends BlueRangeService {
    @Override
    public void onStarted() {
        final BeaconScanner beaconScanner = new BeaconScanner(this);
        final BeaconScannerConfig config = new BeaconScannerConfig();
        config.scanIBeacon("b9407f30-f5f8-466e-aff9-25556b57fe6d", 45, 1);
        config.scanIBeacon("c9407f30-f5f8-466e-aff9-25556b57fe6d", 46, 2);
        config.scanRelutionTags(new long[]{13, 2});
        config.scanJoinMeMessage();
        beaconScanner.setConfiguration(config);
        beaconScanner.addListener(new BeaconScannerListener() {
            @Override
            public void onMeshActive() {
                Log.d(Config.projectName, "onMeshActive");
            }

            @Override
            public void onMeshInactive() {
                Log.d(Config.projectName, "onMeshInactive");
            }

            @Override
            public void onBeaconUpdate(List<BeaconMessage> beaconMessages) {
                Log.d(Config.projectName, "onBeaconUpdate");
                for (BeaconMessage beaconMessage : beaconMessages) {
                    Log.d("BeaconMessage", beaconMessage.toString());
                }
            }

            @Override
            public void onError() {

            }
        });
        beaconScanner.startScanning();
    }
}
```

To start the scanner in foreground or in background mode, just call the appropriate method in an area, where you have access to an Android ```Context```.
```java
private void testScanning() {
    new ScanService().startInForegroundMode(getApplicationContext());
    //new ScanService().startInBackgroundMode(getApplicationContext());
}
```

### Advertising
To advertise periodically, you just need to define another ```BlueRange``` service that starts advertising using a ```BeaconAdvertiser```:
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
