
## KYC flow in Flutter

1. **Fetch configuration.** Call `/app-config`, then display the consent wording from `kyc.consent_text_url` and its current version.
2. **Get explicit consent.** The user reviews and agrees. Send `accepted: true`, that version, and the time they agreed when calling the authenticated `POST /kyc/sessions`. Do not send an Aadhaar number or OTP to this API.
3. **Start a KYC session.** The API returns a session ID, `pending` status, expiry time, and an `authorization_url`. Keep the session ID so Flutter can check that specific session later.
4. **Open the authorization URL.** Use a secure browser hand-off such as Android Custom Tabs or iOS `ASWebAuthenticationSession`, rather than an embedded WebView. The user signs in to DigiLocker and authorizes sharing there.
5. **Check the result.** When the browser closes, the app resumes, or a deep link returns to the app, call authenticated `GET /kyc/sessions/{id}`. A deep-link status is only a signal to refresh—the GET response is the authoritative result. While it is still `pending`, poll at a modest interval, stop at expiry, and refresh immediately when the app resumes.
6. **Handle the final status.** On `verified`, use the returned `holder_id` (and, where needed, the masked Aadhaar last four digits). On `failed`, show an appropriate user-facing message based on `failure_code`; on `expired`, let the user start again.
7. **Use verified holders in the application.** Fetch `/kyc/holders` to select the verified self and co-travellers. The backend requires every traveller on an application to be KYC-verified.


## What does `role: "self"` mean?

`role` identifies **whose identity is being verified**:

- `"self"`: the signed-in account holder is verifying their own identity.
- `"co_traveller"`: a different traveller, such as a family member, is being verified. The request may also include `relationship_with_primary`.

Use `"self"` for the logged-in user’s KYC—not for every person in a group. Each traveller who will be included in an application needs their own verified holder record. The API contract defines the two roles in `KycSessionCreateRequest`.