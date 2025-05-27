<final_answer>

markdown
Copy
Edit
# 🚀 Langflow on Hetzner Cloud

Deploy Langflow — a powerful visual programming interface for LangChain — on a Hetzner Cloud server using Docker Compose.

---

## 📦 Features

- Visual interface to build and run LangChain flows
- Drag-and-drop components
- Easy deployment using Docker
- PostgreSQL backend for persistence

---

## ⚙️ Prerequisites

- A Hetzner Cloud server (Ubuntu 22.04 recommended)
- SSH access to the server
- [Docker](https://docs.docker.com/get-docker/)
- [Docker Compose](https://docs.docker.com/compose/install/)
- (Optional) A domain name for HTTPS setup

---

## 🧪 Environment Setup

1. **SSH into your server**:
   ```bash
   ssh root@your_server_ip
Install Docker & Docker Compose:

bash
Copy
Edit
apt update && apt install -y docker.io docker-compose
systemctl enable --now docker
Create project directory:

bash
Copy
Edit
mkdir langflow && cd langflow
Create a .env file:

bash
Copy
Edit
nano .env
Add:

env
Copy
Edit
POSTGRES_USER=langflow_user
POSTGRES_PASSWORD=langflow_pass
POSTGRES_DB=langflow
LANGFLOW_SUPERUSER=admin
LANGFLOW_SUPERUSER_PASSWORD=admin123
📄 Docker Compose Setup
Create a docker-compose.yml file:

yaml
Copy
Edit
version: "3.9"
services:
  langflow-db:
    image: postgres:16-alpine
    container_name: langflow-db
    environment:
      POSTGRES_DB: ${POSTGRES_DB}
      POSTGRES_USER: ${POSTGRES_USER}
      POSTGRES_PASSWORD: ${POSTGRES_PASSWORD}
    volumes:
      - ./langflow-db:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U ${POSTGRES_USER} -d ${POSTGRES_DB}"]
      interval: 5s
      timeout: 5s
      retries: 5

  langflow:
    image: langflowai/langflow:latest
    container_name: langflow
    ports:
      - "7860:7860"
    environment:
      LANGFLOW_DATABASE_URL: postgresql://${POSTGRES_USER}:${POSTGRES_PASSWORD}@langflow-db:5432/${POSTGRES_DB}?sslmode=disable
      LANGFLOW_CONFIG_DIR: /var/lib/langflow
      LANGFLOW_SUPERUSER: ${LANGFLOW_SUPERUSER}
      LANGFLOW_SUPERUSER_PASSWORD: ${LANGFLOW_SUPERUSER_PASSWORD}
      LANGFLOW_AUTO_LOGIN: False
    volumes:
      - ./langflow:/var/lib/langflow
    depends_on:
      - langflow-db
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:7860/health"]
      interval: 30s
      timeout: 10s
      retries: 5
🚀 Run the App
bash
Copy
Edit
docker-compose up -d
Access the app at:

arduino
Copy
Edit
http://your_server_ip:7860
Login with your superuser credentials from .env.

🔒 (Optional) Secure with HTTPS using Caddy
Install Caddy:

bash
Copy
Edit
apt install -y debian-keyring debian-archive-keyring apt-transport-https
curl -1sLf 'https://dl.cloudsmith.io/public/caddy/stable/gpg.key' | gpg --dearmor -o /usr/share/keyrings/caddy-stable-archive-keyring.gpg
curl -1sLf 'https://dl.cloudsmith.io/public/caddy/stable/debian.deb.txt' | tee /etc/apt/sources.list.d/caddy-stable.list
apt update && apt install caddy
Create Caddyfile:

bash
Copy
Edit
nano /etc/caddy/Caddyfile
Add:

css
Copy
Edit
your-domain.com {
    reverse_proxy localhost:7860
}
Restart Caddy:

bash
Copy
Edit
systemctl restart caddy
Visit https://your-domain.com in your browser.

🧯 Troubleshooting
If git clone or curl fails with "Could not resolve host":

Set DNS manually:

bash
Copy
Edit
echo "nameserver 8.8.8.8" > /etc/resolv.conf
If Docker fails to start:

Check logs:

bash
Copy
Edit
docker-compose logs
📜 License & Attribution
This setup uses Langflow by Logspace AI, under their respective license. This repo is only a deployment guide.
