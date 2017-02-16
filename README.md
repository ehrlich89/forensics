# Cordova BlueRange SDK Plugin

## Using

### Create a new Cordova Project or use your existing project.

    $ cordova create hello com.example.helloapp Hello
    
### Install the plugin

    $ cd hello
    $ cordova plugin add <path_to_bluerange_sdk_plugin>
    

### Edit the `www/js/index.js` of your project and add the following code inside `onDeviceReady`.

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

	// Will be called whenever a new beacon has become the nearest.
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
- Select your target, open the tab "General" and (remove and) add the BlueRangeSDK.framework to "Embedded binaries", which can be found in the plugins "libs" folder.
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
