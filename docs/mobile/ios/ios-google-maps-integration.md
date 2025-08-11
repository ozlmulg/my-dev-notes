# iOS Google Maps Integration

This document explains step-by-step how to integrate Google Maps into an iOS application.

---

## 1. 🔑 Google Cloud Console Setup

### 1.1 Create or Select Project

- Log in to [Google Cloud Console](https://console.cloud.google.com/).
- Create a new project or select an existing one.
- For permissions, coordinate with an authorized team member.

### 1.2 Enable APIs

Enable the following APIs for your project:

- ✅ Maps SDK for iOS (for maps)
- ✅ Places API (for local search/autocomplete)
- ✅ Places SDK for iOS

> Note: Enable any additional APIs depending on the services your app uses.

### 1.3 Create API Key

- Navigate to **API & Services > Credentials**.
- Create a new API key.
- Give it a descriptive name (e.g., `MyApp iOS Maps Key`).

Apply restrictions:

- **Application Restrictions:**
    - iOS apps
    - Bundle ID: Your app’s exact bundle ID (e.g., `com.example.myapp`)

- **API Restrictions:**
    - Allow access only to:
        - Maps SDK for iOS
        - Places API
        - Places SDK for iOS

---

## 2. 🧩 Xcode Setup

### 2.1 Required SDKs

Make sure `GoogleMaps` and `GooglePlaces` SDKs are installed in your project.

### 2.2 Define API Key

In your `AppDelegate.swift`, inside the `application(_:didFinishLaunchingWithOptions:)` method, add:

```swift
import GoogleMaps
import GooglePlaces

GMSServices.provideAPIKey("YOUR_API_KEY")
GMSPlacesClient.provideAPIKey("YOUR_API_KEY")
```

> It’s recommended to fetch the API key from a configuration file or plist instead of hardcoding.

### 2.3 Info.plist Settings

Add the following keys and descriptions if your app uses location services:

```xml

<key>NSLocationWhenInUseUsageDescription</key>
<string>Permission required to show your location on the map.</string>
<key>NSLocationAlwaysAndWhenInUseUsageDescription</key>
<string>Permission required to show your location on the map.</string>
<key>io.flutter.embedded_views_preview</key>
<true/>
```

> Location permissions are necessary if your app uses device location features.

## 3. In-App Settings

- Ensure your Bundle ID exactly matches the one set in Google Cloud Console.

- Consider defining the API key in a configuration file (e.g., `Config.plist` or `Environment.swift`) for easier
  management.

- Use different API keys for testing and production environments if needed.

## 4. Testing & Debugging

Test the following features inside your app:

- Does the map load correctly?
- Are pins placed correctly on the map?
- Are location permissions requested and handled properly?
- Does Places API autocomplete return results as expected?
- Can addresses be added via the map?

If you encounter errors in Logcat, check:

- The API key might be incorrect.
- Required APIs might not be enabled.
- iOS location permissions might be missing.
- Check Xcode Console logs for messages like:

```
Google Maps SDK for iOS version...
Loaded GMSCoreResources.bundle
```

## Notes

- Google Maps usage includes a $200 free monthly credit; billing applies if usage exceeds this.

- Autocomplete queries can be frequent; consider adding throttling or limiting requests.
