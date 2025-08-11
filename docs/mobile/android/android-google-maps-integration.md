# Android Google Maps Integration

This document provides step-by-step instructions to add Google Maps integration to an Android application.

---

## 1. 🔑 Google Cloud Console Setup

### 1.1 Create or Select Project

- Log in to [Google Cloud Console](https://console.cloud.google.com/).
- Create a new project or select an existing one.
- For permissions, coordinate with an authorized team member.

### 1.2 Enable APIs

Enable the following APIs:

- ✅ Maps SDK for Android (for maps)
- ✅ Places API (for local search/autocomplete)

> Note: Enable any additional APIs your app requires.

### 1.3 Create API Key

- Navigate to **API & Services > Credentials**.
- Create a new API Key.
- Name the key appropriately (e.g., "App Internal - Android Maps Key").
- Apply restrictions as follows:

    - **Application Restrictions**:
        - Android apps
        - Package name: `com.example.app.internal`
        - SHA-1 Certificate fingerprint: (from keystore, explained below)

    - **API Restrictions**:
        - Allow access only to:
            - Maps SDK for Android
            - Places API

---

## 2. 🔐 Obtain Keystore SHA-1 Fingerprint

- Follow instructions for [Android Keystore Settings](android-keystore-settings.md) for generating a keystore and
  obtaining SHA-1 fingerprint in
  your Android Studio project.
- Alternatively, run the following command:

  `./gradlew signingReport`

- Use the SHA-1 value shown under the release or debug variant in Google Cloud Console.
- It is recommended to register both debug (internal) and release (production) SHA-1 fingerprints.

---

## 3. 🧩 Android Studio Configuration

### 3.1 build.gradle Setup

**Project-level build.gradle:**

    allprojects {
        repositories {
            google()
            mavenCentral()
        }
    }

**App-level build.gradle:**

    dependencies {
        implementation 'com.google.android.gms:play-services-maps:18.2.0'
        implementation 'com.google.android.libraries.places:places:3.4.0'
    }

### 3.2 AndroidManifest.xml Setup

    <manifest>
        <application>
            <meta-data
                android:name="com.google.android.geo.API_KEY"
                android:value="@string/google_maps_key" />
        </application>

        <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
        <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
    </manifest>

### 3.3 Add API Key

Add the `google_maps_key` in your `res/values/strings.xml` file:

    <string name="google_maps_key">YOUR_API_KEY</string>

You can define different keys for different build flavors or environments if needed.

---

## 4. 🧪 Testing & Debugging

Test the following features inside your app:

- Does the map load correctly?
- Are pins placed correctly on the map?
- Are location permissions requested and handled properly?
- Does Places API autocomplete return results as expected?
- Can addresses be added via the map?

If you encounter errors in Logcat, check:

- Is the API key correct?
- Is the SHA-1 fingerprint registered properly?
- Are necessary permissions granted?
- Are APIs enabled in Google Cloud Console?

---

## 📝 Notes

- Google Maps API is paid beyond a free monthly quota ($200).
- Autocomplete API can be queried frequently; consider throttling if needed.
- If map does not load in release build, verify the SHA-1 fingerprint matches the release keystore.

