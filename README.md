# Overview
Device Intelligence helps prevent fraudsters from hacking, breaching, or spamming your application while providing detailed insights into every customer accessing your iOS app. By integrating the Device Intelligence module, you can effectively safeguard against account takeovers, multiple account signups, and fraudulent payments.

To implement SEON SDK for iOS, follow the steps below.

## Known Issues
If you're using Swift Package Manager with an SDK version ranging from `5.2.0`-`5.4.2` and you encounter an error related to the revision id not matching the previously recorded value, then you should delete the Swift security fingerprint cache for this library.

You should delete the `seon-ios-sdk-swift-package-{commit-hash}.json` file located at:
 `~/Library/org.swift.swiftpm/security/fingerprints`

You could also delete the caches if needed:

`rm -rf ~/Library/Caches/org.swift.swiftpm`

Or you can delete every fingerprint (not advised):

`rm -rf ~/Library/org.swift.swiftpm/security/fingerprints`

## Requirements
- iOS 12.0 or higher
- _(optional)_ [Access WiFi Information entitlement](https://developer.apple.com/documentation/bundleresources/entitlements/com_apple_developer_networking_wifi-info) for `wifi_mac_address` and `wifi_ssid`
- _(optional)_ [Core Location permission](https://developer.apple.com/documentation/corelocation/) for `device_location`, `wifi_mac_address` and `wifi_ssid` (starting from iOS 13)

> __NOTE:__ If the listed permissions are not available for the application, the values collected using those permissions will be ignored. We recommend using as much permission as possible based on your use-case to provide reliable Device Intelligence.

## Installation

The recommended approach for installing SeonSDK is via the [CocoaPods](http://cocoapods.org/)  package manager, as it provides flexible dependency management and simple installation.

#### Using CocoaPods

Install CocoaPods if not already available:

```
gem install cocoapods
```

To integrate SeonSDK into your Xcode project using CocoaPods, specify it in your Podfile:

```
pod 'SeonSDK', '~> 5.5.0'
```

Then you can use install as usual:

```
pod install
```

#### Using Swift package manager

URL for the repository: https://github.com/seontechnologies/seon-ios-sdk-swift-package


## Integration

#### Swift Integration

###### Setup

To be able to use the SDK in projects written in Swift you should add the use_frameworks    ! attribute to the Podfile:

```
use_frameworks!
```
After that the SDK can be imported like any other library:
```
import SeonSDK
```
 Alternatively a bridging header file can be used importing the SDK there:
```
#import <SeonSDK/SEONFingerprint.h>
```

If you opt to use a bridging header, the path to the header must be set in Build Settings -> Objective-C Bridging Header

###### Integration

```
/* CONFIGURATION */

let seonfp = SEONFingerprint()

// Enable logging
seonfp.setLoggingEnabled(loggingEnabled: true)

// Set session_id
seonfp.sessionId = "CUSTOM_SESSION_ID"

/* INVOCATION */
// Compute fingerprint asynchronously
seonfp.getFingerprintBase64 { seonFingerprint, error in
      if let error{
        // Handle the error
      } else{
        //set seonFingerprint as the value for the session
        //property of your Fraud API request.
      }
}

```

#### Objective-C Integration

```
...
@import SeonSDK;
...
```

```


/* CONFIGURATION */

// Enable logging
[[SEONFingerprint sharedManager] setLoggingEnabled:true]

// Set session_id
[[SEONFingerprint sharedManager] setSessionId:@"[CUSTOM_SESSION_ID]"];

/* INVOCATION */
// Compute fingerprint asynchronously
[[SEONFingerprint sharedManager]
   getFingerprintBase64:^(NSString *seonFingerprint, NSError *error) {
      if (error == nil){
        //set seonFingerprint as the value for the session
        //property of your Fraud API request.
      } else{
        // Handle error
      }
  }];

```

# Geolocation Integration (Opt-in)
**To enable SEON's geolocation feature on your account for Fraud API requests, please reach out to the customer success team to enable the functionality on your Admin page and your Scoring Engine!**
> __Note:__ Currently even if the integration has been done correctly there won't be a **device_location** field in the Fraud API response until the feature flag has been set by our customer success team.

> __Note:__ iOS SDK Version 5.5.0 or higher and the following Geolocation integration setup is required to use the Geofence API. For further information please visit https://docs.seon.io/api-reference/geofence-api
### Important: Collecting consent and necessary permissions from the end user for location tracking is required.

Use the `SEONGeolocationfig` object the customise how the geolocation is collected or just use the instance as-is for default values. The following properties are available on object:
- `geolocationEnabled` - Explicit property to set geolocation collection. Defaults to true/YES.
- `prefetchEnabled` - By setting it true the geolocation service is going to pre-fetch a valid location as soon as the `SEONFingerprint` object is created. Defaults to false/NO.
- `maxGeolocationCacheAgeSec` - Sets the maximum allowed age of a location object in seconds. Default value is 60.
- `geoLocationServiceTimeoutMs` - Sets the maximum time in milliseconds `getFingerprintBase64` or `stopBehaviourMonitoring` can wait for a valid location. Default value is 3000.

Make sure you call `setGeolocationConfig` on `SEONFingerprint` before calling `getFingerprintBase64` or `stopBehaviourMonitoring` for the SDK to enrich the collected device information with location data.
For the most accurate results when using the Geofence API, prefer Behaviour Monitoring over simply calling `getFingerprintBase64`, see the relevant section in the documentation about how to set up and use Behaviour Monitoring.

The following Geolocation specific error codes can occur:
- `1008` : `Authorization Error - Permission Denied` : The user has denied the use of location services for the app or they have disabled it globally in the Settings.
- `1009` : `Authorization Error - Not Determined` : The user has not chosen whether the app can use location services.
- `1010` : `Authorization Error - Restricted` : The app is not authorized to use location services due to active restrictions.
- `1011` : `Location Error - No Location` : Failed to get location for device.
- `1012` : `Location Error - Timeout` : The location service has timed out.
- `1013` : `Location Error - Location Services Disabled` : The user has disabled location services by toggling the Location Services switch off in privacy settings.

Please note that if either of the previously listed Geolocation errors occur, the **SDK should still return a valid fingerprint object** which can be used as the session property for the regular Fraud API requests if needed.
#### Swift Integration
```
// Prompt the user for appropriate location permission(s)
// ...

// Customize how geolocation is collected by modifying the default values of `SEONGeolocationConfig`
let config = SEONGeolocationConfig() // creates a config object with the default values
config.geolocationEnabled = true      // Enable the Geolocation feature
config.prefetchEnabled = false        // Disable pre-fetching a valid location
config.geolocationServiceTimeoutMs = 3000 // Timeout for the location service in milliseconds
config.maxGeoLocationCacheAgeSec = 60    // Maximum allowed age of a location object in seconds

// Set the geolocation config for the SEONFingerprint object
SEONFingerprint.sharedManager().setGeolocationConfig(config)

// Simply call getFingerprintBase64 with location data enriched
SEONFingerprint.sharedManager().getFingerprintBase64 { fingerPrint, error in
    if let error = error {
        // Handle any errors
        print("Error fetching fingerprint: \(error.localizedDescription)")
    } else if let fingerPrint = fingerPrint {
        // Use the received fingerprint value in your session property for your Fraud API request
        print("Received fingerprint: \(fingerPrint)")
    }
}
```
#### Objective-C Integration
```
// Prompt the user for appropriate location permission(s)
// ...

// Customise how geolocation is collected by modifying the default values of `SEONGeolocationConfig`
SEONGeolocationConfig *config = [[SEONGeolocationConfig alloc]init]; // creates a config object with the default values.
config.geolocationEnabled = YES; // Enable the Geolocation feature.
config.prefetchEnabled = NO; // Pre-fetch a valid location
config.geolocationServiceTimeoutMs = 3000; // Timeout for the location service.
config.maxGeoLocationCacheAgeSec = 60; // Maximum allowed age of a location object in seconds.

// Set the geolocation config for the SEONFingerprint object.
[[SEONFingerprint sharedManager] setGeolocationConfig:config];

// Simply call getFingerprintBase64 with location data enriched.
[[SEONFingerprint sharedManager] getFingerprintBase64:^(NSString *fingerPrint, NSError *error) {
  if (error != nil) {
        // Handle any errors
    } else {
        // Use the received fingeprint value in your session property for your Geofence API or Fraud API request.
    }
}];

```
# Behaviour Monitoring (Optional)
behaviour Monitoring allows the SEON SDK to be able to detect potentially suspicious user behaviour on the device. The SDK collects data during the session, which is then analyzed to identify potentially fraudulent environments and actions. This feature enhances the SDK’s ability to prevent fraud by detecting various forms of automated or suspicious activity, such as bot usage or device farms.

>__Note:__ For the result of the behaviour evaluation, we are introducing a new response field in the Fraud API response named `suspicious_flags`. It's available in sessions generated by iOS SDK version 5.4.0 or later when the session had been generated by the new `startBehaviourMonitoring` and `stopBehaviourMonitoring` interfaces.

The monitoring should be started with calling `startBehaviourMonitoring` wherever you would like to detect suspicious activity in your application and should be stopped with `stopBehaviourMonitoring` whenever it's reasonable. The returned session string should be then used in a Fraud API request as usual.

### Possible `suspicious_flags` values:
-	`"possible_automation"`: Suggests that automation tools or scripts may be controlling the device.
-	`"possible_device_farm"`: Suggests that the device might be part of a device farm used for fraudulent activities.
-	`"possible_vishing"`: Flags possible vishing (voice phishing) activity, where the user might be coerced into providing sensitive information.
- **To be continously improved and extended with new signals**

#### Swift Integration
```
// Start behaviour monitoring
do {
    try seonfp.startBehaviourMonitoring()
} catch let error {
    // Handle potential errors
    print("Failed to start behaviour monitoring: \(error.localizedDescription)")
}

// Stop behaviour monitoring and get the fingerprint session result to be sent in
// with the Fraud API request
seonfp.stopBehaviourMonitoring { resultWithFingerprint, error in
    if let error = error {
        // Handle any errors
        print("Error stopping behaviour monitoring: \(error.localizedDescription)")
    } else {
        // Use resultWithFingerprint in your Fraud API request
        print("behaviour monitoring fingerprint: \(resultWithFingerprint)")
    }
}
```

#### Objective-C Integration
```
// Start behaviour monitoring
NSError *error = [[SEONFingerprint sharedManager] startBehaviourMonitoring];
if (error) {
    // Handle potential errors
    NSLog(@"Failed to start behaviour monitoring: %@", error.localizedDescription);
}

// Stop behaviour monitoring and get the fingerprint session result to be sent in
// with the Fraud API request
[[SEONFingerprint sharedManager] stopBehaviourMonitoring:^(NSString *resultWithFingerprint, NSError *error) {
    if (error == nil) {
        // Use resultWithFingerprint in your Fraud API request
        NSLog(@"behaviour monitoring fingerprint: %@", resultWithFingerprint);
    } else {
        // Handle any errors
        NSLog(@"Error stopping behaviour monitoring: %@", error.localizedDescription);
    }
}];
```
# Changelog
## 5.5.0
>IMPORTANT: This version is going to introduce a new Fraud API response field named `true_device_id`.
- Compatibiity with SEON's Geofence API.
- Improved Geolocation collection.
- Extended the SDK's error object with Geolocation specific errors.
- Added `SEONGeolocationConfig` object and a corresponding setter to the SDK's interface with the following properties:
  - `geolocationEnabled` - Explicit property to set geolocation collection. Defaults to true/YES.
  - `prefetchEnabled` - By setting it true the geolocation service is going to pre-fetch a valid location as soon as the `SEONFingerprint` object is created. Defaults to false/NO.
  - `maxGeolocationCacheAgeSec` - Sets the maximum allowed age of a location object in seconds. Default value is 60.
  - `geoLocationServiceTimeoutMs` - Sets the maximum time in milliseconds `getFingerprintBase64` or `stopBehaviourMonitoring` can wait for a valid location. Default value is 3000.
- Deprecated `setGeolocationTimeout` in favour of `setGeolocationConfig`, `geoLocationServiceTimeoutMs` should be set on the config object instead.
- Improved thread safety.
- Minor fixes and improvements.

## 5.4.2
- Improved thread management.
- Internal changes for React Native support.

## 5.4.1
- Fix invalid error message when calling `startBehaviourMonitoring`

## 5.4.0
-	Added Behaviour Monitoring feature, which allows detection of suspicious device behaviour.
-	New `suspicious_flags` field in the Fraud API response. Possible values include:
    -	`"possible_automation"`: Indicates potential automation tool or script usage.
    -	`"possible_device_farm"`: Suggests the device might be part of a device farm.
    -	`"possible_vishing"`: Flags possible vishing activity.
-	Added public interfaces for behaviour monitoring:
    -	Swift:
        -	startBehaviourMonitoring()
        -	stopBehaviourMonitoring(completionHandler: @escaping (String?, Error?) -> Void)
    -	Objective-C:
        -	-(NSError*)startBehaviourMonitoring;
        -	-(void)stopBehaviourMonitoring:(void(^)(NSString* resultWithFingerprint,NSError* error))completionHandler;
- Fixed potential memory leak.
- Fixed occasional "(null)" values in `proxy_address` when both proxy and vpn is enabled.
-	Internal improvements and changes for upcoming features.

## 5.3.0
- Added VPN detection logic and the related response field:
  - `vpn_state` Returns the vpn connection state of the device. The possible return values are:
    - `"UNKNOWN"`
    - `"CONNECTED"`
    - `"NOT_CONNECTED"`
- Added proxy detection logic and the following related response fields:
  - `proxy_state` Returns the proxy connection state of the device. The possible return values are:
    - `"UNKNOWN"`
    - `"CONNECTED"`
    - `"NOT_CONNECTED"`
  - `proxy_address` Returns a String value of the connected proxy's host address followed by the port. The value can be null if no proxy connection has been found. Example value: `"111.11.11.11:8008"`
  - Internal changes for upcoming features.
## 5.2.0
- Added GeoLocation feature, the SDK now optionally can retrieve the device's location. See the documentation about how to use it.
- Internal performance improvements and changes for upcoming features
- Fixed rare threading related issue
- Added codesign signature to XCFramework binary to attest to the validitiy of the SDK
- Fixed test coverage reports not showing up when the SDK is linked
- Raised minimum deployment target from iOS 11.0 to iOS 12.0

### New response fields
- `device_location` Contains additional device location datapoints. Refer documentation for details. _This is currently an opt-in feature_

## 5.1.1
- Fix rare failure on devices with Auto Proxy configuration.
- Fix test coverage report generation in XCode.
- Internal improvements.
## 5.1.0
- #### Significantly improved the uniqueness and stability of the `device_hash` property. With the current change the value should persist through app reinstalls.
>Note: This is NOT a breaking change for the `device_hash` property. The value only changes for a small subset of devices, where the `device_hash` wasn't unique previously.

### Bugfixes
- Fix `system_uptime` to correctly return the elapsed time in seconds since the last cold boot instead of the unix timestamp.
### Other
- Internal improvements.
- Internal changes for upcoming features.
## 5.0.0
### Important Integration changes

- #### Starting from v5 there is a change in SEON’s API Policy. From now on SEON might introduce new fields in the SDK with minor versions. We advise you to integrate in a way that addition of new fields is handled gracefully.

- #### `device_hash` field is calculated differently, resulting in different values for a given device. This means these values are going to break between versions.
- #### Minimum deployment target has been raised to iOS 11.0
- #### SeonFingerprint class has been renamed to SEONFingerprint
- #### fingerprintBase64With has been renamed to getFingerprintBase64 and changed to return the fingerprint asynchronously. Thread management is fully handled by the SDK internally and doesn’t require any extra effort from the integration side.
- #### getFingerprintBase64’s completion handler now returns an NSError object which contains details about possible integration and runtime errors. For now the following errors are forwarded by the SDK:
  - `SEONErrorInvalidSessionID`
  - `SEONErrorFingerprintFailed`

### New features and improvements
  - Introducing screen capturing detection
  - Introducing call status detection
  - Introducing various new response fields, listed below
  - General performance improvements
  - Improved stability of device hash
  - Improved error handling
  - Added PrivacyInfo manifest

### New response fields

- `is_biometrics_enabled` Flags whether biometrics on the phone are enabled or not. _This will help determining the end user’s security awareness._
- `is_passcode_enabled` Flags whether a passcode is enabled or not on the phone. _This will help determining the end user’s security awareness._
- `is_ios_app_on_mac` Flags when the host process is an iOS app running on a Mac. The value of the property is true for apps built using Mac Catalyst.
- `is_on_call` Flags if the phone is on a call during the transaction. _High value security information which can be tied to fraud._
- `is_screen_captured` Flags if the phone’s screen is captured during the transaction. _High value security information which can be tied to fraud._
- `can_send_mail` Flags if the phone is set up for email sending.
- `can_send_text` Flags if the phone is set up for text sending. _Information whether the device is set up for use properly, false values for either is suspicious if the device is a phone._
- `timezone_identifier` Returns the current system time zone’s geopolitical region ID. _Eg.: `Europe/Budapest`_

### Removed response fields
#### The following fields are no longer collected and removed from the Fraud API response to comply with Apple’s required reason API policy:
- `free_storage`
- `total_storage`
- `last_boot_time`
- `carrier_name`
- `carrier_country`

### Bugfixes

- Added class name prefixes to avoid collision with other frameworks
- Fixed minor bug where fingerprint generation could cause cuts in audio playback
- Added missing network configurations
- Added missing devices from `device_name`'s output

### Other

- Internal changes to prepare for upcoming features and improvements

## 4.0.0
- Changed fingerprint method to be async, improving speed and reliability
- device_ip fields are now available
- Performance improvements

## 3.0.8
- Swift integration improvements

## 3.0.7
- Minor fixes and integration improvements

## 3.0.6
- XCFramework support

## 3.0.1
- iOS 14 compatibility
- Stability and performance improvements

## 3.0.0
- Removed background HTTP request for data transmission, the SDK returns an encrypted, base64 encoded string to use with SEON's REST API
- Removed public key support
- Bugfixes and security improvements

## 2.1.2
- iOS 14 compatibility

## 2.1.1
- Stability and compatibility improvements

## 2.1.0
- Bugfixes and security improvements

## 2.0.4
- Bugfixes and performance improvements

## 2.0.1
- Bugfixes and performance improvements

## 2.0.0
- `startAnalyzingWithSession` method has been removed
- Added `scanFingerprint` method
- Added public key support
- Enhanced logging settings
- Bugfixes and performance improvements

## 1.0.9
- Fix bug related to enabled proximity sensor

## 1.0.8
- Bugfixes and performance improvements

## 1.0.7
- First stable build
