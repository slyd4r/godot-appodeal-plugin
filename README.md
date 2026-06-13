# Appodeal Ads for Godot Android

<p align="center">
  <a href="https://ko-fi.com/slyd4r">
    <img src="https://ko-fi.com/img/githubbutton_sm.svg" alt="Support me on Ko-fi">
  </a>
</p>

<p align="center">
  <b>Unofficial Appodeal Ads plugin for Godot Android.</b>
</p>

<p align="center">
  Banner · Interstitial · Rewarded · Native Ads
</p>

<p align="center">
  <img src="https://img.shields.io/badge/Godot-4.x-blue?logo=godot-engine&logoColor=white" alt="Godot 4.x">
  <img src="https://img.shields.io/badge/Platform-Android-green?logo=android&logoColor=white" alt="Android">
  <img src="https://img.shields.io/badge/Ads-Appodeal-orange" alt="Appodeal">
  <img src="https://img.shields.io/badge/License-MIT-lightgrey" alt="MIT License">
</p>

---

## Overview

**Appodeal Ads for Godot Android** is an unofficial Android plugin that allows Godot 4 games to use Appodeal ads.

The plugin provides a simple Godot API for initializing Appodeal, loading ads, showing ads, and listening to ad callbacks through Godot signals.

It also supports showing banner and native ads over Godot `Control` / `Panel` nodes, with automatic position syncing when the target node moves or resizes.

Created by **Slyd4r**.

> This plugin is not affiliated with, sponsored by, or endorsed by Appodeal.
> Appodeal SDK and third-party ad network SDKs remain under their own licenses and terms.

---

## Supported Ads

| Ad Type      | Supported |
| ------------ | --------: |
| Banner       |       Yes |
| Interstitial |       Yes |
| Rewarded     |       Yes |
| Native       |       Yes |

---

## Native Ad Template Types

The plugin supports Appodeal native ad template types:

| Template       | Description                          |
| -------------- | ------------------------------------ |
| News Feed      | Good for feed-style layouts          |
| App Wall       | Good for app-promotion style layouts |
| Content Stream | Good for larger content/card layouts |

Native ads are displayed as Android views above the Godot game view.

Because of this, native ads can follow a Godot `Panel` position and size, but they do not inherit Godot effects such as clipping, shaders, opacity, rotation, or `z_index`.

---

## Requirements

* Godot 4.x
* Android export template installed
* Android Gradle Build enabled
* Appodeal account
* Appodeal Android App Key
* Android package name configured in the Appodeal dashboard
* Required Appodeal/ad-network Gradle dependencies
* Internet permission enabled in Android export
* AdMob App ID in Android manifest if using the AdMob adapter

---

## Installation

Copy the addon into your Godot project:

```text
res://addons/AppodealNative/
```

Expected structure:

```text
addons/
└── AppodealNative/
    ├── AppodealAds.gd
    ├── AppodealAdsNode.gd
    ├── AppodealNative-debug.aar
    ├── AppodealNative-release.aar
    ├── appodeal_networks.cfg
    ├── export_plugin.gd
    ├── plugin.cfg
    ├── README.md
    ├── LICENSE.md
    └── icons/
        └── appodeal.png
```

Enable the plugin:

```text
Project > Project Settings > Plugins > AppodealNative > Enable
```

Add `AppodealAds.gd` as an Autoload:

```text
Project > Project Settings > Autoload
Path: res://addons/AppodealNative/AppodealAds.gd
Name: AppodealAds
Add
```

Basic initialization example:

```gdscript
const APPODEAL_APP_KEY := "YOUR_APPODEAL_APP_KEY"

func _ready() -> void:
	AppodealAds.initialization_finished.connect(_on_appodeal_initialized)
	AppodealAds.init(APPODEAL_APP_KEY, true, false)


func _on_appodeal_initialized(success: bool, error_message: String) -> void:
	if not success:
		print("Appodeal initialization failed: ", error_message)
		return

	print("Appodeal initialized successfully")

	AppodealAds.load_banner()
	AppodealAds.load_interstitial()
	AppodealAds.load_rewarded()
	AppodealAds.load_native(2)
```

---

## Android Export Setup

Install the Android build template:

```text
Project > Install Android Build Template
```

Enable Gradle Build in your Android export preset:

```text
Project > Export > Android > Gradle Build > Use Gradle Build = true
```

If you use AdMob, add your AdMob App ID to your Android manifest:

```xml
<meta-data
    android:name="com.google.android.gms.ads.APPLICATION_ID"
    android:value="ca-app-pub-xxxxxxxxxxxxxxxx~yyyyyyyyyy" />
```

Do not commit your personal Appodeal App Key, AdMob App ID, or private ad unit IDs into a public repository.

If your app supports Android below API 26, enable core library desugaring in your Android Gradle build:

```gradle
android {
    compileOptions {
        coreLibraryDesugaringEnabled true
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
}

dependencies {
    coreLibraryDesugaring 'com.android.tools:desugar_jdk_libs:2.1.5'
}
```

---

## Network Configuration

The addon can use a configuration file to control which Appodeal mediation dependencies are included during Android export:

```text
res://addons/AppodealNative/appodeal_networks.cfg
```

Example minimal setup:

```ini
[appodeal]
core=true
admob=true
bidmachine=true
iab=true
applovin=false
pangle=false
unity_ads=false
vungle=false
mintegral=false
```

Only enabled networks are added to the exported Android build.

This helps reduce APK/AAB size.

Network selection happens at export/build time, not at runtime.

---

## Showing Ads

Show banner on a Godot `Control` or `Panel`:

```gdscript
AppodealAds.show_banner_on_control($CanvasLayer/BannerPanel)
```

Show native ad on a Godot `Control` or `Panel`:

```gdscript
AppodealAds.show_native_on_control($CanvasLayer/NativePanel)
```

Show interstitial:

```gdscript
if AppodealAds.is_interstitial_loaded():
	AppodealAds.show_interstitial()
```

Show rewarded:

```gdscript
if AppodealAds.is_rewarded_loaded():
	AppodealAds.show_rewarded()
```

Reward the player only from the rewarded callback:

```gdscript
func _on_rewarded_finished(amount: float, currency: String) -> void:
	print("Reward earned")
```

---

## Coffee

If this plugin helped you, you can support my work here:

<p align="center">
  <a href="https://ko-fi.com/slyd4r">
    <img src="https://ko-fi.com/img/githubbutton_sm.svg" alt="Support me on Ko-fi">
  </a>
</p>

Or visit:

```text
https://ko-fi.com/slyd4r
```

---

## Author

Created by **Slyd4r**.

GitHub: `@slyd4r`
Support: https://ko-fi.com/slyd4r

---

## License

This addon code is released under the MIT License.

Appodeal SDK and third-party ad network SDKs are not included under this license and remain under their respective licenses and terms.
