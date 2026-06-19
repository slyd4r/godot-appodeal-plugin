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

| Ad Type | Supported |
|---|---:|
| Banner | Yes |
| Interstitial | Yes |
| Rewarded | Yes |
| Native | Yes |

---

## Native Ad Template Types

The plugin supports Appodeal native ad template types:

| Template | Description |
|---|---|
| News Feed | Good for feed-style layouts |
| App Wall | Good for app-promotion style layouts |
| Content Stream | Good for larger content/card layouts |

Native ads are displayed as Android views above the Godot game view.

Because of this, native ads can follow a Godot `Panel` position and size, but they do not inherit Godot rendering features such as clipping containers, shaders, opacity, rotation, or `z_index` automatically.

For best results, use a simple visible panel area for native ads and avoid rotating, hiding, heavily clipping, or visually distorting ad content.

---

## Requirements

- Godot 4.x
- Android export template installed
- Android Gradle Build enabled
- Appodeal account
- Appodeal Android App Key
- Android package name configured in the Appodeal dashboard
- Required Appodeal / ad-network Gradle dependencies
- Internet permission enabled in Android export
- AdMob App ID in Android manifest if using the AdMob adapter
- Valid CMP / privacy consent setup before using live ads in GDPR, CCPA, or other privacy-regulated regions

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

---

## Basic Initialization

For test ads during development:

```gdscript
const APPODEAL_APP_KEY := "YOUR_APPODEAL_APP_KEY"

func _ready() -> void:
    AppodealAds.initialization_finished.connect(_on_appodeal_initialized)

    # testing = true, auto_cache = false
    AppodealAds.init(APPODEAL_APP_KEY, true, false)


func _on_appodeal_initialized(success: bool, error_message: String) -> void:
    if not success:
        print("Appodeal initialization failed: ", error_message)
        return

    if error_message != "":
        print("Appodeal initialized with warning: ", error_message)

    print("Appodeal initialized successfully")

    AppodealAds.load_banner()
    AppodealAds.load_interstitial()
    AppodealAds.load_rewarded()
    AppodealAds.load_native(2)
```

For live ads, configure CMP / consent first. See the **CMP / Consent Setup** section below.

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

---

## Android Below API 26 / Desugaring

If your app supports Android below API 26, enable core library desugaring in your final Godot Android Gradle build.

Open:

```text
your_godot_project/android/build/build.gradle
```

Inside `android { }`, add:

```gradle
compileOptions {
    coreLibraryDesugaringEnabled true
    sourceCompatibility JavaVersion.VERSION_1_8
    targetCompatibility JavaVersion.VERSION_1_8
}
```

Inside `dependencies { }`, add:

```gradle
coreLibraryDesugaring 'com.android.tools:desugar_jdk_libs:2.1.5'
```

This is required because some ad network SDKs may use Java APIs such as `java.time.Duration`, which are not available on older Android versions without desugaring.

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

After changing networks in Appodeal or changing mediation groups, make sure to actualize / update the waterfall configuration for every ad type in the Appodeal dashboard.

---

## CMP / Consent Setup Required

This addon does **not** replace legal compliance work.

Before using live ads, each publisher must configure a valid CMP / consent flow for GDPR, CCPA, and other privacy-regulated regions.

If you see an Appodeal onboarding warning like this:

```text
CMP not integrated yet. No valid consent string detected in SDK requests from GDPR/CCPA zones.
```

or a UMP error like this:

```text
no form(s) configured for the input app ID
```

this is usually **not a Godot plugin error**. It means the app publisher has not configured the required privacy message / consent form for the AdMob App ID used by the app.

### Required AdMob / Google Privacy & Messaging setup

1. Go to **AdMob → Privacy & messaging**.
2. Create a **GDPR message**.
3. Select the same app that uses your AdMob App ID.
4. Configure the consent options.
5. Publish the message.
6. Make sure the AdMob App ID in `AndroidManifest.xml` matches that same app.

Example manifest entry:

```xml
<meta-data
    android:name="com.google.android.gms.ads.APPLICATION_ID"
    android:value="ca-app-pub-xxxxxxxxxxxxxxxx~yyyyyyyyyy" />
```

If the manifest contains this App ID:

```text
ca-app-pub-7554323896591291~1934976505
```

then the GDPR message must be created and published for the same app in AdMob.

### Correct consent startup flow

Recommended live-ad startup flow:

1. Request consent info update.
2. If consent status is `Required`, load and show the consent form.
3. If consent status is `NotRequired` or `Obtained`, initialize Appodeal directly.
4. If consent info update fails, do not call the consent form. Fix the AdMob / privacy configuration first.
5. Initialize Appodeal after the consent flow is complete.

Do **not** call the consent form when consent status is `NotRequired`. If you do, you may see:

```text
No available form can be built
```

That means no form is needed for the current user / location.

### Example Godot consent flow

```gdscript
const APPODEAL_APP_KEY := "YOUR_APPODEAL_APP_KEY"

func _ready() -> void:
    AppodealAds.consent_info_updated.connect(_on_consent_info_updated)
    AppodealAds.consent_form_dismissed.connect(_on_consent_form_dismissed)
    AppodealAds.initialization_finished.connect(_on_appodeal_initialized)

    AppodealAds.request_consent_info_update(APPODEAL_APP_KEY, false)


func _on_consent_info_updated(success: bool, message: String) -> void:
    print("Consent info updated: ", success, " / ", message)

    if not success:
        push_warning("Consent info update failed. Check AdMob Privacy & Messaging setup: " + message)
        # Do not show the consent form here because consent information may be null.
        return

    if message == "Required":
        AppodealAds.load_and_show_consent_form_if_required()
        return

    if message == "NotRequired" or message == "Obtained":
        AppodealAds.init(APPODEAL_APP_KEY, false, false)
        return

    # Fallback: initialize only if your compliance flow allows it.
    AppodealAds.init(APPODEAL_APP_KEY, false, false)


func _on_consent_form_dismissed(success: bool, message: String) -> void:
    print("Consent form dismissed: ", success, " / ", message)

    # The consent flow is complete after the form is dismissed.
    AppodealAds.init(APPODEAL_APP_KEY, false, false)


func _on_appodeal_initialized(success: bool, error_message: String) -> void:
    if not success:
        print("Appodeal initialization failed: ", error_message)
        return

    if error_message != "":
        print("Appodeal initialized with warning: ", error_message)

    AppodealAds.load_banner()
    AppodealAds.load_interstitial()
    AppodealAds.load_rewarded()
    AppodealAds.load_native(2)
```

### Common consent messages

| Message / Error | Meaning | What to do |
|---|---|---|
| `NotRequired` | Consent form is not needed for this user / location. | Initialize Appodeal directly. |
| `Required` | Consent form is needed. | Call `load_and_show_consent_form_if_required()`. |
| `Obtained` | Consent was already collected. | Initialize Appodeal directly. |
| `no form(s) configured for the input app ID` | No GDPR message is published for the AdMob App ID in the manifest. | Create and publish a GDPR message in AdMob Privacy & messaging. |
| `consent information is null` | Consent update failed, then the app tried to show a form anyway. | Do not show the form if consent update failed. |
| `No available form can be built` | Usually means consent is not required or no form is available for the current status. | Do not call the form when status is `NotRequired`. |

### Privacy options / CCPA

If your app is distributed in regions that require privacy options, provide a visible privacy entry point in your game settings menu, such as:

```text
Privacy Options
Do Not Sell or Share My Personal Information
```

Depending on your Appodeal SDK version and consent implementation, expose and call the privacy options form from your Godot UI when required.

---

## Showing Ads

Show banner on a Godot `Control` or `Panel`:

```gdscript
AppodealAds.show_banner_on_control($CanvasLayer/BannerPanel)
```

Show native ad on a Godot `Control` or `Panel`:

```gdscript
AppodealAds.show_native_on_control(
    $CanvasLayer/NativePanel,
    AppodealAds.NativeTemplate.CONTENT_STREAM
)
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

## Native Ads on Multiple Screens

The addon can register multiple native ad targets as slots, then show the correct slot depending on the active screen.

Example:

```gdscript
AppodealAds.register_native_slot(
    "home",
    $HomeScreen/NativePanel,
    AppodealAds.NativeTemplate.NEWS_FEED
)

AppodealAds.register_native_slot(
    "level_complete",
    $LevelCompleteScreen/NativePanel,
    AppodealAds.NativeTemplate.CONTENT_STREAM
)

AppodealAds.show_native_slot("level_complete")
```

Only one native Android overlay is shown at a time unless the native plugin is modified to support multiple native view instances.

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
Support: <https://ko-fi.com/slyd4r>

---

## License

This addon code is released under the MIT License.

Appodeal SDK and third-party ad network SDKs are not included under this license and remain under their respective licenses and terms.
