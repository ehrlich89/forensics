# Cordova BlueRange SDK Plugin

A Cordova plugin that makes the BlueRange SDK accessible for Cordova apps. Currently you can start scanning and get informed about the nearest beacons. Moreover, you can send advertising messages to the beacons to generate heatmap data.

## Using

### Create a new Cordova Project or use your existing project.

    $ cordova create hello com.example.helloapp Hello
    
### Install the plugin

    $ cd hello
    $ cordova plugin add <path_to_bluerange_sdk_plugin>
    

### Edit the `www/js/index.js` of your project. You can use the following code in the `onDeviceReady` function:

#### Bluetooth checks
```js
	// Checks if this device supports Bluetooth Low Energy.
	bluerange.isBluetoothLeSupported(function(enabled) {
		if (enabled === "true") {
			console.log("This device supports Bluetooth Low Energy.");
		} else {
			console.log("This device does not support Bluetooth Low Energy.");
		}
	});
	
	// Checks, if Bluetooth Low Energy is currently enabled.
	bluerange.isBluetoothLeEnabled(function(enabled) {
		if (enabled === "true") {
			console.log("Bluetooth is enabled");
		} else {
			console.log("Bluetooth is not enabled");
		}
	});
	
	// Checks, if Android location services is enabled.
	bluerange.isAndroidLocationServicesEnabled(function(enabled) {
		if (enabled === "true") {
			console.log("Location services are enabled!");
		} else {
			console.log("Location services are not enabled!");
		}
	});
```

#### Scanning
```js
	// Starts scanning beacons
	bluerange.startScanning(function(message) {
		console.log("Starting scanning successful.");
	}, function(message) {
		console.log("Starting scanning failed.");
	});

	// This callback will be triggered whenever a beacon is next to the mobile phone. 
	// Before your call this method, you must start scanning.
	// Currently the plugin triggers on one of the following iBeacon messages:
	// - UUID: 710ec796-bf43-4f93-bd25-b8468e65fcf1, Major: 1, Minor:10001
	// - UUID: 710ec796-bf43-4f93-bd25-b8468e65fcf1, Major: 1, Minor:10003
	// - UUID: 710ec796-bf43-4f93-bd25-b8468e65fcf1, Major: 1, Minor:10004
	// - UUID: 710ec796-bf43-4f93-bd25-b8468e65fcf1, Major: 1, Minor:10005
	// - UUID: 710ec796-bf43-4f93-bd25-b8468e65fcf1, Major: 1, Minor:10006
	// - UUID: 710ec796-bf43-4f93-bd25-b8468e65fcf1, Major: 1, Minor:10007
	// - UUID: 710ec796-bf43-4f93-bd25-b8468e65fcf1, Major: 1, Minor:10008
	// - UUID: 710ec796-bf43-4f93-bd25-b8468e65fcf1, Major: 1, Minor:10009
	// - UUID: 710ec796-bf43-4f93-bd25-b8468e65fcf1, Major: 1, Minor:10015
	// The minor identifier corresponds with the beacon ID passed to the Javascript callback. 
	// The callback will be triggered, when the mobile device has a distance 
	// of approximately 1 meter to the beacon.
	bluerange.addBeaconActiveListener(function(nodeId) {
		var message = "Beacon " + nodeId + " is active!";
		console.log(message);
	});

	// Will be called whenever a beacon has lost its state as being the nearest.
	bluerange.addBeaconInactiveListener(function(nodeId) {
		var message = "Beacon " + nodeId + " is inactive!";
		console.log(message);
	});

	// Stops scanning beacons.
	bluerange.stopScanning(function(message) {
		console.log("Stopping scanning successful.");
	}, function(message) {
		console.log("Stopping scanning failed.");
	});
```

#### Advertising
```js
	// Starts sending advertising messages to the beacons 
	// which generate the heatmap data. So, if advertising is
	// running, you should see the device on the heatmap in Relution.
	bluerange.startAdvertisingDiscoveryMessage(function(message) {
		console.log("Starting advertising succeeded.");
	}, function(message) {
		console.log("Starting advertising failed. " + message);
	});
	
	// Stops sending advertising messages to the beacons.
	bluerange.stopAdvertising(function(message) {
		console.log("Stopping advertising succeeded.");
	}, function(message) {
		console.log("Stopping advertising failed. " + message);
	});
```

### Add iOS or Android platform to your project.

    cordova platform add ios
    cordova platform add android
    
### Android: 
- Before you build and run, you must set the minSDK to 18 in the AndroidManifest.xml in your project's 'platforms/android' directory.

### iOS:
- After you have added the cordova plugin to your app, open the .xcodeproj under platforms->ios. Select a development team in the "Signing" section of your project. 
- Select your target, open the tab "General" and (remove and) add the BlueRangeSDK.framework to "Embedded binaries", which can be found in the plugin's "libs" folder.
- Moreover, make sure that under Build Settings -> Runpath Search Paths the value @executable_path/Frameworks is set.
- Furthermore, for iOS 10 devices, you need to add the NSBluetoothPeripheralUsageDescription property to the info.plist file.
- If the plugin is not correctly added to your project, please make sure that your config.xml contains the following xml element:
```xml
<feature name="BlueRangePlugin">
	<param name="ios-package" value="BlueRangePlugin" />
</feature>
```
    
### Run the code
    cordova build --device
    cordova run android --device
	cordova run ios --device

## More Info

For more information on setting up Cordova see [the documentation](http://cordova.apache.org/docs/en/4.0.0/guide_cli_index.md.html#The%20Command-Line%20Interface)

For more info on plugins see the [Plugin Development Guide](http://cordova.apache.org/docs/en/4.0.0/guide_hybrid_plugins_index.md.html#Plugin%20Development%20Guide)
