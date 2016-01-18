# BlueRange SDK for Android
## What does this SDK do?
"BlueRange SDK for Android" is an Android SDK providing an API to develop applications that interact with BlueRange mesh beacons. The API is intended to provide an easy to use interface for the development of time and location based applications that make use of BlueRange meshes. For more information about BlueRange have a look at this project on github.

## Features
The SDK is still in an early development phase. Currently the BlueRange SDK supports Android 6.0 Marshmallow and iOS 8.0 devices. However, to enable advertising, Android devices must additionally support the Bluetooth LE peripheral mode.
At the moment the BlueRange SDK provides the following features:

### Scanning
- Scanning for iBeacon messages, Relution Tag messages (RelutionTagMessageV1) and JoinMeMessages. 
- The scanning procedure will be continued when the app is running in background mode, even when the user attempts to terminate the app.
- The energy consumption can be adjusted dynamically by changing properties like the scan interval.
- Messages can be saved persistently and postprocessed at a later time. The scan log is saved in an energy efficient way.
- Messages logged over a long period of time can be transformed to status reports and periodically sent to a mesh management system.

### Advertising
- Advertising arbitrary data and BlueRange specific messages in foreground and background mode.

### Getting started
- In order to start with your own app, first you need to unzip the "BlueRangeSDK_Android.zip".
- Next, start the Android IDE which you want to develop with (e.g. Android Studio) and import the project that is contained in the unpacked folder. To do this with Android Studio, just click on "Open an existing Android project" right after Android Studio has started and choose the unpacked folder.
- After the project has been imported, just have a look at the project structure on the left side bar.
- As you can see the "com.mway.bluerange.android" contains two subpackages called "examples" and "sdk".
- The "sdk" package contains the SDK's source code. So if you are interested in how we implemented the SDK components, you should have a closer look at the packages contained inside.
- In the "examples" package you can see how the SDK components can be used within your app code. The "systemtests" package contains some examples, of how the background scanning and advertising services can be started.
- The package "nearyou" contains an example app displaying the beacon messages that the device currently receives. You can test the app, if you have a Bluetooth Low Energy capable device. In order to start the app, you only need to build, deploy and start the application. If you are using Android Studio, you can simply use the keyboard shortcut "Shift+F10". The build process should take a while because our build management tool needs to resolve all dependencies. After the app has been build and deployed you should see the example app on your device listing all Beacon messages (iBeacons, Relution Tag messages and JoinMeMessages) that are currently received an conform to the beacon format as specified in the ```BeaconScanService``` class.
- The best way to start writing your own app would be to start with the NearYou app and modify the respective classes.


## Scanning and advertising
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
