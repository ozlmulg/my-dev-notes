# Android Keystore Settings

This document explains the steps to create, configure, and encrypt sensitive files such as `.keystore` and
`release.properties` in an Android project.

---

## 1. 🔐 Creating .keystore Files

- In Android Studio, go to **Build > Generate Signed Bundle / APK**.
- Select **Android App Bundle** → Click **Next**.
- Click **Create new...**.
- Fill in the fields as follows:

| Field            | Example Value                    |
|------------------|----------------------------------|
| Keystore path    | `.keystore/appinternal.keystore` |
| Password         | `123456` (example only)          |
| Alias            | `appinternalkeystore`            |
| Key password     | `123456` (example only)          |
| Validity (years) | 25                               |

- Repeat the above process for a production keystore (e.g. `.keystore/app.keystore`).

---

## Keystore Folder Structure

The `.keystore/` folder should be placed at the project root and contain the keystore files.

| Environment | File Name              | Description             |
|-------------|------------------------|-------------------------|
| Debug       | `appinternal.keystore` | For development signing |
| Release     | `app.keystore`         | For production signing  |

---

## 2. 🧾 release.properties File

This file is read by Gradle for signing information and stores keystore properties:

Example `release.properties` content:

``` yaml
APP_KEYSTORE_PASSWORD=123456
APP_ALIAS_PASSWORD=appinternalkeystore
APP_KEY_PASSWORD=123456
```

- This file can also be named `keystore.properties`.
- Allows managing separate credentials for debug and release builds.
- **Important:** This file **must** be included in `.gitignore` to avoid committing sensitive data.

---

## 3. 🔐 Obtaining Keystore SHA-1 Fingerprint

Run this command to get SHA-1, SHA-256, and certificate details:

    keytool -list -v -keystore .keystore/appinternal.keystore -alias appinternalkeystore

- Enter the keystore password when prompted (e.g., `123456`).
- The output includes certificate fingerprints required for services like Google Cloud Console.

---

## 4. 🔐 Encrypting the Properties File (`release.properties.gpg`)

From the project root, run:

    gpg --symmetric --cipher-algo AES256 release.properties

- You will be prompted for a passphrase (example: `123456`).
- This generates `release.properties.gpg`.
- The original `release.properties` should be deleted or kept in `.gitignore`.
- If `gpg` is not installed, install it via package manager (e.g., `brew install gnupg` on macOS).

---

## 5. 🔓 Decrypting the Encrypted File

To decrypt `release.properties.gpg` on another machine or CI/CD:

    gpg --output release.properties --decrypt release.properties.gpg

---

## 6. ✅ Gradle SigningConfig Setup

In your app-level `build.gradle`:

    android {
        signingConfigs {
            debug {
                storeFile = rootProject.file(".keystore/appinternal.keystore")
                storePassword = "123456"
                keyAlias = "appinternalkeystore"
                keyPassword = "123456"
            }
            release {
                storeFile = rootProject.file(".keystore/app.keystore")
                storePassword = releaseProperties.getProperty("APP_KEYSTORE_PASSWORD")
                keyAlias = releaseProperties.getProperty("APP_ALIAS_PASSWORD")
                keyPassword = releaseProperties.getProperty("APP_KEY_PASSWORD")
            }
        }
    
        buildTypes {
            debug {
                signingConfig signingConfigs.debug
            }
            release {
                signingConfig signingConfigs.release
            }
        }
    }

- `releaseProperties` is loaded from the decrypted `release.properties` file, which is **not** committed to Git.

---

## 7. ✅ Usage in CI/CD Environment

- The `.gpg` encrypted file can be committed to the repository.
- During the CI/CD pipeline, decrypt it securely.
- Store the decryption passphrase as an environment secret (e.g., `RELEASE_FILE_PASSPHRASE`).
- Avoid hardcoding passwords.
- Use the same password for keystore and `.gpg` for simplicity.

---

## 8. 🛑 Security Notes

- Never commit `.properties` files containing sensitive data to Git.
- Encrypted `.gpg` files can be version controlled but must be protected.
- On shared machines, delete decrypted files immediately after use.