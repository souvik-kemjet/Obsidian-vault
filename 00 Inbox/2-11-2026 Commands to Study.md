
# Command 1 
```powershell
New-NetFirewallRule -DisplayName "Kemjet ILP API 8080" -Direction Inbound -Protocol TCP -LocalPort 8080 -Action Allow -Profile Private

```

1. **Open port 8080 in Windows Firewall.** Run this in an **admin** PowerShell:
    
    ```powershell
    New-NetFirewallRule -DisplayName "Kemjet ILP API 8080" -Direction Inbound -Protocol TCP -LocalPort 8080 -Action Allow -Profile Private
    ```
    
    My test came from your own laptop, which the firewall doesn't block. Your teammates will be blocked until this rule exists.
2. **Set your Wi-Fi network to Private:** Settings → Network & Internet → Wi-Fi → your network → **Private network**. The rule above only applies on Private networks.
3. **Send your team this link:** `http://192.168.1.7:8080/swagger`. Your IP can change after a reconnect, so check it with `ipconfig` (the Wi-Fi IPv4 address). Setting a fixed IP for your laptop in the router stops it changing.

---

# Command 2 
### Daily Use
```
# start (rebuilds after code changes)
docker compose -f deploy/compose/docker-compose.lan.yml up -d --build
# logs
docker compose -f deploy/compose/docker-compose.lan.yml logs -f api
# stop
docker compose -f deploy/compose/docker-compose.lan.yml down

```


# Command 3 

```bash
dotnet ef database update --project src/Kemjet.Ilp.DAL --startup-project src/Kemjet.Ilp.Api --connection "Host=localhost;Port=5433;Database=ilp_nl;Username=ilp_dev;Password=ilp_dev_only"

```


---

# Command 4

**Option 1: mark the Wi-Fi as Private (recommended for your home Wi-Fi)** Settings → Network & Internet → Wi-Fi → click **Airtel_souv_5701 2** → set "Network profile type" to **Private network**.

Or in an **admin** PowerShell:

```powershell
Set-NetConnectionProfile -InterfaceAlias "Wi-Fi" -NetworkCategory Private
```

**Option 2: keep the network Public and make the rule apply there too** This also opens port 8080 whenever you're on café or office Wi-Fi, so use it only if Option 1 isn't possible. In an **admin** PowerShell:

```powershell
Set-NetFirewallRule -DisplayName "Kemjet ILP API 8080" -Profile Private,Public
```

