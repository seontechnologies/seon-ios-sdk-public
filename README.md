# Overview
Device fingerprinting can be used to stop fraudsters from attempting to hack, break into, or spam application as well as offer detailed insights into any customer that’s coming onto your iOS application. Applying the Device Fingerprinting module Account takeovers, multiple account signups and payments can easily be avoided. 

To implement SEON SDK for iOS, follow the steps below.

## Requirements
- iOS 11.0 or higher
- _(optional)_ [Access WiFi Information entitlement](https://developer.apple.com/documentation/bundleresources/entitlements/com_apple_developer_networking_wifi-info) for `wifi_mac_address` and `wifi_ssid`
- _(optional)_ [Core Location permission](https://developer.apple.com/documentation/corelocation/) for `wifi_mac_address` and `wifi_ssid` (starting from iOS 13)

> __NOTE:__ If the listed permissions are not available for the application, the values collected using those permissions will be ignored. We recommend using as much permission as possible based on your use-case to provide reliable device fingerprint.

## Installation

The recommended approach for installing SeonSDK is via the [CocoaPods](http://cocoapods.org/)  package manager, as it provides flexible dependency management and dead simple installation. 

#### Using CocoaPods

Install CocoaPods if not already available:

```
gem install cocoapods
```

To integrate SeonSDK into your Xcode project using CocoaPods, specify it in your Podfile:

```
pod 'SeonSDK'
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
@import SeonSDK
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
seonfp.setLoggingEnabled(true)

// Set session_id
seonfp.sessionId = "CUSTOM_SESSION_ID"

/* INVOCATION */
// Compute fingerprint asynchronously
seonfp.getFingerprintBase64 { fingerprint, error in
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
   getFingerprintBase64:^(NSString *fingerPrint, NSError *error) {
      if (error == nil){
        //set seonFingerprint as the value for the session 
        //property of your Fraud API request.
      } else{
        // Handle error 
      }
  }];

```

# Changelog

## 5.0.0
### Important Integration changes

- #### Starting from v5 there is a change in SEON’s API Policy. From now on SEON might introduce new fields in the SDK with minor versions. We advise you to integrate in a way that addition of new fields is handled gracefully.

- #### `device_hash` field is calculated differently, resulting in different values for a given device. This means these values are going to break between versions.

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
