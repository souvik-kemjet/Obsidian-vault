

|Command|Purpose|
|---|---|
|`docker ps`|Show running containers|
|`docker ps -a`|Show all containers|
|`docker compose -f deploy/compose/docker-compose.dev.yml logs`|View logs|
|`docker compose -f deploy/compose/docker-compose.dev.yml logs -f`|Follow logs live|
|`docker compose -f deploy/compose/docker-compose.dev.yml stop`|Stop containers|
|`docker compose -f deploy/compose/docker-compose.dev.yml down`|Stop and remove containers and Compose networks|
|`docker compose -f deploy/compose/docker-compose.dev.yml restart`|Restart services|
```powershell

docker ps -a --filter "name=postgres" --format "table {{.Names}}\t{{.Status}}\t{{.Ports}}"
```


```powershell
Docker inspect kmejet-ilp-lan --format "{{range .Config.Env}}{{println .}}{{end}}"
```


```powershell
docker run --rm --network kemjet-ilp-lan_default postgres:16-alpine pg_isready -h postgres -p 5432
```

```powershell
docker run --rm --network kemjet-ilp-lan_default postgres:16-alpine pg_isready -h postgres -p 5432
```

```powershell
docker start kemjet-ilp-lan-postgres-1
```


```powershell

docker run -d --name kmejet-ilp-lan `
  -p 8080:8080 `
  --env-file C:\secure\kemjet-ilp.env `
  --restart unless-stopped `
  kemjet-ilp-api:latest

```


```powershell
docker network connect kemjet-ilp-lan_default kmejet-ilp-lan
```

```powershell
docker network create kemjet-net
```

```powershell
docker run -d --name kemjet-ilp-db `
  --network kemjet-net `
  -v kemjet-ilp-db-data:/var/lib/postgresql/data `
  -e POSTGRES_PASSWORD="YOUR_PASSWORD" `
  --restart unless-stopped `
  postgres:16-alpine

```

```powershell
docker run -d --name kmejet-ilp-lan --network kemjet-net `
  -p 8080:8080 --env-file C:\secure\kemjet-ilp.env `
  --restart unless-stopped kemjet-ilp-api:latest
```

```powershell
docker exec -it <container_name_or_id> bash
```

```powershell
docker compose -f deploy/compose/docker-compose.lan.yml restart api
docker compose -f deploy/compose/docker-compose.lan.yml up -d
```


---



