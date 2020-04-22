# Overview

The Device Fingerprint tool collects thorough insight about the devices associated to a user. Account takeovers and multiple account signups, payments can easily be avoided by applying the Device Fingerprinting module. To implement SEON SDK for iOS, follow the steps below.

## Requirements
- iOS 9.0 or higher
- [Access WiFi Information entitlement](https://developer.apple.com/documentation/bundleresources/entitlements/com_apple_developer_networking_wifi-info) for wifi_mac_address and wifi_ssid
- [Core Location permission](https://developer.apple.com/documentation/corelocation/) for wifi_mac_address and wifi_ssid (starting from iOS 13)

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

## Integration


```
...
#import <SeonSDK/SeonFingerprint.h>
...
```

```
/* CONFIGURATION */

[[SeonFingerprint sharedManager] 

// Enable logging
[[SeonFingerprint sharedManager] setLoggingEnabled:true]

// Set session_id
[SeonFingerprint sharedManager].sessionId = @"[CUSTOM_SESSION_ID]";


/* INVOCATION */

// Compute and submit fingerprint
[[SeonFingerprint sharedManager] scanFingerprint:@”[YOUR_PUBLIC_KEY]”];

```

## Changelog

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
