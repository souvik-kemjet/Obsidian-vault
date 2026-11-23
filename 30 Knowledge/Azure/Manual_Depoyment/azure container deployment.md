
```
cd ~ 
git clone https://github.com/KEM-JET-HIMALOG-Pvt-Ltd/kemjet-ilp-backend.git 
```

```
cd kemjet-ilp-backend/ilp-platform-backend 
```

```
grep -n "swaggerEnabled" src/Kemjet.Ilp.Api/Program.cs
```


```bash
az acr build -r acrkemjetc6d26 -t ilp-api:dev02 . 
```

```
az containerapp update -g rg-kemjet-ilp-dev -n ca-ilp-api-dev --image acrkemjetc6d26.azurecr.io/ilp-api:dev02 --set-env-vars "Swagger__Enabled=true" -o none
```