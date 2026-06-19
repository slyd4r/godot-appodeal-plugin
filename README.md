# Appodeal Ads for Godot Android

# <p align="center">

# &#x20; <a href="https://ko-fi.com/slyd4r">

# &#x20;   <img src="https://ko-fi.com/img/githubbutton\_sm.svg" alt="Support me on Ko-fi">

# &#x20; </a>

# </p>

# <p align="center">

# &#x20; <b>Unofficial Appodeal Ads plugin for Godot Android.</b>

# </p>

# <p align="center">

# &#x20; Banner · Interstitial · Rewarded · Native Ads

# </p>

# <p align="center">

# &#x20; <img src="https://img.shields.io/badge/Godot-4.x-blue?logo=godot-engine\&logoColor=white" alt="Godot 4.x">

# &#x20; <img src="https://img.shields.io/badge/Platform-Android-green?logo=android\&logoColor=white" alt="Android">

# &#x20; <img src="https://img.shields.io/badge/Ads-Appodeal-orange" alt="Appodeal">

# &#x20; <img src="https://img.shields.io/badge/License-MIT-lightgrey" alt="MIT License">

# </p>

# \---

# Overview

# Appodeal Ads for Godot Android is an unofficial Android plugin that allows Godot 4 games to use Appodeal ads.

# The plugin provides a simple Godot API for initializing Appodeal, loading ads, showing ads, and listening to ad callbacks through Godot signals.

# It also supports showing banner and native ads over Godot `Control` / `Panel` nodes, with automatic position syncing when the target node moves or resizes.

# Created by Slyd4r.

# > This plugin is not affiliated with, sponsored by, or endorsed by Appodeal.

# > Appodeal SDK and third-party ad network SDKs remain under their own licenses and terms.

# \---

# Supported Ads

# Ad Type	Supported

# Banner	Yes

# Interstitial	Yes

# Rewarded	Yes

# Native	Yes

# \---

# Native Ad Template Types

# The plugin supports Appodeal native ad template types:

# Template	Description

# News Feed	Good for feed-style layouts

# App Wall	Good for app-promotion style layouts

# Content Stream	Good for larger content/card layouts

# Native ads are displayed as Android views above the Godot game view.

# Because of this, native ads can follow a Godot `Panel` position and size, but they do not inherit Godot effects such as clipping, shaders, opacity, rotation, or `z\_index`.

# \---

# Requirements

# Godot 4.x

# Android export template installed

# Android Gradle Build enabled

# Appodeal account

# Appodeal Android App Key

# Android package name configured in the Appodeal dashboard

# Required Appodeal/ad-network Gradle dependencies

# Internet permission enabled in Android export

# AdMob App ID in Android manifest if using the AdMob adapter

# \---

# Installation

# Copy the addon into your Godot project:

# ```text

# res://addons/AppodealNative/

# ```

# Expected structure:

# ```text

# addons/

# └── AppodealNative/

# &#x20;   ├── AppodealAds.gd

# &#x20;   ├── AppodealAdsNode.gd

# &#x20;   ├── AppodealNative-debug.aar

# &#x20;   ├── AppodealNative-release.aar

# &#x20;   ├── appodeal\_networks.cfg

# &#x20;   ├── export\_plugin.gd

# &#x20;   ├── plugin.cfg

# &#x20;   ├── README.md

# &#x20;   ├── LICENSE.md

# &#x20;   └── icons/

# &#x20;       └── appodeal.png

# ```

# Enable the plugin:

# ```text

# Project > Project Settings > Plugins > AppodealNative > Enable

# ```

# Add `AppodealAds.gd` as an Autoload:

# ```text

# Project > Project Settings > Autoload

# Path: res://addons/AppodealNative/AppodealAds.gd

# Name: AppodealAds

# Add

# ```

# Basic initialization example:

# ```gdscript

# const APPODEAL\_APP\_KEY := "YOUR\_APPODEAL\_APP\_KEY"

# 

# func \_ready() -> void:

# &#x09;AppodealAds.initialization\_finished.connect(\_on\_appodeal\_initialized)

# &#x09;AppodealAds.init(APPODEAL\_APP\_KEY, true, false)

# 

# 

# func \_on\_appodeal\_initialized(success: bool, error\_message: String) -> void:

# &#x09;if not success:

# &#x09;	print("Appodeal initialization failed: ", error\_message)

# &#x09;	return

# 

# &#x09;print("Appodeal initialized successfully")

# 

# &#x09;AppodealAds.load\_banner()

# &#x09;AppodealAds.load\_interstitial()

# &#x09;AppodealAds.load\_rewarded()

# &#x09;AppodealAds.load\_native(2)

# ```

# \---

# Android Export Setup

# Install the Android build template:

# ```text

# Project > Install Android Build Template

# ```

# Enable Gradle Build in your Android export preset:

# ```text

# Project > Export > Android > Gradle Build > Use Gradle Build = true

# ```

# If you use AdMob, add your AdMob App ID to your Android manifest:

# ```xml

# <meta-data

# &#x20;   android:name="com.google.android.gms.ads.APPLICATION\_ID"

# &#x20;   android:value="ca-app-pub-xxxxxxxxxxxxxxxx\~yyyyyyyyyy" />

# ```

# Do not commit your personal Appodeal App Key, AdMob App ID, or private ad unit IDs into a public repository.

# If your app supports Android below API 26, enable core library desugaring in your Android Gradle build:

# ```gradle

# android {

# &#x20;   compileOptions {

# &#x20;       coreLibraryDesugaringEnabled true

# &#x20;       sourceCompatibility JavaVersion.VERSION\_1\_8

# &#x20;       targetCompatibility JavaVersion.VERSION\_1\_8

# &#x20;   }

# }

# 

# dependencies {

# &#x20;   coreLibraryDesugaring 'com.android.tools:desugar\_jdk\_libs:2.1.5'

# }

# ```

# \---

# Network Configuration

# The addon can use a configuration file to control which Appodeal mediation dependencies are included during Android export:

# ```text

# res://addons/AppodealNative/appodeal\_networks.cfg

# ```

# Example minimal setup:

# ```ini

# \[appodeal]

# core=true

# admob=true

# bidmachine=true

# iab=true

# applovin=false

# pangle=false

# unity\_ads=false

# vungle=false

# mintegral=false

# ```

# Only enabled networks are added to the exported Android build.

# This helps reduce APK/AAB size.

# Network selection happens at export/build time, not at runtime.

# \---

# Showing Ads

# Show banner on a Godot `Control` or `Panel`:

# ```gdscript

# AppodealAds.show\_banner\_on\_control($CanvasLayer/BannerPanel)

# ```

# Show native ad on a Godot `Control` or `Panel`:

# ```gdscript

# AppodealAds.show\_native\_on\_control($CanvasLayer/NativePanel)

# ```

# Show interstitial:

# ```gdscript

# if AppodealAds.is\_interstitial\_loaded():

# &#x09;AppodealAds.show\_interstitial()

# ```

# Show rewarded:

# ```gdscript

# if AppodealAds.is\_rewarded\_loaded():

# &#x09;AppodealAds.show\_rewarded()

# ```

# Reward the player only from the rewarded callback:

# ```gdscript

# func \_on\_rewarded\_finished(amount: float, currency: String) -> void:

# &#x09;print("Reward earned")

# ```

# 

# \### CMP / Consent setup required

# 

# If you see:

# 

# `CMP not integrated yet`  

# or  

# `no form(s) configured for the input app ID`

# 

# this is not a Godot plugin error.

# 

# You must configure a GDPR consent message in AdMob:

# 

# 1\. Go to AdMob → Privacy \& messaging.

# 2\. Create a GDPR message.

# 3\. Select the same app that uses your AdMob App ID.

# 4\. Publish the message.

# 5\. Make sure the AdMob App ID in AndroidManifest.xml matches that app.

# 

# 

# \---

# Coffee

# If this plugin helped you, you can support my work here:

# <p align="center">

# &#x20; <a href="https://ko-fi.com/slyd4r">

# &#x20;   <img src="https://ko-fi.com/img/githubbutton\_sm.svg" alt="Support me on Ko-fi">

# &#x20; </a>

# </p>

# Or visit:

# ```text

# https://ko-fi.com/slyd4r

# ```

# \---

# Author

# Created by Slyd4r.

# GitHub: `@slyd4r`

# Support: https://ko-fi.com/slyd4r

# \---

# License

# This addon code is released under the MIT License.

# Appodeal SDK and third-party ad network SDKs are not included under this license and remain under their respective licenses and terms.

