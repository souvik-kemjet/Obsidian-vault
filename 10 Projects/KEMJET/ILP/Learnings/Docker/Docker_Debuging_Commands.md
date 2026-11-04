

# Debug it step by step
---
### **1. Is the DB up and healthy?**

##### `docker compose -p kemjet-ilp-lan ps`

- **`docker compose`**: Calls the modern Docker Compose CLI tool. It manages multi-container applications defined in a `docker-compose.yml` file.

- **`-p kemjet-ilp-lan`**: The `-p` (or `--project-name`) flag explicitly specifies the **project name** as `kemjet-ilp-lan` . By default, Docker Compose uses the name of the folder the file is in . Using this flag forces Docker to look for or manage containers linked specifically to this unique project identifier, regardless of what directory you are currently standing in within your terminal.

- **`ps`**: Lists the containers associated with that project, showing details like their container IDs, names, current running status (e.g., _Up 2 hours_, _Exited_), ports mapped to your system, and health status.
```
`docker compose -p kemjet-ilp-lan ps`
```

---

##### `docker inspect kemjet-ilp-lan-postgres-1 --format "{{.State.Health.Status}}"`

**🔍 Expected Outputs**
- **`healthy`**: The database is running optimally, and its internal health check script (like `pg_isready`) is successfully responding .
- **`unhealthy`**: The container is running, but its internal health check is failing. This usually means the database engine has crashed, is out of memory, or isn't accepting connections.
- **`starting`**: The container has just booted up, and Docker is waiting for the initial warm-up period to pass before executing the first health check.

1. `docker inspect`
	This is a low-level diagnostic tool used to view deep configuration data for Docker objects (containers, volumes, networks, or images). By default, running `docker inspect <container-name>` dumps a massive JSON array containing hundreds of lines of technical details.

2. `kemjet-ilp-lan-postgres-1`
	This is the **target container name** you are interrogating. Based on its naming structure, this is the PostgreSQL database container belonging to the `kemjet-ilp-lan` Docker Compose project deployment you checked earlier.

3. `--format` (or `-f`)
	This is the filter flag. It tells Docker: _"Do not show me the whole JSON file. I am going to give you a specific path using a Go template string, and I only want you to print the value found at that path."_ 

4. `"{{.State.Health.Status}}"`
	This is the **Go template path** that drills down into the container's structural data tree. Docker looks at the root object (`.`), goes into the `State` category, opens the `Health` sub-category, and extracts the raw value of `Status`.

---

##### `docker logs --tail 30 kemjet-ilp-lan-postgres-1`

**🔍 Command Breakdown**
- **`docker logs`**: The core Docker command used to retrieve the console output (`stdout` and `stderr`) that a container has generated since it started running.
- **`--tail 30`**: The filter flag that modifies the output. It tells Docker: _"Start reading from the absolute bottom of the log history, count backward, and show me **only the last 30 lines**."_
- **`kemjet-ilp-lan-postgres-1`**: The specific target container name. This is the [PostgreSQL](https://www.postgresql.org/) database instance supporting your `kemjet-ilp-lan` environment.
  
**💡 Why Developers Run This Command**
1. **Successful Initialization:** Lines showing that the database is fully booted and ready to accept connections (e.g., `database system is ready to accept connections`).
2. **Authentication Errors:** Logs indicating that your application backend is passing the wrong credentials (e.g., `FATAL: password authentication failed for user "postgres"`).
3. **Crash Reports:** Messages explaining why the database suddenly stopped or restarted (e.g., `PANIC`, `out of memory`, or `database system was not properly shut down`).


---

# **2. Which connection string does the API actually have?**
This prints the dev password; that's fine here because it's the local-only value.
```powershell
docker exec kemjet-ilp-lan-api-1 printenv | Select-String "Tenancy|ASPNETCORE|Sms"
docker compose -p kemjet-ilp-lan config     # the fully resolved compose settings
```
Expect `Tenancy__Tenants__NL__ConnectionString=Host=postgres;Port=5432;...`.


---

# **3. Are both containers on the same network?**
```powershell
docker network ls
docker network inspect kemjet-ilp-lan_default --format "{{range .Containers}}{{.Name}} {{.IPv4Address}}{{println}}{{end}}"
```
Both `api-1` and `postgres-1` must be listed. `kemjet-ilp-db` won't be, which is why it's unreachable.

---

# **4. From inside the API container, can it find the DB by name and reach the port?**
```powershell
docker exec kemjet-ilp-lan-api-1 getent hosts postgres
docker exec kemjet-ilp-lan-api-1 bash -c "echo > /dev/tcp/postgres/5432 && echo PORT OPEN"
```
- No output from the first command means a **DNS / network problem**: wrong network or wrong name.
- An error from the second means the DB isn't listening or isn't ready.

---

# **5. Can it log in?** This runs a throwaway Postgres client on the same network:
```powershell
docker run --rm --network kemjet-ilp-lan_default postgres:16-alpine pg_isready -h postgres -U ilp_dev -d ilp_nl
```

---
# **6. Do the tables exist?**
```powershell
docker exec -it kemjet-ilp-lan-postgres-1 psql -U ilp_dev -d ilp_nl -c "\dn" -c "\dt ilp.*"
```
If no `ilp` schema or tables are listed, run the migration step against port 5433.

---

# **7. Is the stray DB unused?**
```powershell
docker inspect kemjet-ilp-db --format "{{json .NetworkSettings.Networks}} {{json .Mounts}}"
```

---

### How to read the API error

|Error in the API log|Meaning|Fix|
|---|---|---|
|`Connection refused` to `127.0.0.1`|Using `localhost` inside a container|Use `Host=postgres`|
|`Name or service not known` / `Resource temporarily unavailable`|The host name doesn't exist on this network|Same network and the correct name (step 3)|
|`Connection refused` to `postgres`|DB not running or not ready yet|Step 1|
|`password authentication failed`|Wrong user or password, or an old volume created with other credentials|Check step 2 against the compose file|
|`database "ilp_nl" does not exist`|The volume was created without `POSTGRES_DB`|Recreate the volume or create the DB|
|`relation "ilp.otp_challenges" does not exist`|Connected fine, but no migrations|Run `dotnet ef database update` on 5433|
|Timeout|Firewall or wrong port|Port inside the network is **5432**; 5433 is only for your laptop|

## Going deeper

- **Docker Desktop:** click a container to get **Logs**, **Inspect** (env, network, mounts), **Exec** (a shell inside it) and **Stats**.
- **A full network toolbox** attached to the API's network, with `ping`, `dig`, `nc`, `curl` and `tcpdump`:
```powershell
    docker run --rm -it --network container:kemjet-ilp-lan-api-1 nicolaka/netshoot
```

- **Live Docker events** (container starts, stops, health changes):
```powershell
    docker events --filter "label=com.docker.compose.project=kemjet-ilp-lan"
```

- **Debugging the C# code with breakpoints** is easier outside Docker. Keep `postgres-1` running, stop only `api-1` (`docker compose -p kemjet-ilp-lan stop api`), and run the API from Visual Studio. Point it at `Host=localhost;Port=5433;...` using user-secrets (`Tenancy:Tenants:NL:ConnectionString`). Then you can step through `RequestOtpHandler` and see every query in the debug output.
- **See the SQL EF runs:** set `Logging__LogLevel__Microsoft.EntityFrameworkCore.Database.Command=Information` on the API.