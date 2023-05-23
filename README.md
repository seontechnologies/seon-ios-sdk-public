# Overview
Device fingerprinting can be used to stop fraudsters from attempting to hack, break into, or spam application as well as offer detailed insights into any customer that’s coming onto your iOS application. Applying the Device Fingerprinting module Account takeovers, multiple account signups and payments can easily be avoided. 

To implement SEON SDK for iOS, follow the steps below.

## Requirements
- iOS 9.0 or higher
- _(optional)_ [Access WiFi Information entitlement](https://developer.apple.com/documentation/bundleresources/entitlements/com_apple_developer_networking_wifi-info) for wifi_mac_address and wifi_ssid
- _(optional)_ [Core Location permission](https://developer.apple.com/documentation/corelocation/) for wifi_mac_address and wifi_ssid (starting from iOS 13)

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
#import <SeonSDK/SeonFingerprint.h>
```

If you opt to use a bridging header, the path to the header must be set in Build Settings -> Objective-C Bridging Header

###### Integration

```
/* CONFIGURATION */

let seonfp = SeonFingerprint()

// Enable logging
seonfp.setLoggingEnabled(true)

// Set session_id
seonfp.sessionId = "CUSTOM_SESSION_ID"

/* INVOCATION */

// Compute fingerprint
seonfp.fingerprintBase64 { (seonFingerprint:String?) in
    //set seonFingerprint as the value for the session 
    //property of your Fraud API request.
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
[[SeonFingerprint sharedManager] setLoggingEnabled:true]

// Set session_id
[[SeonFingerprint sharedManager] setSessionId:@"[CUSTOM_SESSION_ID]"];

/* INVOCATION */

// Compute fingerprint asynchronously
 [[SeonFingerprint sharedManager] fingerprintBase64With:^(NSString *seonFingerprint) {
        //set seonFingerprint as the value for the session 
        //property of your Fraud API request.
}];

```

## Changelog
#### 4.0.0
- Changed fingerprint method to be async, improving speed and reliability
- device_ip fields are now available
- Performance improvements

#### 3.0.8
- Swift integration improvements

#### 3.0.7
- Minor fixes and integration improvements

#### 3.0.6
- XCFramework support

#### 3.0.1
- iOS 14 compatibility
- Stability and performance improvements

#### 3.0.0
- Removed background HTTP request for data transmission, the SDK returns an encrypted, base64 encoded string to use with SEON's REST API
- Removed public key support
- Bugfixes and security improvements

#### 2.1.2
- iOS 14 compatibility

#### 2.1.1
- Stability and compatibility improvements

#### 2.1.0
- Bugfixes and security improvements

#### 2.0.4
- Bugfixes and performance improvements

#### 2.0.1
- Bugfixes and performance improvements

#### 2.0.0
- `startAnalyzingWithSession` method has been removed
- Added `scanFingerprint` method
- Added public key support
- Enhanced logging settings
- Bugfixes and performance improvements

#### 1.0.9
- Fix bug related to enabled proximity sensor

#### 1.0.8
- Bugfixes and performance improvements

#### 1.0.7
- First stable build
