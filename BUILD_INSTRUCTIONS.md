# Abua Network APK — Build Instructions

## Folder Location
```
C:\Users\Tinl99\Desktop\AbuaNetworkAPK
```

---

## Option 1: Build with Android Studio (Recommended)

1. Download Android Studio: https://developer.android.com/studio
2. Open Android Studio → File → Open → Select `AbuaNetworkAPK` folder
3. Wait for Gradle sync (may take 5-10 minutes)
4. Click Build → Build Bundle(s) / APK(s) → Build APK(s)
5. APK will be at: `app/build/outputs/apk/debug/app-debug.apk`

---

## Option 2: Build with GitHub Actions (No Android Studio needed)

1. Go to https://github.com and create account
2. Create new repository: `AbuaNetworkAPK`
3. Upload all files from `C:\Users\Tinl99\Desktop\AbuaNetworkAPK`
4. Go to Actions tab → Click "Build APK"
5. After build completes, download APK from Artifacts section

---

## Option 3: Build with Online Tool

1. Go to https://www.appsgag.com/online-apk-builder
2. Upload the entire `AbuaNetworkAPK` folder
3. Wait for build to complete
4. Download APK file

---

## What's Inside

| File | Purpose |
|------|---------|
| `MainActivity.kt` | Complete app with ALL features |
| `AndroidManifest.xml` | App permissions and screens |
| `build.gradle` | App dependencies |
| `colors.xml` | App colors |
| `themes.xml` | App theme |

---

## Features Included

- Login screen with phone number
- Home dashboard with plan info, data usage, coins
- Community chat with real-time messages
- WiFi voice and video calling
- Local shop directory
- Government schemes info
- Ad watching to earn coins
- WhatsApp payment integration

---

## Server Configuration

Edit `MainActivity.kt` line 27 to change server IP:

```kotlin
var SERVER_URL = "http://192.168.10.233:8081"  // Your laptop IP
```

---

## After Building APK

1. Copy APK to your phone
2. Enable "Install from Unknown Sources"
3. Install the APK
4. Connect to Abua Network WiFi
5. Open app → Login → Start using!
