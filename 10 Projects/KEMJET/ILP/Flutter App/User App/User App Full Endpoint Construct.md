

# **Full Endpoint Construct**
The application appends the user API path prefix `/_userApiPath` (`/user/api/v1`) to the base URL in  `ApiService`.

##### **The complete full URLs for the OTP operations are:**
• Send Login OTP: https://arunachal-ilp.ujudebug.in/user/api/v1/send-otp
• Verify Login OTP: https://arunachal-ilp.ujudebug.in/user/api/v1/verify-otp
• Initiate Aadhaar OTP: https://arunachal-ilp.ujudebug.in/user/api/v1/aadhaar/initiate
• Verify Aadhaar OTP: https://arunachal-ilp.ujudebug.in/user/api/v1/aadhaar/verify


# **Code Configuration Reference**

1. **App Initialization: Set globally via `MultiProvider` in  `lib/app/app.dart`:**
	```dart
	Provider<ApiService>(
	  create: (_) => ApiService(
	    baseUrl: 'https://arunachal-ilp.ujudebug.in',
	    token: storageService.getToken(),
	  ),
	)
	```

2. **Request Construction: Concatenated in  `lib/common/services/api_service.dart`**:
	```dart
	final uri = Uri.parse('$baseUrl$_userApiPath$endpoint');
	// Resolves to: https://arunachal-ilp.ujudebug.in/user/api/v1<endpoint>
	```
