# BlueRange SDK for iOS
##What does this SDK do?
"BlueRange SDK for iOS" is an iOS SDK providing an API to develop applications that interact with BlueRange mesh beacons. The API is intended to provide an easy to use interface to develop time and location based applications that make use of a BlueRange mesh. For more information about BlueRange have a look at this project on github.

## Features
The SDK is still in an early development phase. Currently the BlueRange SDK supports Android 6.0 Marshmallow and iOS 8.0 devices. However, to enable advertising, Android devices must additionally support the Bluetooth LE peripheral mode. Currently supported features in the SDK for iOS are:
- Scanning iBeacon messages in foreground and background and Relution Tag messages (RelutionTagMessageV1) in foreground mode.
- Advertising arbitrary service UUIDs.

## Getting started
- In order to start with your own app, first you need to unzip the "BlueRangeSDK_iOS.zip".
- Next, start Xcode and import the project that is contained in the unpacked folder. To do this, just click on "Open another project" on the "Welcome to Xcode" screen and choose the BlueRange_SDK_iOS.xcodeproj contained inside the subfolder "bluerangesdk".
- After the project has been imported, just have a look at the project structure on the left side bar.
- As you can see, the "sdk" folder contains two subfolders called "examples" and "sdk".
- The "sdk" package contains the SDK's source code. So if you are interested in how we implemented the SDK components, you should have a closer look at the packages contained inside.
- In the "examples" folder you can see, how the SDK components can be used within your app code. The ```ExampleApplication```class contains some basic examples, of how the background scanning and advertising services can be started.
- The folder "nearyou" contains an example app, similar to the one contained in the Android SDK, displaying a set of products. Each product could in future be tagged with a specific Relution Tag. If the device is next to a beacon placed beside the product, the app receives the Relution tags and displays the products that are next to the user. You can test the app, if you have an iOS 8.0 device. In order to start the app, you only need to build, deploy and start the application by clicking on the "Run" button on the top left corner of Xcode. After the app has been built and deployed, you should see the example app on your device listing some sample products how it could look in an app using this SDK.
- To experiment with the SDK, you can have a look at the source code of the ```ExampleApplication``` which shows you how to use the core components of the SDK. In the BlueRange Android SDK you will find some more classes for the persistent logging of an incoming message stream and periodically sending status reports. In future we are going to implement these components for the iOS version of the BlueRange SDK, as well.

## Example
In the following section we show you, how to define a service that runs in background and listens to a specified set of beacons.
### Scanning
In order to start scanning mesh beacons, you only need to get the singleton instance of the ```BeaconScanner``` class, pass a ```BeaconScannerConfig``` object and call the ```startScanning``` method. To get informed about incoming beacon messages you have to pass a ```BeaconScannerListener``` object to the scanner.
```objective-c
#import "BeaconScanner.h"
#import "BeaconScannerConfig.h"
#import "MyBeaconScannerListener.h"

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
#import "BeaconScannerListener.h"
#import "MyBeaconScannerListener.h"
#import "BeaconMessage.h"
#import "IBeaconMessage.h"
#import "RelutionTagMessage.h"

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
The BlueRange SDK also supports broadcasting Bluetooth Low Energy advertising messages. To do that, you must include the UIBackgroundModes key with the bluetooth-peripheral value in the app's Info.plist. However, as described in the [Core Bluetooth Programming Guide](https://developer.apple.com/library/ios/documentation/NetworkingInternetWeb/Conceptual/CoreBluetooth_concepts/CoreBluetoothBackgroundProcessingForIOSApps/PerformingTasksWhileYourAppIsInTheBackground.html) you can only send a list of service UUIDs with the advertising messages. To do that, create an instance of the ```BeaconAdvertiser``` class and call the ```startAdvertisingWithServiceUUIDs``` method.
```objective-c
#import "BeaconAdvertiser.h"

- (void) testAdvertising
{
  self.beaconAdvertiser = [[BeaconAdvertiser alloc] init];
  [self.beaconAdvertiser startAdvertisingWithServiceUUIDs:@[
    @"EB6D9E77-6FA9-47A4-8174-889846FF9EAD",
    @"DB6D9E77-6FA9-47A4-8174-889846FF9EAD"]];
}
```
