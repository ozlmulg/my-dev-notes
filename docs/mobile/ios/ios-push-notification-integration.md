# iOS Push Notification Integration

This document provides step-by-step instructions to create an APNs Authentication Key (.p8) from Apple Developer, add
the GoogleService-Info.plist file to the project, and configure necessary settings in Firebase Console.

The goal is to enable push notification delivery to iOS devices via Firebase.

---

## 📋 Prerequisites

| Requirement                                                      | Description                                         | Notes                                                                         |
|------------------------------------------------------------------|-----------------------------------------------------|-------------------------------------------------------------------------------|
| ✅ [Apple Developer Account](https://developer.apple.com/account) | Apple Developer account required                    | See [iOS TestFlight Integration](ios-test-flight-integration.md) for details. |
| ✅ [Firebase Project](https://console.firebase.google.com)        | Firebase project must be created                    | See [iOS Firebase Integration](ios-firebase-integration.md) for details.      |
| ✅ iOS App created with Xcode                                     | The app must be registered in Firebase              |                                                                               |
| ✅ Physical iOS device                                            | Required for push testing (simulator not supported) |                                                                               |

---

## 🧩 1. Firebase Setup

Follow the document for creating a Firebase project and adding the `GoogleService-Info.plist` file to the project:

[iOS Firebase Integration](ios-firebase-integration.md)

---

## 🧩 2. App Store Connect and Apple Developer Setup

Follow the document for Apple Developer account setup and app registration:

[iOS TestFlight Integration](ios-test-flight-integration.md)

---

## 🔑 3. Create APNs Authentication Key (.p8)

1. Log in to Apple Developer
   at [Apple Developer Auth Keys](https://developer.apple.com/account/resources/authkeys/list).
2. Click the "+" (Create a key) button at the top right.
3. Fill in required information:
    - **Key Name**: e.g., `FCM APNs Key`
    - **Services**: Select **Apple Push Notifications service (APNs)** ✅
4. Click **Continue > Register**, then download the **.p8** file.
5. Note these values:
    - **Key ID** (shown on the main page)
    - **Team ID** (found under [Membership](https://developer.apple.com/account/#/membership))

---

## ☁️ 4. Upload APNs Key to Firebase Console

1. Go to **Firebase Console > Project Settings > Cloud Messaging** tab.
2. Scroll down to **iOS App Configuration > APNs Authentication Key** section.
3. Upload the **.p8** file.
4. Fill in **Key ID** and **Team ID** fields.
5. Click **Save**.

✅ Firebase is now authorized to connect to APNs!

---

## ⚙️ 5. iOS App Push Notification Setup

Ensure `FirebaseMessaging` SDK is installed.

- Add the following code in `AppDelegate.swift`:

```swift
import UIKit
import Firebase
import UserNotifications

@UIApplicationMain
class AppDelegate: UIResponder, UIApplicationDelegate {

  func application(_ application: UIApplication,
                   didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {
    FirebaseApp.configure()

    UNUserNotificationCenter.current().delegate = self
    UNUserNotificationCenter.current().requestAuthorization(options: [.alert, .badge, .sound]) { granted, error in
        print("Push permission: \(granted)")
    }

    application.registerForRemoteNotifications()
    Messaging.messaging().delegate = self

    return true
  }

  func application(_ application: UIApplication,
                   didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
    Messaging.messaging().apnsToken = deviceToken
  }
}
```

- `MessagingDelegate` and `UNUserNotificationCenterDelegate` Extensions:

```swift
extension AppDelegate: MessagingDelegate {
  func messaging(_ messaging: Messaging, didReceiveRegistrationToken fcmToken: String?) {
    // Send token to backend
    NotificationServiceV2Helper.shared.saveFCMToken(fcmToken)
  }
}

extension AppDelegate: UNUserNotificationCenterDelegate {
  func userNotificationCenter(_ center: UNUserNotificationCenter,
                              willPresent notification: UNNotification,
                              withCompletionHandler completionHandler: @escaping (UNNotificationPresentationOptions) -> Void) {
    completionHandler([.alert, .badge, .sound])
  }
}
```

## 🚀 6. Test: Send Push via Firebase Console

1. Go to **Firebase Console > Cloud Messaging**.
2. Click **Send your first message**.
3. Enter title and message.
4. Select **Test on device**.
5. Enter the FCM token retrieved from the iOS device.
6. Click **Send Test Message**.

✅ Push notification should appear on the device.

---

## 🧠 Notes & Tips

- The **.p8 APNs key** is valid for all your apps (one key for all bundle IDs).
- Push notifications **do not work on the simulator**; testing must be on a physical device.
- If you don't have a physical device, upload your app to TestFlight and ask someone with a real device to test.
- To receive push notifications:
    - Notification permission must be granted.
    - Device must have an internet connection.
    - FCM token must be sent correctly to your backend.

---

## 💬 FAQ

**Q:** When I visit [https://developer.apple.com/account/resources/](https://developer.apple.com/account/resources/), I
get this error:

> Access Unavailable  
> This resource is only for developers enrolled in a developer program or members of an organization's team in a
> developer program.

**A:** You need to be added to the Apple Developer account by an authorized person. See step 2 for details.

---

**Q:** When I
visit [https://developer.apple.com/account/resources/authkeys/list](https://developer.apple.com/account/resources/authkeys/list),
I get this error:

> You are not allowed to perform this operation. Please check with one of your Team Admins, or, if you need further
> assistance, please contact Apple Developer Program Support.  
> [https://developer.apple.com/support](https://developer.apple.com/support)

**A:** You need to be added to the Apple Developer account by an authorized person. See step 2 for details.
