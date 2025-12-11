# Docker Commands & Notes

## CLI Structure

All Docker commands follow this pattern:

```
docker <command> <sub-command> [options] [args]
```

Example:

```bash
docker run -d -p 3000:3000 myapp
```

---

## Container Management

### `docker run` — Run a container

```
docker run [OPTIONS] IMAGE [COMMAND]
```

**Common Options:**

| Option                | Abbreviation      | Meaning                                               |
| --------------------- | ----------------- | ----------------------------------------------------- |
| `-d`                  | detached          | Run container in background (doesn't occupy terminal) |
| `-it`                 | interactive + tty | Allow terminal input to container (use with sh/bash)  |
| `--name <name>`       | -                 | Set container name                                    |
| `-p host:container`   | publish           | Map host port → container port                        |
| `-e KEY=value`        | env               | Set environment variable                              |
| `--env-file file.env` | -                 | Load variables from .env file                         |
| `-v host:container`   | volume            | Attach volume or bind mount                           |
| `--rm`                | remove            | Auto-remove container when stopped                    |
| `--network <name>`    | -                 | Attach container to network                           |
| `--restart <policy>`  | -                 | Auto-restart container if crashed                     |

**Example:**

```bash
docker run -d --name api -p 3000:3000 myapp
```

**Restart policies:**

- `no` — Never restart (default)
- `on-failure` — Restart if exit code is non-zero
- `always` — Always restart
- `unless-stopped` — Always restart unless manually stopped

**Example with restart policy:**

```bash
docker run -d --name api --restart always nginx
```

### `docker ps` — View containers

| Command        | Meaning                                 |
| -------------- | --------------------------------------- |
| `docker ps`    | View running containers                 |
| `docker ps -a` | View all containers (including stopped) |
| `docker ps -q` | List container IDs in short format      |

**Options:**

- `-a` = **all**
- `-q` = **quiet** (only show IDs)

### `docker stop / start / restart`

```bash
docker stop <container>
docker start <container>
docker restart <container>
```

**Note:** `stop` sends SIGTERM signal → SIGKILL after 10s

### `docker pause / unpause` — Pause/unpause container

```bash
docker pause <container>
docker unpause <container>
```

Freezes all processes in the container (using cgroups).

### `docker wait` — Wait for container to stop

```bash
docker wait <container>
```

Blocks until container stops, then prints exit code.

### `docker rm` — Remove container

```bash
docker rm <container>
```

**Options:**

- `-f` = **force** (remove even if container is running)

### `docker exec` — Run command inside container

```bash
docker exec [OPTIONS] CONTAINER COMMAND
```

**Options:**

| Option | Abbreviation      | Meaning                   |
| ------ | ----------------- | ------------------------- |
| `-it`  | interactive + tty | Open shell into container |
| `-d`   | detached          | Run command in background |

**Example:**

```bash
docker exec -it myapp sh
```

### `docker logs` — View container logs

```bash
docker logs <container>
```

**Options:**

- `-f` = **follow** (like `tail -f`)
- `--since` = logs from specific time
- `-t` = show **timestamps**

---

## Image Management

### `docker build` — Build image

```bash
docker build [OPTIONS] PATH
```

**Important Options:**

| Option                | Abbreviation | Meaning                         |
| --------------------- | ------------ | ------------------------------- |
| `-t name:tag`         | tag          | Set name & tag for image        |
| `-f Dockerfile`       | file         | Specify different Dockerfile    |
| `--no-cache`          | -            | Build without using cache       |
| `--build-arg KEY=VAL` | -            | Pass ARG variable to Dockerfile |

**Example:**

```bash
docker build -t myapp:v1 .
```

### `docker images` — List images

```bash
docker images
```

**Options:**

- `-a` = view intermediate images
- `--digests` = show digest
- `--no-trunc` = don't truncate ID

**Filter images:**

```bash
docker images -f "dangling=true"  # Show untagged images
docker images -f "reference=node:*"  # Show images matching pattern
```

### `docker rmi` — Remove image

```bash
docker rmi <image>
```

**Options:**

- `-f` = **force** (remove even if image is being used by container)

### `docker pull` — Pull image from registry

```bash
docker pull nginx
docker pull ubuntu:22.04
```

### `docker push` — Push image to registry

```bash
docker push username/app:v1
```

**Note:** Login first with `docker login`

### `docker tag` — Tag an image

```bash
docker tag SOURCE_IMAGE[:TAG] TARGET_IMAGE[:TAG]
```

**Example:**

```bash
docker tag myapp:v1 username/myapp:v1
docker tag myapp:latest myapp:v2
```

### `docker save / load` — Export/Import images

```bash
docker save -o myapp.tar myapp:v1
docker load -i myapp.tar
```

**Use case:** Transfer images without a registry

## Dockerfile Concepts

### Common Dockerfile Instructions

| Instruction  | Purpose                                         |
| ------------ | ----------------------------------------------- |
| `FROM`       | Base image                                      |
| `WORKDIR`    | Set working directory                           |
| `COPY`       | Copy files from host to image                   |
| `ADD`        | Like COPY but can extract tar and download URLs |
| `RUN`        | Execute commands during build                   |
| `CMD`        | Default command when container starts           |
| `ENTRYPOINT` | Main command (harder to override than CMD)      |
| `ENV`        | Set environment variables                       |
| `ARG`        | Build-time variables                            |
| `EXPOSE`     | Document which ports container listens on       |
| `VOLUME`     | Create mount point                              |
| `USER`       | Set user for RUN, CMD, ENTRYPOINT               |
| `LABEL`      | Add metadata to image                           |

**Example Dockerfile:**

```dockerfile
FROM node:18-alpine
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
EXPOSE 3000
CMD ["npm", "start"]
```

### CMD Instruction

The `CMD` instruction in a Dockerfile is **not executed** when the image is built. It only runs when a container starts from that image.

**CMD vs ENTRYPOINT:**

- `CMD` — easily overridden by `docker run` arguments
- `ENTRYPOINT` — main executable, CMD becomes arguments to it

**Example:**

```dockerfile
# CMD format
CMD ["npm", "start"]

# ENTRYPOINT + CMD format
ENTRYPOINT ["npm"]
CMD ["start"]
```

### Docker Cache Optimization

**Note:** `export` flattens the image (loses history), while `save` preserves layers

---

## Dockerfile Concepts

### CMD Instruction

The `CMD` instruction in a Dockerfile is **not executed** when the image is built. It only runs when a container starts from that image.

### Docker Cache Optimization

Docker caches build steps to speed up rebuilds. If a step hasn't changed, Docker reuses the cached result instead of re-running it.

**Optimized approach:**

```dockerfile
COPY package.json .
RUN npm install
COPY . .
```

**Why is this faster?**

Docker only re-runs `npm install` when `package.json` changes.

**If you only modify `.js` files (not `package.json`):**

- `COPY package.json .` → unchanged → **uses cache**
- `RUN npm install` → skipped → **uses cache**
- `COPY . .` → only copies new code

---

## Network Management

### `docker network` — Manage networks

| Command                         | Meaning            |
| ------------------------------- | ------------------ |
| `docker network ls`             | View networks      |
| `docker network inspect <name>` | View details       |
| `docker network rm <name>`      | Remove network     |
| `docker network create <name>`  | Create new network |

**Connect/Disconnect container:**

```bash
docker network connect <network> <container>
docker network disconnect <network> <container>
```

**Network drivers:**

- `bridge` (default) — isolated network on host
- `host` — use host's network directly
- `none` — no networking

**Example:**

```bash
docker network create --driver bridge my-network
docker run -d --network my-network nginx
```

---

## Volume Management

### `docker volume` — Manage volumes

| Command                        | Meaning          |
| ------------------------------ | ---------------- |
| `docker volume ls`             | View all volumes |
| `docker volume rm <name>`      | Remove volume    |
| `docker volume inspect <name>` | View details     |

**Create volume:**

```bash
docker volume create my-volume
```

**Remove unused volumes:**

```bash
docker volume prune
```

**Volume types:**

- **Named volumes:** Managed by Docker (`docker volume create`)
- **Anonymous volumes:** Created automatically, harder to reference
- **Bind mounts:** Mount host directory (`-v /host/path:/container/path`)

**Example:**

````bash
## Docker Compose

| Command                       | Meaning                             |
| ----------------------------- | ----------------------------------- |
| `docker compose up`           | Run all services                    |
| `docker compose up -d`        | Run in background                   |
| `docker compose down`         | Stop + remove containers            |
| `docker compose down -v`      | Stop + remove containers + volumes  |
| `docker compose logs -f`      | View logs                           |
| `docker compose build`        | Rebuild images                      |
| `docker compose ps`           | List containers                     |
| `docker compose exec <svc> sh`| Execute command in service          |
| `docker compose restart`      | Restart services                    |
| `docker compose stop`         | Stop services (don't remove)        |
### `docker system` — Clean up

| Command                  | Meaning                                  |
| ------------------------ | ---------------------------------------- |
| `docker system df`       | View disk usage                          |
| `docker system prune`    | Remove unused containers + images        |
| `docker system prune -a` | Remove everything unused (very powerful) |

**Prune specific resources:**

```bash
docker container prune  # Remove stopped containers
docker image prune      # Remove dangling images
docker image prune -a   # Remove all unused images
docker volume prune     # Remove unused volumes
docker network prune    # Remove unused networks
````

### `docker info` — System information

```bash
docker info
```

Shows Docker version, storage driver, running containers, images count, etc.

### `docker version` — Docker version

```bash
docker version
```

Shows client and server version details.
docker compose logs -f api
docker compose exec api sh

````

```bash
docker inspect <container|image>
````

### `docker system` — Clean up

| Command                  | Meaning                                  |
| ------------------------ | ---------------------------------------- |
| `docker system df`       | View disk usage                          |
| `docker system prune`    | Remove unused containers + images        |
| `docker system prune -a` | Remove everything unused (very powerful) |

---

## Docker Compose

| Command                  | Meaning                  |
| ------------------------ | ------------------------ |
| `docker compose up`      | Run all services         |
| `docker compose up -d`   | Run in background        |
| `docker compose down`    | Stop + remove containers |
| `docker compose logs -f` | View logs                |
| `docker compose build`   | Rebuild                  |

---

## Common Flags Explained

| Flag | Abbreviation | Real Meaning                                 |
| ---- | ------------ | -------------------------------------------- |
| `-d` | detached     | Detach from terminal                         |
| `-i` | interactive  | Interactive mode (accept input)              |
| `-t` | tty          | Create pseudo-terminal                       |
| `-p` | publish      | Expose port to host                          |
| `-v` | volume       | Attach volume                                |
| `-f` | file/force   | Depends on command: file (build), force (rm) |
| `-q` | quiet        | Only show IDs                                |
| `-a` | all          | Show all (including stopped/intermediate)    |
