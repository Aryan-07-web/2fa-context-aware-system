# User Guide: Context-Aware Authentication App

1. Direct APK Installation (Easiest)1.Locate the APK file: The file you need is at 2FA-Implementation/app/build/outputs/apk/debug/app-debug.apk.2.Transfer the APK to your Android device: You can do this via USB, email, or a cloud storage service.3.Enable "Install from Unknown Sources" on your device. You can usually find this in Settings > Security.4.Install the APK: Open the app-debug.apk file on your device and tap "Install".

2. Install via Android Studio1.Open the project in Android Studio.2.Connect your Android device (with USB debugging enabled) or start an emulator.3.Run the app: Click the "Run" button (the green play icon) in Android Studio.

3. Install via ADB (Command Line)1.Enable USB Debugging on your device (in Developer Options).2.Connect your device to your computer via USB.3.Install the APK using the following command: adb install app/build/outputs/apk/debug/app-debug.apk

Important Note on the Backend Server:This application requires a backend server to be running for it to function correctly. The USER_GUIDE.md file provides detailed instructions on how to set up the Node.js and PostgreSQL backend.Before you run the app, please make sure the backend server is running and accessible from your Android device or emulator.


## Overview

This is a **Context-Aware Authentication Application** that provides secure login using:
- **Device Fingerprinting**: Unique device identification
- **Device Integrity Checks**: Detects rooted devices, emulators, USB debugging, etc.
- **Network Detection**: Monitors network changes
- **Security Questions**: 5 predefined security questions for verification
- **Trusted Devices**: Allows trusted devices to bypass security questions

---

## System Requirements

### For Android Device/Emulator:
- **Android Version**: Android 8.0 (API 26) or higher
- **Storage**: At least 50 MB free space
- **Network**: Internet connection required (WiFi or Mobile Data)
- **Permissions**: 
  - Network access
  - Device information (for fingerprinting)

### For Backend Server:
- **Node.js**: Version 14.0 or higher
- **PostgreSQL**: Version 12.0 or higher
- **Operating System**: Linux (Ubuntu recommended) or Windows
- **Network**: Accessible IP address (192.168.2.243:3000 in this setup)

---

## Installation

### Option 1: Install APK Directly on Android Device

1. **Transfer the APK file** (`app-debug.apk`) to your Android device
   - Location: `2FA-Implementation/app/build/outputs/apk/debug/app-debug.apk`
   - Transfer via USB, email, or cloud storage

2. **Enable Unknown Sources** (if not already enabled):
   - Go to **Settings** → **Security** → Enable **"Install from Unknown Sources"**
   - Or go to **Settings** → **Apps** → **Special Access** → **Install Unknown Apps**

3. **Install the APK**:
   - Open the APK file on your device
   - Tap **"Install"**
   - Wait for installation to complete
   - Tap **"Open"** to launch the app

### Option 2: Install via Android Studio

1. **Open Android Studio**
2. **Open the Project**:
   - File → Open → Navigate to `2FA-Implementation` folder
3. **Connect Device/Emulator**:
   - Connect Android device via USB (enable USB debugging)
   - Or start an Android Emulator
4. **Run the App**:
   - Click the **Run** button (green play icon)
   - Or press `Shift + F10`
   - Select your device/emulator
   - Wait for installation and launch

### Option 3: Install via ADB (Android Debug Bridge)

1. **Enable USB Debugging** on your Android device:
   - Go to **Settings** → **About Phone**
   - Tap **Build Number** 7 times to enable Developer Options
   - Go to **Settings** → **Developer Options** → Enable **USB Debugging**

2. **Connect device via USB** and verify connection:
   ```bash
   adb devices
   ```

3. **Install the APK**:
   ```bash
   adb install app/build/outputs/apk/debug/app-debug.apk
   ```

---

## Backend Server Setup

**Important**: The app requires a backend server to function properly. The backend must be running before using the app.

### Quick Setup (If Backend is Already Configured)

If the backend server is already running on `192.168.2.243:3000`, you can skip this section.

### Full Backend Setup

1. **Connect to the VM Server** (if using remote server):
   ```bash
   ssh user6@192.168.2.243
   # Password: 7%wQa1@9
   ```

2. **Navigate to Backend Directory**:
   ```bash
   cd ~/backend-server
   ```

3. **Install Dependencies** (if not already installed):
   ```bash
   npm install
   ```

4. **Set Up Environment Variables**:
   Create a `.env` file with:
   ```env
   PORT=3000
   DB_USER=your_db_user
   DB_HOST=localhost
   DB_NAME=context_aware_2fa
   DB_PASSWORD=your_db_password
   DB_PORT=5432
   JWT_SECRET=your-secret-key-change-this
   ```

5. **Start PostgreSQL** (if not running):
   ```bash
   sudo systemctl start postgresql
   ```

6. **Start the Backend Server**:
   ```bash
   node server.js
   ```

   You should see:
   ```
   Server is running on http://0.0.0.0:3000
   Health check: http://0.0.0.0:3000/api/health
   ```

7. **Keep the Server Running**: The server must remain running while using the app.

---

## Running the Application

### First Launch

1. **Open the App** on your Android device
2. **Grant Permissions** (if prompted):
   - Network access
   - Device information

3. **Verify Backend Connection**:
   - The app connects to: `http://192.168.2.243:3000/api`
   - Ensure the backend server is running
   - Ensure your device can reach the server IP

### Network Configuration

If the backend server is on a different IP address:

1. **Edit the API URL** in the source code:
   - File: `app/src/main/java/com/example/a19dhjetor2024/ApiService.java`
   - Line 28: Change `BASE_URL` to your server IP
   - Rebuild the APK

2. **Or use Network Security Configuration**:
   - The app allows cleartext HTTP traffic to the configured IP
   - Configuration file: `app/src/main/res/xml/network_security_config.xml`

---

## Using the Application

### 1. Sign Up (First Time Users)

1. **Open the App** → Tap **"Sign Up"**
2. **Enter Details**:
   - Full Name
   - Email Address
   - Password
3. **Tap "Sign Up"**
4. **Answer Security Questions**:
   - You will be asked 5 security questions:
     - "What was your favorite place to visit as a child?"
     - "What was the title of a book, movie, or show you loved when you were younger?"
     - "What food did you really dislike as a kid but later started liking?"
     - "What was the nickname of someone close to you growing up?"
     - "What was a hobby or activity you enjoyed in school?"
   - Answer all questions (answers are case-insensitive)
   - Tap **"Register & Verify"**
5. **Verification Complete**: You'll be redirected to the login screen

### 2. Login

1. **Enter Credentials**:
   - Email Address
   - Password
2. **Tap "Log In"**
3. **Context-Aware Authentication**:
   - The app checks:
     - Device fingerprint
     - Device integrity (rooted, emulator, USB debugging, etc.)
     - Network information
     - Trusted device status

4. **Possible Outcomes**:

   **A. Direct Login (Trusted Device with Good Integrity)**:
   - If your device is trusted and has no security issues
   - You'll go directly to the Dashboard
   - Message: "Welcome back! Trusted device detected."

   **B. Security Questions Required**:
   - If device is new, untrusted, or has security issues
   - A popup will explain why verification is needed:
     - "Device is rooted"
     - "USB debugging enabled"
     - "Developer mode enabled"
     - "Network changed"
     - "This is a new device"
   - Answer the same 5 security questions
   - Tap **"Verify"**

5. **Trust Device Option** (After Security Questions):
   - If this is a new device, you'll be asked:
     - **"Trust This Device?"**
   - Choose:
     - **"Trust Device"**: Device will be saved as trusted (max 3 devices)
     - **"Not now"**: Device won't be trusted

6. **Dashboard**: After successful login, you'll see:
   - Trusted Devices count
   - Suspicious Attempts count
   - Last Login time
   - Last Network used
   - Device Status

### 3. Dashboard Features

**Trusted Devices Card**:
- Shows number of trusted devices (max 3)

**Suspicious Attempts Card**:
- Shows number of suspicious login attempts

**Login Details**:
- Last Login: Timestamp of last successful login
- Last Network: Last WiFi network or "Mobile"
- Device Status: "Secure" or "Suspicious"

**Action Buttons**:

1. **Device Management**:
   - View all trusted devices
   - See device name, model, last login
   - Remove trusted devices

2. **View Login Attempts**:
   - See all login attempts
   - View details: timestamp, IP, device, network, status, method
   - Block suspicious devices

3. **Profile**:
   - View user profile information

4. **Logout**:
   - Log out of the current session

### 4. Device Management

- **View Trusted Devices**: See all devices marked as trusted
- **Remove Device**: Tap "Remove Device" to untrust a device
- **Add Device**: Login from a new device and select "Trust Device" when prompted

### 5. Login Attempts

- **View History**: See all login attempts with details
- **Block Device**: Tap "Block Device" on suspicious attempts to block that device
- **Details Shown**:
  - Timestamp
  - IP Address
  - Device Model
  - Network Type and SSID
  - Status (Success/Failed/Pending)
  - Authentication Method
  - Suspicious Reason (if any)

---

## Troubleshooting

### App Won't Install

**Problem**: "App not installed" or "Installation failed"

**Solutions**:
1. Enable "Install from Unknown Sources" in Settings
2. Uninstall any previous version of the app
3. Check if device has enough storage space
4. Try installing via ADB: `adb install -r app-debug.apk`

### Cannot Connect to Server

**Problem**: "Network error" or "Cannot connect to server"

**Solutions**:
1. **Check Backend Server**:
   - Ensure backend is running on `192.168.2.243:3000`
   - Test in browser: `http://192.168.2.243:3000/api/health`

2. **Check Network Connection**:
   - Ensure device is connected to WiFi or Mobile Data
   - Ensure device can reach the server IP (ping test)

3. **Check Firewall**:
   - Ensure port 3000 is not blocked
   - Check server firewall settings

4. **Update Server IP** (if changed):
   - Edit `ApiService.java` line 28
   - Rebuild APK

### Registration Fails

**Problem**: "Registration failed" or "User already exists"

**Solutions**:
1. Use a different email address
2. Check backend server logs for errors
3. Ensure database is properly configured
4. Check network connection

### Security Questions Not Loading

**Problem**: "Error loading questions" or questions screen closes immediately

**Solutions**:
1. Check backend server is running
2. Ensure user completed registration with security questions
3. Check network connection
4. The app will use default questions as fallback

### Trusted Device Still Asking for Questions

**Problem**: Trusted device is still prompted for security questions

**Explanation**:
- Even trusted devices require security questions if:
  - Device is rooted
  - Running on emulator
  - USB debugging is enabled
  - Developer mode is enabled
  - Network changed significantly

**Solution**:
- A popup will explain why verification is needed
- This is a security feature to protect against compromised devices

### Dashboard Shows Zero Counts

**Problem**: Trusted devices and login attempts show "0"

**Solutions**:
1. Check backend server is running
2. Ensure device can connect to server
3. Try logging out and logging back in
4. Check backend database for data

### App Crashes

**Problem**: App closes unexpectedly

**Solutions**:
1. Clear app data: Settings → Apps → [App Name] → Clear Data
2. Reinstall the app
3. Check Android version compatibility (requires Android 8.0+)
4. Check device logs: `adb logcat | grep -i error`

---

## Technical Details

### API Endpoints Used

- `POST /api/auth/register` - User registration
- `POST /api/auth/login` - User login
- `GET /api/auth/security-questions` - Get security questions
- `POST /api/auth/verify-security-questions` - Verify answers
- `GET /api/devices/trust` - Get trusted devices
- `POST /api/devices/trust` - Add trusted device
- `GET /api/security/login-attempts` - Get login attempts
- `POST /api/security/login-attempts` - Log login attempt
- `GET /api/security/last-network` - Get last network
- `POST /api/security/update-network` - Update network

### Data Stored

- **User Credentials**: Hashed passwords (bcrypt)
- **Security Questions**: Hashed answers (bcrypt)
- **Device Fingerprints**: SHA-256 hashes
- **Login Attempts**: Timestamps, IPs, device info, network info
- **Trusted Devices**: Device fingerprints, names, models, last login

### Security Features

- **Password Hashing**: BCrypt with salt
- **Security Answer Hashing**: BCrypt with salt
- **Device Fingerprinting**: SHA-256 hash of device attributes
- **Integrity Checks**: Root detection, emulator detection, USB debugging check
- **Network Monitoring**: WiFi SSID tracking, IP address logging
- **Trusted Device Management**: Max 3 trusted devices per user

---

## Support

For issues or questions:
1. Check the Troubleshooting section above
2. Review backend server logs
3. Check Android device logs: `adb logcat`
4. Verify network connectivity
5. Ensure backend server is running and accessible

---

## Version Information

- **App Version**: 1.0.0
- **Minimum Android**: 8.0 (API 26)
- **Target Android**: Latest
- **Backend**: Node.js + Express + PostgreSQL

---
