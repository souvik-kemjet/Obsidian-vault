
Here’s how the LAN release gets its configuration:

- `lan-release.ps1` sets `LAN_IP` and `KYC_PUBLIC_BASE_URL`; it does not set the MSG91 key.
- `docker-compose.lan.yml` reads `SMS_PROVIDER`, `MSG91_AUTH_KEY`, and related values through Compose variable substitution. There is a `.env` beside that compose file, which is the likely source.
- The LAN config uses demo KYC and development JWT settings, but **your current `.env` selects MSG91**, so the LAN container may send real SMS. The compose file also has hard-coded development database credentials; don’t reuse those for production.
- Docker containers do not automatically get your `dotnet user-secrets`.

**What to do now:**

1. If the MSG91 key may still be valid, treat it as an active credential. Check the MSG91 dashboard and rotate it if you’re unsure who has had access. Don’t paste it into chat or source control.
2. For a demo-only LAN run, set `SMS_PROVIDER=development` and remove/blank the MSG91 credential entries in `.env`.
3. For local `dotnet run`, secrets are stored outside the repo in your Windows user profile by `dotnet user-secrets`. The API’s secrets ID is in `Kemjet.Ilp.Api.csproj`. Be careful: `dotnet user-secrets list` prints values.
4. For a regular Docker server deployment, follow `secrets-and-configuration.md`: it documents server-side secret files mounted at `/run/secrets`.
5. For Azure, use **Azure Key Vault** with a managed identity. Make the secrets available to the container as environment settings or mounted files. The app already reads environment variables and `/run/secrets`; it is **not currently wired to fetch directly from Key Vault**.