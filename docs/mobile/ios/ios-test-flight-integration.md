# iOS TestFlight Integration

This document guides you through the steps to enable testing of your local iOS project via TestFlight by other users.

---

## Requirements

- Apple Developer account (annual $99 fee)
- Xcode
- Valid provisioning profile for your Apple device
- Access to **App Store Connect** (same Apple account)
- App must have a **Bundle ID** and version number

---

## 1. App Store Connect and Apple Developer Setup

- Have an authorized person invite you to **App Store Connect** via [https://appstoreconnect.apple.com/access/users](https://appstoreconnect.apple.com/access/users) with **Developer** role for read access or **App Manager** role for admin tasks.
- Accept the invitation email and set your password to activate your developer account.
- Log in to [https://developer.apple.com/account/](https://developer.apple.com/account/)
- Create an App ID (Bundle ID) under [https://developer.apple.com/account/resources/identifiers/list](https://developer.apple.com/account/resources/identifiers/list) if you haven’t already. Xcode might create some automatically but you may need to create others manually.
- In **App Store Connect** ([https://appstoreconnect.apple.com/apps](https://appstoreconnect.apple.com/apps)), create a new app:
    - Platform: iOS
    - Name: (Your app’s internal or public name) (e.g. `MyApp Internal`)
    - Primary Language: Choose your language
    - Bundle ID: Must exactly match the one in Xcode (e.g. `com.example.app.internal`)
    - SKU: A unique short identifier (e.g. `com.example.app.internal`)

---

## 2. Prepare Your App in Xcode

- Open your project in Xcode.
- Under the **General** tab:
    - Verify Version and Build number (e.g. Version = 1.0.0, Build = 1)
    - Manually increment Build number for each archive.
- Under **Signing & Capabilities**, select your **Team**.
- Archive your app via **Product > Archive**.
- When archive completes, **Organizer** window opens.
- Select the new archive, then click **Distribute App**.
- Choose **App Store Connect > Upload**.
- Follow the steps until you receive the **"Upload succeeded"** message.

---

## 3. Configure TestFlight in App Store Connect

- Go to **App Store Connect > My Apps > Select your app**.
- Open the **TestFlight** tab.
- Wait for your uploaded build to appear (may take a few minutes).
- For internal testing:
    - Go to **Internal Testing > click "+"** to create a test group.
    - Add testers to the group.
    - Optionally enable automatic assignment of builds to this group.
- Approve the build.
- Invite **Internal Testers** (team members) or **External Testers** (outside testers).
- Note: External testers require a brief Apple review process (usually 1–2 days).

---

## 4. What Testers Receive

- Testers download the **TestFlight** app from the App Store.
- They are invited via their Apple ID email.
- Invitations contain a message like: "**You’ve been invited to test [App Name].**"
- After accepting, testers can install and test your app.

---

## Notes

- The app does not need to be published on the App Store.
- **TestFlight** allows up to **10,000 testers** per app.
- Each build can be tested for up to **90 days**.
- Crash and usage reports are available via TestFlight.

---

## FAQ

**Q:** I get this error while archiving:

> 1. No profiles for 'com.example.app.internal.NotificationService' were found  
     >  Xcode couldn't find any iOS App Store provisioning profiles matching 'com.example.app.internal.NotificationService'.
>  2. Automatic signing cannot register bundle identifier "com.example.app.internal.NotificationService".  
      >  Automatic signing cannot register bundle identifiers with Apple. Register your bundle identifier on [https://developer.apple.com/account](https://developer.apple.com/account) and then try again.

**A:** Your app's bundle ID `com.example.app.internal.NotificationService` is not registered as an App ID. Register it manually at [https://developer.apple.com/account/resources/identifiers/list](https://developer.apple.com/account/resources/identifiers/list).

---

**Q:** I get this validation error when archiving:

> Validation failed  
> Invalid large app icon. The large app icon in the asset catalog in your app can’t be transparent or contain an alpha channel. For details, visit: [https://developer.apple.com/design/human-interface-guidelines/app-icons](https://developer.apple.com/design/human-interface-guidelines/app-icons).

**A:** App Store does not accept app icons with transparency. Remove transparency or corner rounding in your icons (set corner radius to 0 and fill background with a solid color).

---
