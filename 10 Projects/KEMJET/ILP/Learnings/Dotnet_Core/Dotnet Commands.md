

```bash
docker compose -f deploy/compose/docker-compose.dev.yml up -d
dotnet build; dotnet test
dotnet tool install --global dotnet-ef
dotnet ef migrations add InitialIdentity --project src/Kemjet.Ilp.DAL --startup-project src/Kemjet.Ilp.Api --output-dir Migrations
dotnet ef database update --project src/Kemjet.Ilp.DAL --startup-project src/Kemjet.Ilp.Api
dotnet run --project src/Kemjet.Ilp.Api --launch-profile http
```


```bash
dotnet restore --force-evaluate
```

```bash
dotnet tool restore
dotnet restore --locked-mode|

dotnet build -c Release -p:TreatWarningsAsErrors=true
dotnet format --verify-no-changes
dotnet test

```

powershell -ExecutionPolicy Bypass -File scripts\lan-release.ps1                            