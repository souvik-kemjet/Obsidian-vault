
## Phase 1 — Airtel DLT (paperwork, 1–3 days)

Indian telecom rules (TRAI) block any SMS that doesn't match a registered sender, template and delivery chain.

1. **Register your company** (the "entity") on the Airtel DLT portal (dltconnect.airtel.in). You get a 19-digit **Entity ID**.
2. **Register a sender ID** (called a "header"): 6 letters such as `KMJILP`, type **Transactional / Service-Implicit**.
3. **Register the OTP template** as a **Service-Implicit** content template linked to that sender ID. Example
```powershell
{#var#} is your OTP to log in to Nagaland ILP. It is valid for 5 minutes. Do not share it with anyone. - KEMJET
```
- Keep URLs and phone numbers out of it, since those need separate whitelisting.
- Write the 5 minutes to match `Otp:TtlSeconds = 300`.
- You get a **DLT Template ID**.

4. **Add MSG91 to your delivery chain** on the Airtel portal (the telemarketer chain / PE-TM binding). Ask MSG91 support for their current telemarketer ID. If this step is missing, MSG91 accepts the message but it is never delivered.
