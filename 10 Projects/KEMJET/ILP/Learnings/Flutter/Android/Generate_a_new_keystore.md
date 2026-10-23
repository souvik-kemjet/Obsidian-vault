

## Step 1: Generate a new keystore

Open PowerShell in your Flutter project root and run:
```powershell
keytool -genkeypair -v `
  -keystore android\app\upload-keystore-new.jks `
  -keyalg RSA `
  -keysize 2048 `
  -validity 10000 `
  -alias upload
```


If `keytool` isn't recognized, use Android Studio's bundled Java:
```powershell
& "C:\Program Files\Android\Android Studio\jbr\bin\keytool.exe" -genkeypair -v -keystore "android\app\upload-keystore-new.jks" -keyalg RSA -keysize 2048 -validity 10000 -alias upload
```



### Verify Keystore File
```bash
keytool -list -v -keystore "android\app\upload-keystore.jks"
```