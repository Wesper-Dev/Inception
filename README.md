<p align="center">
  <img src="https://capsule-render.vercel.app/api?type=waving&color=0:2d333b,50:79c0ff,100:56d364&height=200&section=header&text=Inception&fontSize=50&fontColor=ffffff&fontAlignY=35&desc=Dockerized%20WordPress%20Infrastructure&descSize=18&descAlignY=55&animation=twinkling" width="100%"/>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/Docker-2496ED?style=for-the-badge&logo=docker&logoColor=white"/>
  <img src="https://img.shields.io/badge/Nginx-009639?style=for-the-badge&logo=nginx&logoColor=white"/>
  <img src="https://img.shields.io/badge/WordPress-21759B?style=for-the-badge&logo=wordpress&logoColor=white"/>
  <img src="https://img.shields.io/badge/MariaDB-003545?style=for-the-badge&logo=mariadb&logoColor=white"/>
  <img src="https://img.shields.io/badge/Status-Completed-3fb950?style=for-the-badge"/>
</p>

---

## 📖 About

**Inception** is a system administration project from the [42 School](https://42.fr/) curriculum. The goal is to set up a small infrastructure composed of multiple Docker containers, each running a single service, orchestrated with Docker Compose. No pre-built images from Docker Hub are allowed — every Dockerfile must be written from scratch using **Debian** as the base image.

---

## 🏗️ Architecture

```
                       ┌─────────────────┐
                       │    Client        │
                       │  (Browser)       │
                       └────────┬────────┘
                                │ HTTPS :443
                       ┌────────▼────────┐
                       │     Nginx       │
                       │  TLSv1.2/1.3    │
                       │  Reverse Proxy  │
                       └────────┬────────┘
                                │ :9000 (FastCGI)
                       ┌────────▼────────┐
                       │   WordPress     │
                       │   + PHP-FPM     │
                       │   + WP-CLI      │
                       └────────┬────────┘
                                │ :3306
                       ┌────────▼────────┐
                       │    MariaDB      │
                       │   Database      │
                       └─────────────────┘

  Volumes:  ┌─────────────┐    ┌─────────────┐
            │  wp_data    │    │  db_data    │
            │ (WordPress  │    │ (MariaDB    │
            │  files)     │    │  storage)   │
            └─────────────┘    └─────────────┘
```

---

## ✨ Features

| Requirement | Implementation |
|:------------|:---------------|
| **No pre-built images** | All Dockerfiles built from `debian:bullseye` |
| **One service per container** | Nginx, WordPress, MariaDB — each isolated |
| **TLS only** | Nginx configured with TLSv1.2 / TLSv1.3, self-signed certificate |
| **WordPress + PHP-FPM** | Installed and configured via WP-CLI (no web installer) |
| **MariaDB** | Database initialized with setup script, users created automatically |
| **Persistent storage** | Two Docker volumes for WordPress files and database data |
| **Docker network** | All containers communicate through an internal Docker network |
| **Environment variables** | Secrets managed via `.env` file (never committed) |
| **Auto-restart** | Containers configured to restart on failure |

---

## 📁 Project Structure

```
Inception/
├── srcs/
│   ├── docker-compose.yml           # Service orchestration
│   └── requirements/
│       ├── nginx/
│       │   ├── Dockerfile           # Debian + Nginx + SSL setup
│       │   └── conf/               # Nginx server configuration
│       ├── wordpress/
│       │   ├── dockerfile           # Debian + PHP-FPM + WP-CLI
│       │   └── conf/               # PHP-FPM & WordPress config
│       └── mariadb/
│           ├── Dockerfile           # Debian + MariaDB server
│           ├── conf/               # MariaDB configuration
│           └── tools/              # DB initialization scripts
└── Makefile                         # Build & manage shortcuts
```

---

## 🛠️ Services

### 🔒 Nginx
- Serves as the **only entry point** (port 443)
- TLSv1.2 / TLSv1.3 with self-signed certificate
- Reverse proxy to WordPress via FastCGI on port 9000

### 📝 WordPress
- Installed via **WP-CLI** (automated, no manual setup)
- Runs with **PHP-FPM** (no Apache)
- Two users created automatically (admin + regular user)
- Connected to MariaDB for data persistence

### 🗄️ MariaDB
- Database server with automated initialization
- User and database creation via entrypoint script
- Data persisted on a dedicated Docker volume

---

## 🚀 Getting Started

### Prerequisites

- **Docker** & **Docker Compose**
- **Make**

### Build & Run

```bash
# Clone the repository
git clone https://github.com/Wesper-Dev/Inception.git
cd Inception

# Create your environment file
cp .env.example .env
# Edit .env with your credentials

# Build and launch all containers
make

# Stop all containers
make down

# Clean everything (containers, volumes, images)
make fclean
```

### Environment Variables

| Variable | Description |
|:---------|:------------|
| `DOMAIN_NAME` | Your domain (e.g., `adurand.42.fr`) |
| `MYSQL_ROOT_PASSWORD` | MariaDB root password |
| `MYSQL_USER` | WordPress database user |
| `MYSQL_PASSWORD` | WordPress database password |
| `MYSQL_DATABASE` | WordPress database name |
| `WP_ADMIN_USER` | WordPress admin username |
| `WP_ADMIN_PASSWORD` | WordPress admin password |
| `WP_ADMIN_EMAIL` | WordPress admin email |
| `WP_USER` | WordPress regular user |
| `WP_USER_PASSWORD` | WordPress regular user password |
| `WP_USER_EMAIL` | WordPress regular user email |

---

## 📚 Key Concepts Learned

- **Containerization** — Building Docker images from scratch (no pre-built)
- **Service isolation** — One process per container philosophy
- **Docker networking** — Inter-container communication via internal networks
- **TLS/SSL** — Certificate generation and HTTPS enforcement
- **Infrastructure as Code** — Reproducible environments with Docker Compose
- **Process management** — PID 1, signal handling, graceful shutdown
- **Volume persistence** — Data survival across container restarts

---

<p align="center">
  <img src="https://capsule-render.vercel.app/api?type=waving&color=0:2d333b,50:79c0ff,100:56d364&height=100&section=footer" width="100%"/>
</p>
