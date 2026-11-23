
To release your Flutter app **Arunachal ILP** (`arunachal_ilp_flutter`) or **replace an existing app on the Google Play Store / Apple App Store**, here is the complete checklist and step-by-step guide tailored specifically to your codebase.

# CRITICAL Requirements if REPLACING an Existing Published App

If you are updating/replacing an app already published on Google Play or the App Store, you must ensure these 3 things match, or the app store will treat it as a brand new app or reject the update:

1. **Application ID / Package Name MUST match exactly:**
	◦ Check  `build.gradle.kts`:
	```kotlin
	applicationId = "com.kemjethimalog.arunachaltourist"
	```
	
	◦ **Verification:** Go to your Google Play Console / App Store Connect listing. The package name/Bundle ID must match your existing app’s package name word-for-word. If it differs, update `applicationId` in  `build.gradle.kts` (and iOS Bundle ID in Xcode).

2. **Keystore & Signing Key MUST be identical (Android):**
	◦ You MUST sign the release build with the exact same `.jks` or `.keystore` file used to sign previous versions of your app (or upload key if Google Play App Signing is enabled).
	◦ Warning: If you sign with a different key, Google Play will refuse the upload with a signature mismatch error.
	```yaml
	version: 1.0.1+3 # increment 1.0.0+2 -> 1.0.1+3 (Version Code 3 > Previous Version Code)
	```
	◦ The number after + (versionCode) must be strictly greater than the highest version code ever uploaded to Play Console.

---
