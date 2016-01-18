# BlueRange iOS SDK
An iOS SDK providing an API to develop applications that interact with BlueRange mesh beacons. The API is intended to provide an easy to use interface to develop time and location based applications that make use of a BlueRange mesh. For more information about BlueRange have a look at this project on github.

The library is still in an early development phase. Currently supported features are:
- Scanning iBeacon messages in foreground and background and Relution Tag messages (RelutionTagMessageV1) in foreground mode.
- Advertising arbitrary service UUIDs.

## Example
### Scanning
In order to start scanning you only need to get the singleton instance of the ```BeaconScanner``` class, pass a ```BeaconScannerConfig``` object and call the ```startScanning``` method. To get informed about incoming beacon messages you have to pass a ```BeaconScannerListener``` object to the scanner.
```objective-c
BeaconScanner *beaconScanner = [BeaconScanner getInstance];
    BeaconScannerConfig *config = [[BeaconScannerConfig alloc] init];
    [config scanIBeacon:@"b9407f30-f5f8-466e-aff9-25556b57fe6d" major:45 minor:1];
    [config scanIBeacon:@"c9407f30-f5f8-466e-aff9-25556b57fe6d" major:46 minor:2];
    [config scanRelutionTags:[[NSArray alloc] initWithObjects:
                              [NSNumber numberWithLong:13], [NSNumber numberWithLong:2], nil]];
    MyBeaconScannerListener *listener = [[MyBeaconScannerListener alloc] init];
    [beaconScanner startScanningWithConfig: config AndListener: listener];
```

The handler methods ```onMeshActive```, ```onMeshInactive``` and ```onBeaconUpdate``` of the ```BeaconScannerListener``` are called by the scanner whenever messages arrive that match the format specified in the BeaconScannerConfig.
```objective-c
@implementation MyBeaconScannerListener

- (void) onMeshActive
{
    NSLog(@"onMeshActive");
}

- (void) onBeaconUpdate: (id<BeaconMessage>) beaconMessage
{
    if ([[beaconMessage getType] isEqualToString:@"IBeaconMessage"]) {
        [self handleIBeaconMessage: (IBeaconMessage*)beaconMessage];
    } else if ([[beaconMessage getType] isEqualToString:@"RelutionTagMessageV1"]) {
        [self handleRelutionTagMessage: (RelutionTagMessage*)beaconMessage];
    }
}

- (void) handleIBeaconMessage: (IBeaconMessage*) beaconMessage
{
    NSString *uuid = beaconMessage.uuid.UUIDString;
    int major = beaconMessage.major;
    int minor = beaconMessage.minor;
    NSLog(@"iBeacon: UUID = %@, major = %d, minor = %d", uuid, major, minor);
}

- (void) handleRelutionTagMessage: (RelutionTagMessage*) beaconMessage
{
    NSArray* tags = beaconMessage.tags;
    NSMutableString *outputString = [[NSMutableString alloc] initWithString:@"RelutionTagMessageV1: tags = "];
    for (int i = 0; i < [tags count];i++) {
        if (i != 0) {
            [outputString appendString:@", "];
        }
        [outputString appendString:[tags[i] stringValue]];
    }
    [outputString appendString:@", txPower = "];
    [outputString appendFormat:@"%d", beaconMessage.txPower];
    NSLog(@"%@", outputString);
}

- (void) onMeshInactive
{
    NSLog(@"onMeshInactive");
}

@end
```
### Advertising
The BlueRange SDK also supports broadcasting Bluetooth Low Energy advertising messages. To do that, you must include the UIBackgroundModes key with the bluetooth-peripheral value in the app's Info.plist. However, as described in the [Core Bluetooth Programming Guide](https://developer.apple.com/library/ios/documentation/NetworkingInternetWeb/Conceptual/CoreBluetooth_concepts/CoreBluetoothBackgroundProcessingForIOSApps/PerformingTasksWhileYourAppIsInTheBackground.html) it is only allowed to send a list of Service UUIDs. To send advertising messages containing a list of service UUIDs, create an instance of the ```BeaconAdvertiser``` class and call the ```startAdvertisingWithServiceUUIDs```.
```objective-c
- (void) testAdvertising
{
    self.beaconAdvertiser = [[BeaconAdvertiser alloc] init];
    [self.beaconAdvertiser startAdvertisingWithServiceUUIDs:@[
        @"EB6D9E77-6FA9-47A4-8174-889846FF9EAD",
        @"DB6D9E77-6FA9-47A4-8174-889846FF9EAD"]];
}
```
