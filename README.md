# FruityMesh Android SDK
An Android SDK providing an API to develop applications that interact with BlueRange mesh beacons. The API is intended to provide an easy to use interface to develop time and location based applications that make use of a BlueRange mesh. For more information about BlueRange have a look at this project on github.

The library is still in an early development phase. Currently supported features are:
- Scanning for iBeacon messages and Relution Tag messages (RelutionTagMessageV1) in foreground and in background mode. Scanning in background mode works even if the app has been terminated by the user.
- Advertising arbitrary data as manufacturer specific data.

## Example
```java
BeaconScanner beaconScanner = BeaconScanner.getInstance(this);
BeaconScannerConfig config = new BeaconScannerConfig();
config.scanIBeacon("b9407f30-f5f8-466e-aff9-25556b57fe6d", 45, 1);
config.scanIBeacon("c9407f30-f5f8-466e-aff9-25556b57fe6d", 46, 2);
config.scanRelutionTags(new long[]{13, 2});
config.setBackgroundMode(true);
config.setLoggingEnabled(true);
beaconScanner.startScanning(config, KauflandBeaconScannerListener.class);
```
