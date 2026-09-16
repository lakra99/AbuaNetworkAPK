# ER605 Captive Portal Detection — How It Works

## How Android/iOS Detect Captive Portal

When a phone connects to WiFi, it automatically tries to reach specific URLs:

| Device | Detection URL | What Happens |
|--------|---------------|--------------|
| Android | `http://connectivitycheck.gstatic.com/generate_204` | Expects 204 response |
| Android | `http://clients3.google.com/generate_204` | Expects 204 response |
| iOS | `http://captive.apple.com/hotspot-detect.html` | Expects "Success" |
| Windows | `http://www.msftconnecttest.com/connecttest.txt` | Expects "Microsoft Connect Test" |

## What ER605/OpenNDS Does

```
Phone connects to "Abua_Network" WiFi
          ↓
Phone tries: http://connectivitycheck.gstatic.com/generate_204
          ↓
OpenNDS intercepts request
          ↓
Returns HTTP 302 Redirect → http://192.168.10.1/...
          ↓
Android shows: "Sign in to WiFi network"
iOS shows: " captive portal detected"
          ↓
User taps notification → Opens portal page
```

## OpenNDS Configuration (Already on ER605)

```bash
# OpenNDS config on router
option enabled '1'
option fwhook_enabled '1'
option gatewayinterface 'br-lan'
option maxclients '250'
option authidletimeout '120'
option clientidletimeout '120'
option spoofips '1'
option spoofmacs '1'
option gatewayname 'Abua Networks'
option gatewayaddress '192.168.10.1'
option gatewayfqdn 'abua.local'
```

## The Magic: How Phone Knows It's a Captive Portal

### Step 1: SSID Broadcast
```
Router broadcasts: "Abua_Network" (WiFi name)
Phone sees: "Available WiFi: Abua_Network"
```

### Step 2: Phone Connects
```
Phone connects to WiFi
Gets IP via DHCP: 192.168.10.x
Gateway: 192.168.10.1
DNS: 192.168.10.1
```

### Step 3: Phone Tests Internet
```
Phone sends: GET http://connectivitycheck.gstatic.com/generate_204
Router intercepts (OpenNDS)
Returns: HTTP 302 Location: http://192.168.10.1/...
```

### Step 4: Android Detects Portal
```
Android receives: HTTP 302 (redirect)
Android shows: "Abua_Network requires sign in"
Notification appears: "Sign in to Abua_Network"
```

### Step 5: User Taps Notification
```
Opens browser → http://192.168.10.1/...
Shows: Abua Network portal page
User enters PIN → Gets internet
```

## ER605 Commands to Check/Configure

```bash
# Check OpenNDS status
ndsctl status

# Check client list
ndsctl clients

# Check WiFi SSID
uci show wireless

# Check portal page location
ls /etc/opennds/htdocs/

# Restart OpenNDS
/etc/init.d/opennds restart
```

## Mobile Detection Flow

```
┌─────────────────────────────────────────────────────────┐
│                    MOBILE DETECTION                      │
├─────────────────────────────────────────────────────────┤
│                                                          │
│  1. Phone scans WiFi                                     │
│     └─→ Finds "Abua_Network"                             │
│                                                          │
│  2. Phone connects                                       │
│     └─→ Gets IP: 192.168.10.x                           │
│     └─→ Gateway: 192.168.10.1                           │
│     └─→ DNS: 192.168.10.1                               │
│                                                          │
│  3. Phone tests internet                                 │
│     └─→ GET http://connectivitycheck.gstatic.com/generate_204 │
│                                                          │
│  4. OpenNDS intercepts                                   │
│     └─→ Returns HTTP 302 redirect                        │
│     └─→ Location: http://192.168.10.1/...                │
│                                                          │
│  5. Android shows notification                           │
│     └─→ "Sign in to Abua_Network"                        │
│     └─→ User taps → Opens portal                         │
│                                                          │
│  6. iOS shows popup                                      │
│     └─→ "captive.apple.com" detected                    │
│     └─→ Opens Safari with portal                         │
│                                                          │
└─────────────────────────────────────────────────────────┘
```

## Custom Detection (Your APK)

Your APK can also detect the portal:

```kotlin
// Check if connected to Abua Network
fun isConnectedToAbua(): Boolean {
    val ssid = getCurrentSSID()
    return ssid.contains("Abua", ignoreCase = true)
}

// Check if portal is needed
fun checkPortal(): Boolean {
    val url = URL("http://connectivitycheck.gstatic.com/generate_204")
    val connection = url.openConnection()
    connection.connectTimeout = 3000
    val responseCode = connection.responseCode
    // If 302 redirect → portal needed
    return responseCode == 302
}
```

## Summary

| What | How |
|------|-----|
| Phone finds WiFi | SSID broadcast: "Abua_Network" |
| Phone connects | DHCP gives IP + gateway |
| Phone detects portal | HTTP 302 redirect from OpenNDS |
| Android notification | "Sign in to Abua_Network" |
| iOS popup | Opens Safari with portal |
| Your APK | Can also detect via HTTP check |

**No special code needed on ER605** — OpenNDS already handles everything. The phone does all the detection automatically!
