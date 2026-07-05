# 🐳 Docker Compose — Multi-Container Local Development Environment

Running a MongoDB database and its web-based admin UI (Mongo Express) as a linked, multi-container environment using Docker Compose — replacing a chain of manual `docker run` commands with a single, reproducible configuration file.

![Docker](https://img.shields.io/badge/Docker-2496ED?style=flat&logo=docker&logoColor=white)
![Docker Compose](https://img.shields.io/badge/Docker%20Compose-2496ED?style=flat&logo=docker&logoColor=white)
![MongoDB](https://img.shields.io/badge/MongoDB-47A248?style=flat&logo=mongodb&logoColor=white)

---

## Project Overview

Local development often needs several services running together — here, a database and its admin interface. Instead of manually starting containers one by one and wiring up networking by hand, this project uses **Docker Compose** to define both services, their configuration, and how they connect to each other, in a single declarative file.

This is part of a broader DevOps learning path (Module 7 — *Containers with Docker*), building toward later projects covering persistent volumes, private registries, and full CI/CD pipelines.

## What This Demonstrates

- Defining and orchestrating **multiple linked containers** with a single `docker-compose.yml`
- Understanding of **Docker networking** — how services discover each other by service name (`mongodb`) instead of IP address
- Correct **YAML structure and indentation** discipline (a common source of subtle config bugs in real infrastructure work)
- Use of **environment variables** to configure containerized services (DB credentials, connection targets)
- Awareness of `restart` policies and `depends_on` for basic service startup ordering

## Tech Stack

| Tool | Purpose |
|------|---------|
| **Docker** | Containerization engine |
| **Docker Compose** | Multi-container orchestration |
| **MongoDB** | NoSQL database |
| **Mongo Express** | Web-based MongoDB admin UI |

## 📂 Project Structure

```
.
├── docker-compose.yml
└── README.md
```

## The Compose File

```yaml
services:
  mongodb:
    image: mongo
    ports:
      - 27017:27017
    environment:
      - MONGO_INITDB_ROOT_USERNAME=admin
      - MONGO_INITDB_ROOT_PASSWORD=password

  mongo-express:
    image: mongo-express
    restart: always
    ports:
      - 8081:8081
    environment:
      - ME_CONFIG_MONGODB_ADMINUSERNAME=admin
      - ME_CONFIG_MONGODB_ADMINPASSWORD=password
      - ME_CONFIG_MONGODB_SERVER=mongodb
    depends_on:
      - mongodb
```


- **`ME_CONFIG_MONGODB_SERVER=mongodb`** — Mongo Express connects to the database using the Docker Compose **service name** as its hostname, not `localhost` or an IP. Compose automatically creates a shared network where containers resolve each other by service name — no manual network configuration needed.
- **`depends_on: mongodb`** — ensures the MongoDB container starts before Mongo Express attempts to connect, avoiding startup race conditions.
- **`restart: always`** on Mongo Express — keeps the admin UI available across restarts/crashes without manual intervention.
- **Environment variables for credentials** — keeps configuration out of application code and in one central, editable place.

**Note on credentials:** Credentials are hardcoded here for local demo purposes only!!!

## How to Run It

```bash
# 1. Start both containers in detached mode
docker-compose up -d

# 2. Confirm both services are running
docker-compose ps

# 3. Access Mongo Express in the browser
# http://localhost:8081

# 4. View logs for a specific service
docker-compose logs mongodb

# 5. Stop and remove containers, network
docker-compose down
```

## ✅ Verification / Testing

- Confirmed both containers report `Up` status via `docker-compose ps`
- Verified Mongo Express successfully connects to MongoDB and loads the admin UI at `localhost:8081`
- Created a test database/collection through the Mongo Express UI to confirm read/write access
- Restarted the stack with `docker-compose down && docker-compose up -d` to confirm data behavior and startup ordering


## 👤 Author

**Dorjee**
[GitHub](https://github.com/dor-jee) · [LinkedIn](https://www.linkedin.com/in/namgeldorjee)


