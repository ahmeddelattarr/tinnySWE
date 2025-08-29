

# 🐳 Docker Study Notes (Ahmed's Journey)

This is my own roadmap + notes for Docker, written in my way.  
Not boring docs style, just me trying to understand and explain things.  

---

## 🔥 Why Docker?
Okay so basically, Docker is like giving your fish 🐟 its own water 🌊 so it feels free everywhere.  
Instead of "it works on my laptop but not on server", with Docker we ship the *same environment* everywhere.  

- **Containers** → lightweight, share the host OS kernel  
- **VMs** → heavy, each has its own full OS  
- Think of it as:  
  - **Image** = recipe 🍳 (static)  
  - **Container** = the cooked dish 🍲 (running, alive)  

---

## 📝 Dockerfiles
Dockerfile = recipe for your app. One instruction per line.

- `FROM` → base image  
- `WORKDIR` → where to work inside container  
- `COPY` → copy files into container  
- `RUN` → run a command during build  
- `CMD` → default command to run container  
- `EXPOSE` → just a hint about port (doesn’t open it)  

Example (FastAPI app):
```dockerfile
FROM python:3.11-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY . .
EXPOSE 8000
CMD ["uvicorn","main:app","--host","0.0.0.0","--port","8000"]
````

---

## 🐙 Volumes, Networks, Logs

* **Volumes** = keep data alive even if container dies.
  Example: `-v mydata:/data` → named volume.

* **Networks** = containers can talk to each other if they are in the same network.
  For now with just one app, default network is fine.

* **Logs** = don’t panic, just run:

  ```
  docker logs -f <container_name>
  ```

---

## ⚡ Docker Compose

Compose = life saver. Instead of long `docker run` commands, you write one `docker-compose.yml`.

* **services** = containers (api, db, etc.)
* **volumes** = persistent storage
* **depends\_on** = startup order
* **environment** = config values

Run the whole system:

```
docker compose up -d
docker compose logs -f api
docker compose down
```

Example `docker-compose.yml` (just FastAPI app):

```yaml
version: "3.9"

services:
  api:
    build: .
    container_name: fastapi_ctr
    ports:
      - "8000:8000"
```

---

## 🛠️ Image Optimization (don’t be lazy here)

Things I must always remember:

1. **.dockerignore** → don’t copy `.git`, `.env`, junk.
2. **Layer caching** → copy `requirements.txt` first, install, then copy code.
3. **Base images** → use `python:3.11-slim`, not the fat one.
4. **Multi-stage builds** → build deps in one stage, copy only what I need.
5. **Combine RUNs** → keep images small, no stale caches.
6. **Pin versions** → avoid “works yesterday, broken today”.
7. **Run as non-root** → safer.
8. **Use env vars, not baking secrets**.
9. **Logs to stdout/stderr** → Docker handles it.
10. **HEALTHCHECK** → container auto-heals.

---

## 💡 Commands I actually use

```bash
# Build & run
docker build -t myapp:v1 .
docker run -d -p 8000:8000 --name app_ctr myapp:v1

# See stuff
docker ps
docker logs -f app_ctr
docker exec -it app_ctr bash

# Stop & clean
docker stop app_ctr
docker rm app_ctr
docker rmi myapp:v1

# Volumes
docker volume ls
docker volume rm <volume_name>

# Compose
docker compose up -d
docker compose ps
docker compose logs -f api
docker compose down -v
```

---



