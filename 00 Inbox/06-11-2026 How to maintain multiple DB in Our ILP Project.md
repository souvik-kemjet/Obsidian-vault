
_You **can** put a connection string in `appsettings.Development.json` for local development, but don’t put real database passwords in tracked `appsettings.json`. This project already uses that pattern: the local Compose database credentials are defined in `docker-compose.dev.yml`, and the API’s tenant connection string is configured in `appsettings.Development.json`._


## **One local database (Nagaland):**
the Compose file sets the local password to `ilp_dev_only`, so the development setting should match:

```yaml
"Tenancy": {
  "Mode": "Single",
  "DefaultTenant": "NL",
  "Tenants": {
    "NL": {
      "Code": "NL",
      "DisplayName": "Nagaland",
      "TimeZone": "Asia/Kolkata",
      "Hosts": [],
      "ConnectionString": "Host=localhost;Port=5432;Database=ilp_nl;Username=ilp_dev;Password=ilp_dev_only"
    }
  }
}
```


For a real password—or if you want to avoid storing even a development password in a JSON file—set it in .NET User Secrets instead:
```bash
dotnet user-secrets set "Tenancy:Tenants:NL:ConnectionString" "Host=localhost;Port=5432;Database=ilp_nl;Username=ilp_dev;Password=your-password" --project src/Kemjet.Ilp.Api
```


---

## **Multiple databases (Arunachal and Mizoram):**
this project supports `Multi` mode, where it chooses the tenant by matching the request’s host name against each tenant’s `Hosts` list. Each tenant can point to a different database:

```yaml
"Tenancy": {
  "Mode": "Multi",
  "DefaultTenant": "AR",
  "Tenants": {
    "AR": {
      "Code": "AR",
      "DisplayName": "Arunachal Pradesh",
      "TimeZone": "Asia/Kolkata",
      "Hosts": [ "arunachal.example.com" ],
      "ConnectionString": "Host=ar-db.example.com;Port=5432;Database=ilp_ar;Username=ilp_app;Password=<ARUNACHAL_PASSWORD>"
    },
    "MZ": {
      "Code": "MZ",
      "DisplayName": "Mizoram",
      "TimeZone": "Asia/Kolkata",
      "Hosts": [ "mizoram.example.com" ],
      "ConnectionString": "Host=mizoram-db.example.com;Port=5432;Database=ilp_mz;Username=ilp_app;Password=<MIZORAM_PASSWORD>"
    }
  }
}
```


Replace the example hosts, database addresses, and password placeholders with your actual values. In `Multi` mode, requests must use a hostname listed for the intended tenant; for local testing you can use distinct names such as `ar.localhost` and `mz.localhost`, rather than using `localhost` for both. The tenant configuration shape and host-based selection are defined in `Tenancy.cs` and `TenancySetup.cs`.


