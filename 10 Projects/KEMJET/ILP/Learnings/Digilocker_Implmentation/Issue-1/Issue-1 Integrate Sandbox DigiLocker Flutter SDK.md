---
tags:
  - work
  - project
title: Integrate Sandbox DigiLocker Flutter SDK
---

## **Scope**
- Add and configure the sandbox_digilocker_sdk dependency.
- Confirm supported Android SDK versions and SDK initialization options.
- Document local sandbox setup.
  Acceptance criteria
- [x] Flutter app builds successfully on Android.
- [ ] SDK can be initialized using sandbox configuration.
- [x] No provider secret or server access token is stored in Flutter.

---

### Add and configure the `sandbox_digilocker_sdk` dependency.

**Step 1: Add the Dependency**

Open your terminal at the root directory of your Flutter project and execute the installation command: 
```bash
flutter pub add sandbox_digilocker_sdk
```

This will automatically inject the dependency into your **`pubspec.yaml`** file under the `dependencies:`

```yaml
dependencies:
  flutter:
    sdk: flutter
  sandbox_digilocker_sdk: ^1.0.1  # Or the latest compatible version

```

---

**Step 2: Native Platform Configuration**
Because the SDK launches an embedded secure web wrapper (`flutter_inappwebview`) to interact with the DigiLocker login engine, you must grant internet permissions to your underlying native modules.

🌐 Android Setup

Open your **`android/app/src/main/AndroidManifest.xml`** file and ensure the internet permission is included directly inside the `<manifest>`.
```xml
<manifest xmlns:android="http://openxmlformats.org">
    <!-- Include this line -->
    <uses-permission android:name="android.permission.INTERNET"/>
    
    <application ...>
        ...
    </application>
</manifest>

```


**🍏 iOS Setup**
Open your **`ios/Runner/Info.plist`** file and ensure arbitrary loads are permitted inside the application security dictionary to handle standard external redirect calls smoothly: 
```xml
<key>NSAppTransportSecurity</key>
<dict>
    <key>NSAllowsArbitraryLoads</key>
    <true/>
</dict>

```

---

**Step 3: Implement the SDK in Dart Code**
To run the DigiLocker authentication, the flow requires a backend `session_id`. You generate this by calling Sandbox's [Create Session API](https://developer.sandbox.co.in/api-reference/kyc/digilocker-sdk/getting-started/introduction) from your application's secure backend infrastructure.

```dart
import 'package:sandbox_digilocker_sdk/sandbox_digilocker_sdk.dart';

class DigiLockerListener implements EventListener {
  DigiLockerListener(this.sessionId, this.onDone);
  final String sessionId;
  final void Function(bool completed) onDone;

  @override
  void onEvent(Map<String, dynamic> event) {
    final type = event['type'] ?? event['event'];   // log once to confirm the key name
    if (type == 'in.co.sandbox.kyc.digilocker_sdk.session.completed') {
      onDone(true);    // now ask YOUR backend to verify
    } else if (type == 'in.co.sandbox.kyc.digilocker_sdk.session.closed') {
      onDone(false);   // user backed out, offer "Try again"
    }
  }
}

Future<void> startAadhaarVerification(BuildContext context) async {
  // 1. Ask your backend for a session (sends the user's Sanctum token)
  final res = await api.post('/api/kyc/digilocker/sdk/sessions/create',
      data: {'doc_types': ['aadhaar']});
  final sessionId = res.data['data']['id'];

  // 2. Open the SDK
  DigilockerSDK.instance.setAPIKey(Env.sandboxPublicKey); // the key_... one, never the secret
  DigilockerSDK.instance.setEventListener(DigiLockerListener(sessionId, (completed) async {
    if (!completed) return;
    final verified = await api.post('/api/kyc/digilocker/sdk/sessions/$sessionId/complete');
    // show verified name/photo and move on to the ILP form
  }));
  await DigilockerSDK.instance.open(context: context, options: {
    'session_id': sessionId,
    'brand': {'name': 'Arunachal ILP', 'logo_url': 'https://<your-domain>/logo.png'},
    'theme': {'mode': 'light', 'seed': '#1B5E20'},
  });
}

```

---

**Step 4: Tracking Events (Optional)**
If you want to catch live changes while the user updates their OTPs or uploads identities, you can bind a global listener instance.
```
// Hook this in your main initialization logic before calling .open()
DigilockerSDK.instance.setEventListener(SDKEventListener()); //
```
