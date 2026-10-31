---
title: Integrate Laravel DigiLocker session API
tags:
  - Laravel
  - Digilocker
  - api
  - Intergration
---
## Scope
- Call POST /api/kyc/digilocker/sdk/sessions/create.
- Use the existing authenticated API client.
- Parse and validate the returned session ID.
- Handle timeouts, unauthorized requests, and server errors.
  Acceptance criteria
- [ ] Session creation succeeds in sandbox.
- [ ] Invalid responses produce meaningful errors.
- [ ] No Sandbox provider credentials are sent from Flutter.

---

