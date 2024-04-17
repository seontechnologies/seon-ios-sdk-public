# Overview
Device fingerprinting can be used to stop fraudsters from attempting to hack, break into, or spam application as well as offer detailed insights into any customer that’s coming onto your iOS application. Applying the Device Fingerprinting module Account takeovers, multiple account signups and payments can easily be avoided.

To implement SEON SDK for iOS, follow the steps below.

## Requirements
- iOS 11.0 or higher
- _(optional)_ [Access WiFi Information entitlement](https://developer.apple.com/documentation/bundleresources/entitlements/com_apple_developer_networking_wifi-info) for `wifi_mac_address` and `wifi_ssid`
- _(optional)_ [Core Location permission](https://developer.apple.com/documentation/corelocation/) for `device_location`, `wifi_mac_address` and `wifi_ssid` (starting from iOS 13)

> __NOTE:__ If the listed permissions are not available for the application, the values collected using those permissions will be ignored. We recommend using as much permission as possible based on your use-case to provide reliable device fingerprint.

## Installation

The recommended approach for installing SeonSDK is via the [CocoaPods](http://cocoapods.org/)  package manager, as it provides flexible dependency management and simple installation.

#### Using CocoaPods

Install CocoaPods if not already available:

```
gem install cocoapods
```

To integrate SeonSDK into your Xcode project using CocoaPods, specify it in your Podfile:

```
pod 'SeonSDK', '~> 5.2.0'
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
**To enable SEON's geolocation feature on your account please reach out the customer success team to enable the functionality on your Admin page and your Scoring Engine!**

> __Note:__ Currently even if the integration has been done correctly there won't be a **device_location** field in the Fraud API response until the feature flag has been set by our customer success team.

#### Swift Integration
```
// Ask the user for appropriate location permission(s)

seonfp.setGeolocationEnabled(geolocationEnabled: true) // Must be explicitly enabled to turn on the Geolocation feature
seonfp.setGeolocationTimeout(timeoutMs: 3000)          // The timeout in milliseconds for the location service

```

#### Objective-C Integration
```
// Ask the user for appropriate location permission(s)

[[SEONFingerprint sharedManager] setGeolocationEnabled:YES];   // Must be explicitly enabled to turn on the Geolocation feature
[[SEONFingerprint sharedManager] setGeolocationTimeout:3000];  // The timeout in milliseconds for the location service

```


# Changelog
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
