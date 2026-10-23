

# **Step 1: Clean and Test Locally**

Before building for release, make sure everything compiles cleanly and runs without errors.
1. Clean previous build artifacts and fetch dependencies:
	```bash
	flutter clean
	flutter pub get
	```
2. Run the app in debug or release mode on a connected device or emulator to verify stability:
	```bash
	flutter run
	```

# Step 2: Configure App Signing (Release Keystore)
Google Play requires all apps to be digitally signed with a release keystore. Your project is already configured to read from `key.properties` (as seen in  `android/app/build.gradle.kts)`.

1. **Generate a Keystore (if you haven't already) using the terminal:**
	```bash
	keytool -genkey -v -keystore upload-keystore.jks -keyalg RSA -keysize 2048 -validity 10000 -alias upload
	```
	(Note: Save your keystore file `upload-keystore.jks` in the `android/app/` folder).


2. **Create android/key.properties In the android/ directory, create a file named key.properties with the following content:**
	```bash
	storePassword=YOUR_STORE_PASSWORD
    keyPassword=YOUR_KEY_PASSWORD
	keyAlias=upload
	storeFile=upload-keystore.jks
	```
	(Note: `key.properties` and keystore files are automatically ignored by your  `android/.gitignore` so they won't be exposed in Git).



# Step 3: Update Version Code & Version Name
Before building, update your version in  `pubspec.yaml:`
```bash
version: 1.0.17+18
```
• **Version Name (1.0.17):** The public version displayed to users.
• **Version Code (+18):** An integer that must be incremented by 1 (`19`, `20`, etc.) every single time you upload a new update to the Play Console.



# Step 4: Build the Release App Bundle (`.aab`)

Google Play requires apps to be submitted in the Android App Bundle format (`.aab`).

Run the release build command in your terminal:
```bash
flutter build appbundle --release
```


Once the build finishes successfully, your production-ready App Bundle will be located at:
```
build/app/outputs/bundle/release/app-release.aab
```



# Step 5: Publish to Google Play Console
1. **Go to Play Console:** Log in to the Google Play Console.
2. **Select Your App:** Select your Arunachal ILP app (`com.kemjet.ilp_user`).
3. **Create a Release:**
	◦ Go to **Testing and releases** > **Production** (or **Internal testing** / **Closed testing** first to test).
	◦ Click **Create new release.**

4. **Upload the `.aab` File:**
	◦ Upload the `app-release.aab` file generated in Step 4.
	◦ Add release notes describing what's new in this version.

5. **Complete Store Requirements (If first release):**
	◦ **Store listing:** App name, short description, full description, app icon, feature graphic, and screenshots.
	◦ **Content rating:** Complete the questionnaire.
	◦ **Data safety:** Fill out the data collection and privacy policy forms.
	◦ **Target audience & content:** Set age ratings and target audience.

6. **Review and Roll Out:**
	◦ Click **Save**, then **Review release**, and finally **Start rollout to Production.**

Google will review your app, and once approved, it will be live on the Google Play Store!