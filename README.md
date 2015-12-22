# FruityMesh Android SDK
An Android SDK providing an API to develop applications that interact with BlueRange mesh beacons. The API is intended to provide an easy to use interface to develop time and location based applications that make use of a BlueRange mesh. For more information about BlueRange have a look at this project on github.

The library is still in an early development phase. Currently supported features are:
- Scanning for iBeacon messages and Relution Tag messages (RelutionTagMessageV1) in foreground and in background mode. Scanning in background mode works even if the app has been terminated by the user.
- Advertising arbitrary data as manufacturer specific data.

## Example
In order to start scanning you only need to get an instance of the ```BeaconScanner``` class, pass a ```BeaconScannerConfiguration``` object and call the ```startScanning``` method.
```java
private void testScanning() {
      try {
          BeaconScanner beaconScanner = BeaconScanner.getInstance(this);
          BeaconScannerConfig config = new BeaconScannerConfig();
          config.scanIBeacon("b9407f30-f5f8-466e-aff9-25556b57fe6d", 45, 1);
          config.scanIBeacon("c9407f30-f5f8-466e-aff9-25556b57fe6d", 46, 2);
          config.scanRelutionTags(new long[]{13, 2});
          config.setBackgroundMode(true);
          config.setLoggingEnabled(true);
          beaconScanner.startScanning(config, KauflandBeaconScannerListener.class);
      } catch (BluetoothDisabledException e) {
          e.printStackTrace();
      }
  }
```

The listener class:
```java
public class KauflandBeaconScannerListener implements BeaconScannerListener {

    private BeaconAdvertiser beaconAdvertiser;

    @Override
    public void onMeshActive(Context context) {
        Log.d(Config.projectName, "onMeshActive");
        try {
            beaconAdvertiser = new BeaconAdvertiser(context);
            beaconAdvertiser.startAdvertisingDiscoveryMessage();
        } catch (PeripheralAdvertisingNotSupportedException e) {
            e.printStackTrace();
        } catch (BluetoothDisabledException e) {
            e.printStackTrace();
        }
    }

    @Override
    public void onMeshInactive(Context context) {
        Log.d(Config.projectName, "onMeshInactive");
        beaconAdvertiser.stopAdvertising();
        // Print out the scan log.
        BeaconScanner beaconScanner = BeaconScanner.getInstance(context);
        BeaconScannerLog log = beaconScanner.getLog();
        log.print();
    }

    @Override
    public void onBeaconUpdate(Context context, List<BeaconMessage> beaconMessages) {
        Log.d(Config.projectName, "onBeaconUpdate");

        for (BeaconMessage beaconMessage : beaconMessages) {
            Beacon beacon = beaconMessage.getBeacon();
            Region region = beaconMessage.getRegion();
            if (beaconMessage.getType().equals("IBeaconMessage")) {
                IBeaconMessage message = (IBeaconMessage) beaconMessage;
                UUID uuid = message.getUUID();
                int major = message.getMajor();
                int minor = message.getMinor();
                Log.d("BeaconMessage", "iBeacon: UUID = " + uuid + ", major = " + major + ", minor = " + minor);
                // Do something
            } else if (beaconMessage.getType().equals("RelutionTagMessageV1")) {
                RelutionTagMessage message = (RelutionTagMessage) beaconMessage;
                List<Long> relutionTags = message.getTags();
                String outputString = "RelutionTagMessageV1: tags = ";
                for (int i = 0;i < relutionTags.size();i++) {
                    if (i != 0) {
                        outputString += ", ";
                    }
                    outputString += relutionTags.get(i);
                }
                outputString += ", txPower = " + beaconMessage.getTxPower();
                Log.d("BeaconMessage", outputString);
                // Do something else
            }
        }
    }

    @Override
    public void onError(Context context) {
        Log.d(Config.projectName, "onError");
    }
}
```
