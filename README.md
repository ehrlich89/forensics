# BlueRange SDK for Android
An Android SDK providing an API to develop applications that interact with BlueRange mesh beacons. The API is intended to provide an easy to use interface for the development of time and location based applications that make use of BlueRange meshes. For more information about BlueRange have a look at this project on github.

## Features
The library is still in an early development phase. Currently supported features are:

### Scanning
- Scanning for iBeacon messages, Relution Tag messages (RelutionTagMessageV1) and JoinMeMessages. 
- The scanning procedure will be continued when the app is running in background mode, even when the user attempts to terminate the app.
- The energy consumption can be adjusted dynamically by changing properties like the scan interval.
- Messages can be saved persistently and postprocessed at a later time. The scan log is saved in an energy efficient way.
- Messages logged over a long period of time can be transformed to status reports and periodically sent to a mesh management system.

### Advertising
- Advertising arbitrary data and BlueRange specific messages in foreground and background mode.

## Example
In order to start scanning you only need to get the singleton instance of the ```BeaconScanner``` class, pass a ```BeaconScannerConfiguration``` object and call the ```startScanning``` method. To get informed about incoming beacon messages you have to pass the class object of a ```BeaconScannerListener```. Passing the class object is necessary to support background scanning after app termination.

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
