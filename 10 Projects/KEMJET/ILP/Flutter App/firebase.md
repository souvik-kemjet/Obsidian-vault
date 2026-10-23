

#  **Firebase**

This project **(Arunachal ILP User App)** uses **Firebase** primarily for **Push Notifications** via Firebase **Cloud Messaging (FCM)**.
The primary purpose of Firebase in this app is to send real-time Push Notifications to tourists/applicants using Firebase Cloud Messaging (FCM).

• **Application Status Updates:** Instant alerts when an Inner Line Permit (ILP) application is approved, pending, or rejected.
• **Travel Alerts & Announcements:** Broadcast announcements or travel guidelines sent to all tourists or target topics.
• **Backend Integration:** When the app launches or a user logs in, it fetches a unique FCM Device Token from Firebase and syncs it with the backend server via PUT /profile. This allows the backend server to target notifications to specific user devices.


## How to Setup API Keys and Configuration Files

You can set up or refresh your API keys using either the **Automated (FlutterFire CLI)** approach or the **Manual** approach.

**Option A: Automated Setup using FlutterFire CLI (Recommended)**

1. Install Firebase CLI (if not already installed):
```bash
npm install -g firebase-tools
```
2. Login to Firebase:
```bash
firebase login

# List the Projects
firebase projects:list
```
3. Activate FlutterFire CLI:
```bash
dart pub global activate flutterfire_cli
```
4. Run Configuration Tool: In the root of your Flutter project, run:
```bash
flutterfire configure --project=arunachal-ilp
```
(Replace arunachal-ilp with your actual Firebase project ID if different).


---

**Key Project Files:**

• `pubspec.yaml` – Declares firebase_core and firebase_messaging dependencies.

• `lib/main.dart` – Initializes Firebase (Firebase.initializeApp) and listens for incoming foreground
notifications (FirebaseMessaging.onMessage) and notification clicks (onMessageOpenedApp).

• `lib/firebase_options.dart` – Contains the platform-specific credentials (apiKey, appId, messagingSenderId, projectId, storageBucket).

• `lib/common/services/notification_service.dart` – Helper service that requests permissions, fetches/caches FCM tokens, and handles subscribing/unsubscribing to topics.

• `android/app/google-services.json` – Android Firebase configuration file.

• `ios/Runner/GoogleService-Info.plist` – iOS Firebase configuration file.

• `web/firebase-messaging-sw.js` – Web service worker for background notifications (if running on web).

