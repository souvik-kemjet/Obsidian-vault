

|Command|Purpose|
|---|---|
|`docker ps`|Show running containers|
|`docker ps -a`|Show all containers|
|`docker compose -f deploy/compose/docker-compose.dev.yml logs`|View logs|
|`docker compose -f deploy/compose/docker-compose.dev.yml logs -f`|Follow logs live|
|`docker compose -f deploy/compose/docker-compose.dev.yml stop`|Stop containers|
|`docker compose -f deploy/compose/docker-compose.dev.yml down`|Stop and remove containers and Compose networks|
|`docker compose -f deploy/compose/docker-compose.dev.yml restart`|Restart services|