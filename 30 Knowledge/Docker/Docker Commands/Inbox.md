

```bash
docker compose -f ilp-platform-backend/deploy/compose/docker-compose.lan.yml exec -T postgres psql -U ilp_dev -d ilp_nl
```


```bash
docker compose -f ilp-platform-backend/deploy/compose/docker-compose.lan.yml exec postgres psql -U ilp_dev -l
```