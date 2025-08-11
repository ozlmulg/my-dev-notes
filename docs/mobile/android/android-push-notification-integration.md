# Android Push Notification Integration

This guide explains how to set up and integrate push notifications into an Android application using Firebase Cloud
Messaging (FCM). It covers creating a Firebase project, adding the `google-services.json` file, configuring Android
project files, and testing the notifications.

**Goal:** Enable delivery of push notifications to Android devices via Firebase.

---

## 📋 Prerequisites

| Requirement        | Description                                                                            |
|--------------------|----------------------------------------------------------------------------------------|
| ✅ Firebase Project | Create one at [Firebase Console](https://console.firebase.google.com)                  |
| ✅ Android App      | Written in Kotlin or Java, with access to AndroidManifest and build.gradle files       |
| ✅ Physical Device  | Recommended for push testing (emulators may work but physical devices ensure accuracy) |

---

## 🧩 1. Firebase Setup

Follow the steps in the document below to create a Firebase project and add the `google-services.json` file to your
project:

[Android Firebase Integration](android-firebase-integration.md)

---

## ⚙️ 2. Firebase SDK and Push Notification Setup

**🔧 build.gradle (Project-level)**

```groovy
buildscript {
  dependencies {
    classpath 'com.google.gms:google-services:4.4.0' // Keep the version updated  
  }
}
```

**🔧 build.gradle (App-level)**

```groovy
plugins {
  id 'com.android.application'
  id 'com.google.gms.google-services' // Make sure this is added at the bottom  
}

dependencies {
  implementation platform('com.google.firebase:firebase-bom:32.7.0') // Version BOM  
  implementation 'com.google.firebase:firebase-messaging'
}
```

---

## 🚀 3. Initializing Firebase and Retrieving Token

**📝 MyFirebaseMessagingService.kt**

```kotlin
class MyFirebaseMessagingService : FirebaseMessagingService() {

  override fun onNewToken(token: String) {
    super.onNewToken(token)
    Log.d("FCM", "FCM Token: $token")
    // TODO: Send token to backend server for push targeting  
  }

  override fun onMessageReceived(remoteMessage: RemoteMessage) {
    super.onMessageReceived(remoteMessage)

    val notification = remoteMessage.notification
    notification?.let {
      Log.d("FCM", "Notification: ${it.title} - ${it.body}")
    }
  }
}
```

**📝 AndroidManifest.xml**

```xml

<service
    android:name=".MyFirebaseMessagingService"
    android:exported="false">
  <intent-filter>
    <action android:name="com.google.firebase.MESSAGING_EVENT"/>
  </intent-filter>
</service>

<uses-permission android:name="android.permission.POST_NOTIFICATIONS"/>  
```

✅ For Android 13+ devices, runtime permission for notifications must be requested.

---

## 🔔 4. Android 13+ Notification Permission (Recommended)

```kotlin
if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.TIRAMISU) {
  ActivityCompat.requestPermissions(this, arrayOf(Manifest.permission.POST_NOTIFICATIONS), 1001)
}
```

---

## 🚀 5. Testing Push Notifications from Firebase

1. Open **Firebase Console > Cloud Messaging > Send your first message**.
2. Enter a title and message.
3. Click **Test on device**.
4. Enter the device token (retrieved from app logs).
5. Click **Send Test Message**.
6. ✅ The notification should appear on the device.

---

## 🧠 Notes and Tips

- Do not share your `google-services.json` file publicly — it contains sensitive credentials.
- The app’s package name in Firebase must match your app’s actual package name.
- If required, add your SHA-1 key to the Firebase project for authentication.
- To successfully receive push notifications:
    - The notification permission must be granted.
    - The device must be connected to the internet.
    - The FCM token must be sent to your backend correctly.
