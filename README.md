# Cordova BlueRange SDK Plugin

A Cordova plugin that makes the BlueRange SDK available for Cordova apps. The plugin allows checking Bluetooth availability, scanning for Relution SmartBeacons and broadcasting advertising messages collected by Relution SmartBeacons (necessary for computing a heatmap).

## Using

### Create a new Cordova Project or use your existing project.

    $ cordova create hello com.example.helloapp Hello
    
### Install the plugin

    $ cd hello
    $ cordova plugin add <path_to_bluerange_sdk_plugin> --save
    

### Sample code 

Edit the `www/js/index.js` of your project. The following functions can be used inside the `onDeviceReady` function. For more information, please check the `www/bluerange.js` in the plugin repository.

#### Bluetooth availability
```js
// Checks whether this device supports Bluetooth Low Energy.
bluerange.isBluetoothLeSupported(function(enabled) {
	if (enabled === "true") {
		console.log("This device supports Bluetooth Low Energy.");
	} else {
		console.log("This device does not support Bluetooth Low Energy.");
	}
});

// Checks whether Bluetooth Low Energy is enabled.
bluerange.isBluetoothLeEnabled(function(enabled) {
	if (enabled === "true") {
		console.log("Bluetooth is enabled");
	} else {
		console.log("Bluetooth is not enabled");
	}
});

// Checks whether the Location services are enabled on Android devices.
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
// Configure scanner
var scanOptions = {
	scanJoinMeMessages: true,
	scanPeriodInMs: 200,
	betweenScanPeriodInMs: 1
};

// Start scanner
bluerange.startScanning(scanOptions,
	function(sucessMessage) {
	console.log("Started scanner successfully.");
},  function(errorMessage) {
	console.log("Failed to start scanner.");
});

// Stop scanner
bluerange.stopScanning(
	function(successMessage) {
	console.log("Stopped scanner successfully.");
},  function(errorMessage) {
	console.log("Failed to stop scanner.");
});
}, 3000);

// Register callback
bluerange.addScannerCallback(function(message) {
	console.log("Received message: " + message);
});
```

#### Triggering
```js
	// Starts the beacon trigger
	bluerange.startBeaconTrigger(
		function(successMessage) {
			console.log("Beacon trigger start succeeded.");
	},  function(errorMessage) {
			console.log("Beacon trigger start failed.");
	});

	// Called when another beacon has become the nearest one.
	bluerange.addBeaconActiveListener(function(message) {

	});

	// Called when a beacon is not the nearest one anymore.
	bluerange.addBeaconInactiveListener(function(message) {

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

#### Enrollment
```js
// Sending enrollment info to beacon
var enrollmentInfo = {};
bluerange.sendEnrollmentToBeacon(enrollmentInfo,
	function(successMessage) {
		console.log("Beacon enrollment succeeded.");
	}, function(errorMessage) {
		console.log("Beacon enrollment failed.");
	}
);
```

#### Info
```js
// Get the SDK version as a string
bluerange.getVersion(function(versionName) {
	console.log("Version name = " + versionName);
});
```

For more information about the plugin API, please check the `bluerange.js`.

### Add iOS or Android platform to your project.

    cordova platform add ios
    cordova platform add android
    
### Android: 
- Before you build and run, you must set the minSDK to 18 in the AndroidManifest.xml in your project's 'platforms/android' directory.

### iOS:
1. After you have added the cordova plugin to your app, open the .xcodeproj under platforms->ios. Select a development team in the "Signing" section of your project. 
![Choose your Certificate](assets/ios/install/signing.png)
2. Select your target, open the tab "General" and (remove and) add the BlueRangeSDK.framework to "Embedded binaries", which can be found in the plugin's "libs" folder.
![Embedded Binaries](assets/ios/install/add-framework.png)
3. Moreover, make sure that under Build Settings -> Runpath Search Paths the value @executable_path/Frameworks is set.
![Build Settings -> Runpath](assets/ios/install/executable_path.png)
4. Furthermore, for iOS 10 devices, you need to add the NSBluetoothPeripheralUsageDescription property to the config.xml file.
```xml
<plugin name="com.mway.bluerange.android" spec="<path_to_bluerange_sdk_plugin>">
	<variable name="NSBluetoothPeripheralUsageDescription" value="My App Demo would use your bluetooth." />
</plugin>
```

5. If the plugin was not correctly added to your project, please make sure that your config.xml contains the following XML element:
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
