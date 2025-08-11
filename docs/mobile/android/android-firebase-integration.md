# Android Firebase Integration

This document describes the basic configuration required to connect an Android application to Firebase. Completing this
integration allows you to use services such as **Push Notifications**, **Crashlytics**, and **Analytics**.

---

## 📋 Prerequisites

| Requirement        | Description                                                   | Notes                                               |
|--------------------|---------------------------------------------------------------|-----------------------------------------------------|
| ✅ Firebase Account | [Firebase Console](https://console.firebase.google.com)       | Ensure you have access or invitation from an admin. |
| ✅ Android Studio   | Development environment where Firebase SDK will be integrated |                                                     |
| ✅ Android Project  | The Android app project to be connected to Firebase           |                                                     |

---

## 🔧 1. Creating a Firebase Project

1. Go to [Firebase Console](https://console.firebase.google.com).
2. Click **Add project**.
3. Enter a project name and optionally enable Google Analytics.
4. Complete the project creation process.

---

## 🧩 2. Adding an Android App to Firebase

1. Open your Firebase project.
2. Click the **Android** icon to add an app.
3. Fill in the required info:
    - Android package name (e.g., `com.example.myapp`)
    - App nickname (optional) (e.g., `MyApp - Android Internal App`)
    - Debug signing certificate SHA-1 (optional, can be retrieved via Android Studio)
4. Click **Register app**.
5. Download the `google-services.json` file.

---

## 📥 3. Adding `google-services.json` to Your Project

Place the downloaded `google-services.json` file into your Android app folder: `YourProject/app/google-services.json`

This file enables the Firebase SDK to configure the app.

---

## ⚙️ 4. Firebase SDK Setup

- In project-level `build.gradle` (usually `build.gradle (Project: your-app)`):

```groovy
buildscript {
  dependencies {
    classpath 'com.google.gms:google-services:4.4.0'
  }
}
```    

- In app-level `build.gradle` (usually `build.gradle (Module: app)`):

```groovy
plugins {
  id 'com.android.application'
  id 'com.google.gms.google-services' // add at the bottom
}

dependencies {
  implementation 'com.google.firebase:firebase-analytics:21.6.1'
  // Optional Firebase modules:
  implementation 'com.google.firebase:firebase-messaging:23.4.1' // for push notifications
}
```

---

## 🚀 5. Starting Firebase SDK

Firebase initializes automatically with `google-services.json` and the Google services plugin.

If you want manual initialization, do it in your `Application` subclass:

```kotlin
class BaseApplication : Application() {
  override fun onCreate() {
    super.onCreate()
    FirebaseApp.initializeApp(this)
  }
}
```

And add to your `AndroidManifest.xml`:

```xml

<application
    android:name=".BaseApplication"
    ... >
```
