# iOS Firebase Integration

This document outlines the basic setup required to connect an iOS application to Firebase. This integration enables
services such as Push Notifications, Crashlytics, and Analytics.

---

## 📋 Prerequisites

| Requirement        | Description                                                                  |
|--------------------|------------------------------------------------------------------------------|
| ✅ Firebase Account | Create or access via [Firebase Console](https://console.firebase.google.com) |
| ✅ iOS App in Xcode | An existing iOS project created in Xcode                                     |

---

## 🔧 1. Create a Firebase Project

1. Go to [Firebase Console](https://console.firebase.google.com).
2. Click **"Add project"**.
3. Enter a project name → Enable Google Analytics (optional).
4. Complete the project creation process.

---

## 🧩 2. Add Your iOS App to Firebase

1. Open your Firebase project in the console.
2. In the **Project Overview**, click the **iOS** icon.
3. Fill in the following:
    - **iOS Bundle ID**: Must exactly match the bundle ID in your Xcode project (e.g., `com.example.myapp`).
    - **App nickname**: (Optional) A display name for your app. (e.g., `MyApp - iOS Internal App`)
    - **App Store ID**: Leave blank if not applicable.
4. Click **Register app**.
5. Download the **GoogleService-Info.plist** file.

---

## 📥 3. Add `GoogleService-Info.plist` to Your Project

1. Drag the downloaded `GoogleService-Info.plist` file into your Xcode project.
2. In the popup:
    - ✅ **Check** "Copy items if needed".
    - ✅ Ensure your main app target is selected in **Add to targets**.

> This file is required for the Firebase SDK to load your app configuration.

---

## ⚙️ 4. Install Firebase SDK

**Using Swift Package Manager (Recommended):**

1. In Xcode, go to **File > Add Packages**.
2. Enter the URL: https://github.com/firebase/firebase-ios-sdk

3. Select the modules you need:

- `FirebaseAnalytics`
- `FirebaseMessaging` (for push notifications)
- `FirebaseCrashlytics`, `FirebaseAuth`, etc., as needed.

---

## 🚀 5. Initialize Firebase SDK

**AppDelegate.swift**:

```swift
import Firebase

func application(_ application: UIApplication,
                 didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {
  FirebaseApp.configure()
  return true
}
```
