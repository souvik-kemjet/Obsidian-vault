

```
Flutter app                 Your Laravel backend                 Sandbox.co.in
───────────                 ────────────────────                 ─────────────
tap "Verify Aadhaar" ───▶   POST /api/kyc/digilocker/sdk/sessions/create
                            (save session_id against this user)  ──▶ create session
         ◀── session_id ──
open DigiLocker SDK ──────────────────────────────────────────▶ user logs in to DigiLocker,
                                                                  enters Aadhaar OTP, consents
event: session.completed
         ──▶  POST /api/kyc/digilocker/sdk/sessions/{id}/complete
                            GET  .../sessions/{id}/status   ──▶ must be "succeeded"
                            GET  .../sessions/{id}/documents/aadhaar
                            download XML (link expires in 1 hour), parse it,
                            store the verified identity
         ◀── verified name / DOB / photo

```

The key rule is that the app only reports that the flow ended. Your backend decides whether verification succeeded. Never trust a "verified" flag that comes from the app.



