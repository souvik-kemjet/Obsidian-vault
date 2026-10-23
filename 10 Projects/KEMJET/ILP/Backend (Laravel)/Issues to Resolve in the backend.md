

- **Critical:**
    - Nothing limits repeated login or Aadhaar code attempts.
    - Every permit QR code contains the full Aadhaar numbers of the whole group. Every checkpoint officer’s login receives the single shared key that unlocks them (`IlpQrCodeService.php`, `DefenceAuthService.php:77`).
    - Permits are generated inside the payment notification itself. If that fails, the error is hidden, so a tourist can pay and get no permit (`GenerateIlpsForApplication.php`, `PaymentController.php`).
- **High:**
    - Login tokens never expire and there is no logout.
    - Co-travellers are typed in and never Aadhaar-verified.
    - Uploaded photos aren’t checked.
    - Full Aadhaar provider responses are written to logs and kept for 30 days.
    - Live Aadhaar calls are on by default (`SANDBOX_LIVE`).