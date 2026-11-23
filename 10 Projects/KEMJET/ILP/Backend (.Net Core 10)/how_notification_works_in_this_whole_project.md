

### How it works

A notification has two parts, and the push is only a nudge.

1. Something happens in the backend: a permit is approved, a payment fails, a permit is about to expire.
2. The backend first writes a row to the notifications table. That row is the real notification and what the bell screen shows. A unique key on account and dedupe key, written with `ON CONFLICT DO NOTHING`, stops duplicates.
3. Second, it calls `IPushSender` (`FcmPushSender` in production), which sends an FCM message to each registered device token for that account.
4. A push failure never fails the original action. It is logged, and a dead token is removed.
5. The app receives the push, and the user taps it. The app opens the right screen and calls the API to load the real data. The push payload is only a hint and must not be trusted as data.

If the phone never gets the push, the notification is still in the bell list. That is by design.

### Firebase side

- Use the FCM HTTP v1 API with a service account. The old server key is gone and the project does not use it.
- In Google Cloud IAM for your Firebase project, create a service account with the role **Firebase Cloud Messaging API Admin**. Download its JSON key once, to a file on your machine.
- Never paste that JSON into chat, appsettings or the repo.
- Register each Flutter app in the Firebase project: Android with its package name, iOS with its bundle ID. Each flavor (Arunachal, Nagaland, partner) is a separate app registration.
- For iOS, create an APNs Auth Key (.p8) in your Apple developer account and upload it under Firebase Project settings > Cloud Messaging. Without it, iOS gets no pushes, even though Android works.
- Nagaland: the delivery plan says it gets its own Firebase project per flavor. Tokens belong to one Firebase project, so a token from the Nagaland app can only be sent by the Nagaland project's service account.

### Azure side

The config keys are `Push__Provider=fcm`, `Push__Fcm__ProjectId=<firebase project id>` and `Push__Fcm__ServiceAccountJson=<the JSON>`. The double underscore works as a nested key in .NET.

1. Put the JSON in Key Vault from the file, so it never appears in chat or history. For example: `az keyvault secret set --vault-name <kv> --name push-fcm-sa-json --file sa.json`. Then delete the local file.
2. Give the Container App's managed identity the Key Vault Secrets User role.
3. Add a Container App secret with `keyvaultref` pointing to that Key Vault secret. Expose it as the env var `Push__Fcm__ServiceAccountJson` with `secretref`. Set `Push__Provider` and `Push__Fcm__ProjectId` as plain env vars.
4. Create a new revision. If `Push__Provider` is missing or `development`, the app silently uses `LoggingPushSender`. Pushes then only appear in logs and never reach a phone. This is the most common reason a push seems to do nothing.
5. The container needs outbound HTTPS to `fcm.googleapis.com` and `oauth2.googleapis.com`. With VNet integration, check that your NSG, firewall or NAT does not block them.
6. The hourly permit-expiry alerts come from `PermitSweepService`. If that runs in a separate Worker app, the Worker needs the same three push settings. The Azure plan lists the Worker Dockerfile as an open gap, so check whether it is deployed at all.
7. Keep a separate Key Vault and Firebase project for dev and prod, so test devices never mix with real users.

The first end-to-end test is to register a real device and trigger one event. Then check the logs for the FCM response. Success shows a message name, and `UNREGISTERED` means a dead token.

### Backend checklist

- The device token table exists. Partner side: `ilp.partner_devices`, with at most 10 per account and a token owned by one account. Endpoints are `POST /devices` and `DELETE /devices`.
- The notification endpoints work: `GET /notifications`, mark one read, mark all read, and an `unread_count` for the badge.
- The push `data` payload carries only `type` and an id, never personal data. Keep Aadhaar and phone numbers out of it.
- The user's notification settings are checked before sending.
- Dead tokens (`UNREGISTERED`, `INVALID_ARGUMENT`) are deleted, and this is already built.
- Check the DB has the unique dedupe constraint, or the hourly sweep will spam users.

### Flutter checklist

1. Add `firebase_core` and `firebase_messaging`. Run `flutterfire configure` per flavor so each gets its own `google-services.json` and `GoogleService-Info.plist`.
2. Android 13 and above need the `POST_NOTIFICATIONS` runtime permission. Ask for it at a sensible moment, not on first launch. iOS needs `requestPermission` plus the Push Notifications and Background Modes capabilities in Xcode.
3. Create a notification channel on Android for foreground display. FCM does not show a banner while the app is open, so use `flutter_local_notifications` for that case.
4. Get the token with `getToken()` after sign-in and on every app start. Listen to `onTokenRefresh` and re-register each time.
5. Partner app: send the token to `POST /devices` after login. On sign-out, call `DELETE /devices` before clearing the session, or the next user on that phone gets the previous user's pushes.
6. Handle all three cases through one navigator key: foreground (`onMessage`), background tap (`onMessageOpenedApp`), and terminated tap (`getInitialMessage`). Route by `type` and id, then refetch from the API.
7. Register the background handler as a top-level function with `@pragma('vm:entry-point')`.
8. Show the badge from the API's `unread_count`, not a local counter.
9. Test on a real iPhone. The iOS simulator is unreliable for push.

### Known gaps

- Tourist app: `/me/devices` is not built. The FCM token currently travels in `PUT /profile`. Adding a proper tourist devices endpoint is the next backend ticket.
- The scanner app has no push, which is fine for now.
- Some partner events (staff-permit rejection, payout, document expiry) have no push yet. SMS and email delivery are also not wired, and SMS needs DLT templates for India.
- Per-state Firebase projects: I could not confirm whether `FcmPushSender` supports one config per state or just one global one. If it is global, Nagaland and Arunachal share one Firebase project for now, or `Push:Fcm` becomes a per-state setting. Please check `FcmPushSender` and how it is registered in DI.