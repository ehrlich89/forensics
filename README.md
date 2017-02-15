
# Cordova BlueRange SDK Plugin

## Using

### Create a new Cordova Project or use your existing project.

    $ cordova create hello com.example.helloapp Hello
    
### Install the plugin

    $ cd hello
    $ cordova plugin add <path_to_bluerange_sdk_plugin>
    

### Edit the `www/js/index.js` in your project and add the following code inside `onDeviceReady`.

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
	
	// Starts sending advertising messages to the beacons 
	// which generate the heatmap data.
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
- Before you run the code, you have to set the minSDK to 18 in the AndroidManifest.xml in your project's 'platforms/android directory'.

### iOS:
- After you have added the cordova plugin to your app, open the .xcodeproj under platforms->ios. Select your target, open the tab "General" and (remove and) add the BlueRangeSDK.framework to "Embedded binaries".
- Moreover, make sure that under Build Settings -> Runpath Search Paths the value @executable_path/Frameworks is set.
- If the plugin is not correctly added to your project, please make sure that your config.xml contains the following xml element:
```xml
<feature name="BlueRangePlugin">
	<param name="ios-package" value="BlueRangePlugin" />
</feature>
```
    
### Run the code
    cordova build
    cordova run android
	cordova run ios

## More Info

For more information on setting up Cordova see [the documentation](http://cordova.apache.org/docs/en/4.0.0/guide_cli_index.md.html#The%20Command-Line%20Interface)

For more info on plugins see the [Plugin Development Guide](http://cordova.apache.org/docs/en/4.0.0/guide_hybrid_plugins_index.md.html#Plugin%20Development%20Guide)
