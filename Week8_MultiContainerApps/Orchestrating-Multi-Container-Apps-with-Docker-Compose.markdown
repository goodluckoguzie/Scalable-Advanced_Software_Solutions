# Week 8-B: Orchestrating Multi-Container Apps with Docker Compose

## Introduction

Welcome to Week 8-B of our Web Application Development course! This week, we’re stepping up from single-container apps to orchestrating multiple containers using **Docker Compose**. Think of Docker Compose as a band conductor, and each container is a musician playing a specific role (like a web server or a database). The conductor uses a script—a YAML file—to tell everyone what to do, ensuring they work together smoothly. With one command, you can start an entire “band” of containers that communicate to form a complete application. This process is called **container orchestration**, and it’s a core concept used in tools like Docker Compose and even larger systems like Kubernetes. In this lab, we’ll use simple language (perfect for a 14-year-old), step-by-step instructions for Windows 10/11 with PowerShell or Command Prompt, and hands-on tasks to run web servers, databases, and custom apps. By the end, you’ll know how to write a Docker Compose YAML file, connect containers like a web app to a database, scale services, and even set up basic load balancing. Let’s get started and make some container magic! 🐳🎶

## What You’ll Learn

- **YAML Basics**: Understand YAML, a human-friendly format used to define Docker Compose configurations.
- **Docker Compose**: Learn how Docker Compose simplifies running multi-container apps with a single YAML file and one command.
- **Single-Container Setup**: Run a simple web server (Nginx) using Docker Compose to get comfortable with the tool.
- **Multi-Container Setup**: Combine a web server (Nginx) with a database (PostgreSQL) to see how containers work together.
- **Building Images with Compose**: Use Docker Compose to build a custom app image (e.g., a Node.js app with a Redis database for counting page visits).
- **Service Discovery**: Understand how containers find each other using service names instead of IP addresses, thanks to Docker Compose’s internal DNS.
- **Scaling and Load Balancing**: Run multiple instances of a service and use Nginx as a load balancer to distribute requests across them.
- **Environment Variables and Volumes**: Configure containers dynamically with environment variables and ensure database data persists using volumes.
- **Custom Multi-Container App**: Build your own multi-container application with a database and a small app, using best practices like environment variables and persistent storage.

## Prerequisites

- **Docker Desktop**: Ensure Docker Desktop is installed and running on your Windows 10/11 machine. Check the system tray for the Docker whale icon (🐳) to confirm it’s active. Verify it works by running:
  ```bash
  docker version
  ```
  **Command Prompt (single-line):**
  ```bash
  docker version
  ```
  You should see Client and Server details, like `Server: Docker Desktop 4.x.x (Windows)`.

- **Lab Files**: Have the Week 8-B lab ZIP file (provided by your instructor) extracted to a folder on your PC (e.g., `C:\Users\<YourName>\Desktop\week8`). The ZIP contains directories like `docker-compose-1-nginx`, `docker-compose-2-nginx-postgres`, `docker-compose-3-build-redis`, and `docker-compose-4-scaleing`. Each has a `docker-compose.yml` file and sometimes app code or configs.

- **PowerShell or Command Prompt**: Open PowerShell (or Windows Terminal) or Command Prompt. Navigate to the lab folder using `cd` (e.g., `cd C:\Users\<YourName>\Desktop\week8\docker-compose-1-nginx`). Use `ls` (PowerShell) or `dir` (Command Prompt) to list files and confirm you see `docker-compose.yml`.

- **Optional Text Editor**: Use Notepad, VS Code, or another editor to view/edit YAML files. Avoid copying from Word documents, as they may add “smart” quotes or hyphens that break YAML.

Let’s dive into orchestrating containers with Docker Compose!

## Step 1: Understanding YAML and Running a Single-Container App (Nginx)

Before we orchestrate multiple containers, let’s start with a single-container setup to understand **YAML** and **Docker Compose**. YAML (which stands for “YAML Ain’t Markup Language”) is a simple, human-readable way to write configuration files, using indentation and lists instead of complex brackets like JSON. Docker Compose uses a YAML file (usually `docker-compose.yml`) to define your app’s services (containers), networks, volumes, and settings. With one command, Docker Compose reads this file and starts everything.

### What is YAML?

Think of YAML as a recipe card for your app. It uses:
- **Keys and Values**: Written as `key: value` (e.g., `image: nginx:1.21` means the key `image` has the value `nginx:1.21`).
- **Sections**: Marked by a name with a colon (e.g., `services:`) and indented lines below it (use 2 spaces for indentation, never tabs).
- **Lists**: Shown with a hyphen and space (e.g., `- "5000:80"` for a port mapping).
- **Comments**: Anything after `#` is ignored by Docker but helps you add notes.

Here’s an example YAML snippet:
```yaml
services:
  webapp:
    image: nginx:1.21
    ports:
      - "5000:80"
```
This defines a service called `webapp` that uses the `nginx:1.21` image and maps port 5000 on your PC to port 80 in the container.

### Task: Run an Nginx Web Server

Let’s try a simple Docker Compose setup with one service: an **Nginx** web server (a popular tool for serving web pages).

1. **Navigate to the Directory**:
   In PowerShell or Command Prompt, go to the `docker-compose-1-nginx` folder:
   ```bash
   cd C:\Users\<YourName>\Desktop\week8\docker-compose-1-nginx
   ```
   **Command Prompt (single-line):**
   ```bash
   cd C:\Users\<YourName>\Desktop\week8\docker-compose-1-nginx
   ```
   Verify you see `docker-compose.yml` with:
   ```bash
   ls
   ```
   **Command Prompt (single-line):**
   ```bash
   dir
   ```

2. **Inspect the YAML File**:
   Open `docker-compose.yml` in a text editor or view it with:
   ```bash
   cat docker-compose.yml
   ```
   **Command Prompt (single-line):**
   ```bash
   type docker-compose.yml
   ```
   It should look like:
   ```yaml
   version: "3.9"
   services:
     webapp:
       image: nginx:1.21
       ports:
         - "5000:80"
   ```
   Breakdown:
   - `version: "3.9"`: Specifies the Docker Compose file format (3.9 is a modern version).
   - `services:`: Lists the containers to run.
   - `webapp:`: A service named `webapp` (you can name it anything).
   - `image: nginx:1.21`: Uses the official Nginx image (version 1.21) from Docker Hub.
   - `ports: - "5000:80"`: Maps port 5000 on your PC to port 80 in the container (Nginx’s default HTTP port).

3. **Start the App**:
   Run:
   ```bash
   docker-compose up
   ```
   **Command Prompt (single-line):**
   ```bash
   docker-compose up
   ```
   (Note: If you have Docker Compose V2, `docker compose up`—without the hyphen—works too. We’ll use `docker-compose` for consistency.) This command:
   - Reads `docker-compose.yml`.
   - Pulls `nginx:1.21` from Docker Hub if not already downloaded.
   - Creates a container (e.g., `docker-compose-1-nginx_webapp_1`).
   - Sets up a default network (e.g., `docker-compose-1-nginx_default`).
   - Maps port 5000 (host) to 80 (container).
   You’ll see logs in PowerShell showing the image being pulled (if needed) and Nginx starting. The terminal will “follow” the logs, showing Nginx’s startup messages.

4. **Verify It’s Working**:
   Open your browser to `http://localhost:5000`. You should see an Nginx welcome page (e.g., “Welcome to nginx!” or a custom page if the lab provided one, like “Hello from Nginx”). This confirms the container is serving content. Your browser’s request goes to port 5000 on your PC, which Docker forwards to port 80 in the Nginx container.

5. **Stop the App**:
   Since we ran `docker-compose up` in the foreground, press `Ctrl+C` in PowerShell/Command Prompt to stop the container. This gracefully shuts down Nginx and returns your prompt. Alternatively, if it doesn’t stop cleanly, run:
   ```bash
   docker-compose down
   ```
   **Command Prompt (single-line):**
   ```bash
   docker-compose down
   ```
   This stops and removes the container and network.

6. **Confirm It’s Down**:
   Check with:
   ```bash
   docker ps
   ```
   **Command Prompt (single-line):**
   ```bash
   docker ps
   ```
   No containers should be listed (or use `docker-compose ps` to confirm in the directory).

**Debugging Tips**:
- **Error: "port is already allocated"**: Port 5000 is in use. Check with:
  ```bash
  netstat -ano | findstr :5000
  ```
  **Command Prompt (single-line):**
  ```bash
  netstat -ano | findstr :5000
  ```
  If it’s a Docker container, find it with `docker ps` and stop it:
  ```bash
  docker stop <container-id>
  ```
  **Command Prompt (single-line):**
  ```bash
  docker stop <container-id>
  ```
  If it’s not a Docker container, kill the process:
  ```bash
  taskkill /PID <pid> /F
  ```
  **Command Prompt (single-line):**
  ```bash
  taskkill /PID <pid> /F
  ```
  To confirm if a PID is a Docker container, compare with:
  ```bash
  docker inspect --format '{{.State.Pid}}' <container-id>
  ```
  **Command Prompt (single-line):**
  ```bash
  docker inspect --format "{{.State.Pid}}" <container-id>
  ```
  If it matches, use `docker stop` for a graceful shutdown. Alternatively, use a different port in `docker-compose.yml` (e.g., `5001:80`).
- **Error: "Cannot connect to the Docker daemon" or "open //./pipe/dockerDesktopLinuxEngine"**: Docker Desktop isn’t running. Quit Docker Desktop (system tray > Quit), restart it via the Start menu, and wait 1–2 minutes. Verify with:
  ```bash
  docker version
  ```
  **Command Prompt (single-line):**
  ```bash
  docker version
  ```
  If it persists, restart the Docker service (Admin PowerShell):
  ```powershell
  Restart-Service com.docker.service
  ```
  **Command Prompt (single-line, Admin):**
  ```bash
  net stop com.docker.service && net start com.docker.service
  ```
- **YAML syntax error**: Check for tabs (use spaces), missing colons, or incorrect indentation (2 spaces per level). Validate with an online YAML linter (e.g., yamllint.com).
- **Browser shows "connection refused"**: Ensure the container is running (`docker ps`) and logs show no errors (`docker-compose logs`). Check port mapping in `docker-compose.yml`.

**What We Did**: You ran a single Nginx container using Docker Compose, defined by a YAML file. This is similar to `docker run -p 5000:80 nginx:1.21`, but the YAML file makes it reusable and easier to modify. Compose also created a network automatically, which we’ll use more in the next step.

## Step 2: Running a Multi-Container Setup (Nginx + PostgreSQL)

Now let’s orchestrate two containers: an Nginx web server and a **PostgreSQL** database (a popular open-source relational database). This mimics a real app where a web server might need a database. In this task, Nginx serves a static page (not directly using Postgres), but both containers run together, and we’ll see how they coexist on a shared network.

1. **Navigate to the Directory**:
   Move to the `docker-compose-2-nginx-postgres` folder:
   ```bash
   cd C:\Users\<YourName>\Desktop\week8\docker-compose-2-nginx-postgres
   ```
   **Command Prompt (single-line):**
   ```bash
   cd C:\Users\<YourName>\Desktop\week8\docker-compose-2-nginx-postgres
   ```
   Confirm `docker-compose.yml` exists with `ls` or `dir`.

2. **Inspect the YAML File**:
   View the `docker-compose.yml`:
   ```bash
   cat docker-compose.yml
   ```
   **Command Prompt (single-line):**
   ```bash
   type docker-compose.yml
   ```
   It should look like:
   ```yaml
   services:
     webapp:
       image: nginx:1.21
       ports:
         - "5000:80"
     dbserver:
       image: postgres:13
       environment:
         - POSTGRES_PASSWORD=mysecretpassword
   ```
   Breakdown:
   - `webapp`: The Nginx service, same as before, mapping port 5000 to 80.
   - `dbserver`: A PostgreSQL service using `postgres:13`. The `POSTGRES_PASSWORD` environment variable sets the database’s admin password (required by the Postgres image).
   - No ports are exposed for `dbserver`, meaning Postgres is only accessible within the Compose network (e.g., by `webapp` if needed), not from your PC. This is a common setup for databases to stay secure.
   - No explicit network is defined, so Compose creates a default network (e.g., `docker-compose-2-nginx-postgres_default`) where `webapp` and `dbserver` can communicate using their service names.

3. **Start the App**:
   Run in detached mode to free your terminal:
   ```bash
   docker-compose up -d
   ```
   **Command Prompt (single-line):**
   ```bash
   docker-compose up -d
   ```
   This:
   - Pulls `nginx:1.21` and `postgres:13` (if not already cached).
   - Creates a network and starts two containers: `docker-compose-2-nginx-postgres_webapp_1` and `docker-compose-2-nginx-postgres_dbserver_1`.
   - Maps port 5000 for Nginx but keeps Postgres internal (port 5432 is available within the network).

4. **Verify Containers Are Running**:
   Check with:
   ```bash
   docker-compose ps
   ```
   **Command Prompt (single-line):**
   ```bash
   docker-compose ps
   ```
   You should see:
   ```
   Name                                     Command               State          Ports
   docker-compose-2-nginx-postgres_webapp_1   nginx -g 'daemon off;'   Up      0.0.0.0:5000->80/tcp
   docker-compose-2-nginx-postgres_dbserver_1 docker-entrypoint.sh ...  Up      5432/tcp
   ```
   Or use `docker ps` to see all running containers. The Postgres container shows `5432/tcp` but no host mapping, confirming it’s internal.

5. **Test Nginx**:
   Open `http://localhost:5000` in your browser. You should see the same Nginx page as before (e.g., “Welcome to nginx!” or a custom page). The Postgres container runs quietly in the background but doesn’t affect Nginx here.

6. **Check Inter-Container Networking**:
   Although Nginx isn’t using Postgres in this task, the containers can communicate. For example, if `webapp` had a Postgres client, it could connect to `dbserver:5432` using the service name `dbserver`. Compose’s internal DNS resolves `dbserver` to the Postgres container’s IP. We’ll explore this more in Step 4.

7. **Stop the App**:
   Shut down both containers:
   ```bash
   docker-compose down
   ```
   **Command Prompt (single-line):**
   ```bash
   docker-compose down
   ```
   This removes the containers and network but preserves any named volumes (if defined).

**Debugging Tips**:
- **Error: "port is already allocated"**: See Step 1 for port 5000 conflict resolution.
- **Error: "Cannot connect to the Docker daemon"**: See Step 1 for Docker Desktop restart steps.
- **Postgres fails to start**: Check logs:
  ```bash
  docker-compose logs dbserver
  ```
  **Command Prompt (single-line):**
  ```bash
  docker-compose logs dbserver
  ```
  Ensure `POSTGRES_PASSWORD` is set and non-empty. Verify sufficient memory in Docker Desktop (Settings > Resources).
- **No Nginx page**: Confirm the container is running (`docker-compose ps`) and logs show Nginx started (`docker-compose logs webapp`).

**What We Did**: You ran a two-container app with Nginx and Postgres. Compose created a network automatically, allowing `webapp` and `dbserver` to communicate by name if needed. You saw how to run in detached mode (`-d`) and check status with `docker-compose ps`. This shows Compose’s power: managing multiple containers with one command.

## Step 3: Building a Custom App with Docker Compose (Node.js + Redis)

Now let’s use Docker Compose to **build** a custom app image instead of using a pre-built one. We’ll run a Node.js app that counts page visits, storing the count in a **Redis** database (a fast, in-memory key-value store, perfect for counters). The app is defined by a `Dockerfile`, and Compose will build and run it alongside a Redis container.

1. **Navigate to the Directory**:
   Move to `docker-compose-3-build-redis`:
   ```bash
   cd C:\Users\<YourName>\Desktop\week8\docker-compose-3-build-redis
   ```
   **Command Prompt (single-line):**
   ```bash
   cd C:\Users\<YourName>\Desktop\week8\docker-compose-3-build-redis
   ```
   Check for `docker-compose.yml` and an `app/` folder with a `Dockerfile` and `src/` (containing `index.js`, etc.).

2. **Inspect the YAML File**:
   View `docker-compose.yml`:
   ```bash
   cat docker-compose.yml
   ```
   **Command Prompt (single-line):**
   ```bash
   type docker-compose.yml
   ```
   Expected content:
   ```yaml
   services:
     webapp:
       build: ./app
       ports:
         - "5000:80"
     dbserver:
       image: redis:latest
   ```
   Breakdown:
   - `webapp`: The Node.js app, built from the `Dockerfile` in the `./app` directory (not a pre-built image).
   - `build: ./app`: Tells Compose to run `docker build` in the `app/` folder to create the image.
   - `ports: - "5000:80"`: Maps port 5000 (host) to 80 (container, where the Node app listens).
   - `dbserver`: Uses the `redis:latest` image. No ports are exposed, so Redis is only accessible within the Compose network (on port 6379, Redis’s default).
   - No volumes are defined here, so Redis data is in-memory and not persistent (lost on `down` unless a volume is added).

3. **Check the App Structure**:
   Look inside `app/`:
   ```bash
   ls app
   ```
   **Command Prompt (single-line):**
   ```bash
   dir app
   ```
   You should see a `Dockerfile` and a `src/` folder with `index.js`, `package.json`, etc. The `Dockerfile` likely:
   - Starts from `node` (e.g., `FROM node:18`).
   - Copies app files and runs `npm install` or `yarn install`.
   - Starts the app (e.g., `CMD ["node", "src/index.js"]`).

4. **Run and Build the App**:
   Start with:
   ```bash
   docker-compose up --build
   ```
   **Command Prompt (single-line):**
   ```bash
   docker-compose up --build
   ```
   The `--build` flag ensures Compose builds the `webapp` image from the `app/Dockerfile`. You’ll see:
   - Build output for `webapp` (e.g., pulling Node base image, copying files, running `npm install`).
   - Pulling `redis:latest` if needed.
   - Starting both containers and a network (e.g., `docker-compose-3-build-redis_default`).
   - Logs from both services (e.g., `webapp_1 | Server running on port 80`, `dbserver_1 | Ready to accept connections`).

5. **Test the App**:
   Open `http://localhost:5000`. You should see a webpage showing a view counter (e.g., “This page has been viewed 1 times”). Refresh the page multiple times; the counter should increment (2, 3, 4, ...). The page may also display the Redis server’s IP (e.g., `172.20.0.x`), which is an internal Docker network IP, not accessible from your PC. This confirms:
   - The Node app is connecting to Redis (likely using the hostname `dbserver`).
   - Redis is storing the counter, incrementing it per visit.

6. **Inspect the Code (Optional)**:
   Open `app/src/index.js` to see how it connects to Redis. It likely uses a Redis client with `host: 'dbserver'` (the service name from `docker-compose.yml`). For example:
   ```javascript
   const redis = require('redis');
   const client = redis.createClient({ host: 'dbserver', port: 6379 });
   ```
   This shows the app relies on Compose’s DNS to resolve `dbserver` to the Redis container’s IP.

7. **Stop the App**:
   Press `Ctrl+C` to stop (since we ran in the foreground). Then clean up:
   ```bash
   docker-compose down
   ```
   **Command Prompt (single-line):**
   ```bash
   docker-compose down
   ```

**Debugging Tips**:
- **Build fails**: Check `app/Dockerfile` for errors (e.g., wrong base image, missing files). View logs:
  ```bash
  docker-compose logs webapp
  ```
  **Command Prompt (single-line):**
  ```bash
  docker-compose logs webapp
  ```
- **Counter not incrementing**: Ensure the app connects to Redis (check logs for errors). Verify `dbserver` is running (`docker-compose ps`). Confirm the app uses `dbserver` as the Redis host.
- **Error: "port is already allocated"**: See Step 1 for port 5000 conflict resolution.
- **Error: "Cannot connect to the Docker daemon"**: See Step 1 for Docker Desktop restart steps.

**What We Did**: You used Docker Compose to build a custom Node.js app image and run it alongside a Redis container. The app stored page views in Redis, showing how containers communicate over Compose’s network using service names. The `--build` flag integrated the build process into Compose, simplifying development.

## Step 4: Understanding Service Discovery and Name Resolution

How does the Node.js app know where to find the Redis server? The answer lies in **Docker Compose’s service discovery** via its internal DNS. Let’s break it down:

- **Compose Network**: When you run `docker-compose up`, Compose creates a private network (e.g., `docker-compose-3-build-redis_default`). All services (`webapp`, `dbserver`) join this network.
- **DNS Resolution**: Compose sets up a DNS server (at `127.0.0.11` inside containers) that maps service names to their container IPs. For example, `dbserver` resolves to the Redis container’s IP (e.g., `172.20.0.x`).
- **Why Use Names?**: Container IPs can change (e.g., if a container restarts). Using service names like `dbserver` ensures the app always finds the right container, as Compose updates the DNS dynamically.
- **How It Works in the App**: In Step 3, `index.js` likely used `dbserver` as the Redis host. When the app queries `dbserver:6379`, Docker’s DNS resolves it to the Redis container’s current IP, ensuring a reliable connection.

This **service discovery** is a cornerstone of orchestration. Instead of hardcoding IPs (which are unpredictable), you use service names defined in `docker-compose.yml`. This makes your app portable—move it to another machine, and Compose reassigns IPs, but the names stay the same.

**Analogy**: Imagine a school where students (containers) have changing desk numbers (IPs). Instead of memorizing desks, you call students by name (service name). The school office (Docker’s DNS) knows everyone’s current desk and connects you. If a student moves desks, you still reach them by name.

**What We Did**: You learned how Compose’s DNS enables containers to communicate reliably using service names, as seen in the Node.js app connecting to `dbserver` (Redis). This is critical for multi-container apps, ensuring flexibility and stability.

## Step 5: Scaling Services and Load Balancing with Nginx

Now let’s scale our app to handle more “traffic” by running multiple instances of the web app, with an Nginx container acting as a **load balancer** to distribute requests. We’ll use the same Node.js + Redis app, but scale the web app to three instances and add Nginx to balance traffic.

1. **Navigate to the Directory**:
   Move to `docker-compose-4-scaleing`:
   ```bash
   cd C:\Users\<YourName>\Desktop\week8\docker-compose-4-scaleing
   ```
   **Command Prompt (single-line):**
   ```bash
   cd C:\Users\<YourName>\Desktop\week8\docker-compose-4-scaleing
   ```
   Check for `docker-compose.yml`, an `app/` folder, and an `nginx.conf` file.

2. **Inspect the YAML File**:
   View `docker-compose.yml`:
   ```bash
   cat docker-compose.yml
   ```
   **Command Prompt (single-line):**
   ```bash
   type docker-compose.yml
   ```
   Expected content:
   ```yaml
   services:
     nginx:
       image: nginx:1.21
       ports:
         - "8080:8080"
       volumes:
         - ./nginx.conf:/etc/nginx/nginx.conf
     webapp:
       build: ./app
     dbserver:
       image: redis:latest
   ```
   Breakdown:
   - `nginx`: An Nginx service listening on port 8080 (host and container), with a custom `nginx.conf` mounted to `/etc/nginx/nginx.conf`.
   - `webapp`: The Node.js app, built from `app/Dockerfile` (same as Step 3).
   - `dbserver`: The Redis service, internal as before.
   - No ports for `webapp` or `dbserver`, as Nginx will forward requests internally.

3. **Inspect nginx.conf**:
   View `nginx.conf`:
   ```bash
   cat nginx.conf
   ```
   **Command Prompt (single-line):**
   ```bash
   type nginx.conf
   ```
   Likely content:
   ```nginx
   resolver 127.0.0.11 valid=5s;
   upstream webapp_upstream {
       server webapp:80;
   }
   server {
       listen 8080;
       location / {
           proxy_pass http://webapp_upstream;
       }
   }
   ```
   Breakdown:
   - `resolver 127.0.0.11 valid=5s`: Tells Nginx to use Docker’s DNS (`127.0.0.11`) and refresh name lookups every 5 seconds.
   - `upstream webapp_upstream`: Defines a group of servers (here, `webapp:80`). When scaled, `webapp` resolves to multiple IPs.
   - `proxy_pass`: Forwards requests to the `webapp` service, letting Nginx distribute traffic.

4. **Run with Scaling**:
   Start with three `webapp` instances:
   ```bash
   docker-compose up --build --scale webapp=3 -d
   ```
   **Command Prompt (single-line):**
   ```bash
   docker-compose up --build --scale webapp=3 -d
   ```
   This:
   - Builds the `webapp` image (if needed).
   - Starts one `nginx`, three `webapp`, and one `dbserver` container.
   - Creates a network where all containers communicate.
   - Maps port 8080 for Nginx.

5. **Verify Containers**:
   Check:
   ```bash
   docker-compose ps
   ```
   **Command Prompt (single-line):**
   ```bash
   docker-compose ps
   ```
   Expected output:
   ```
   Name                            Command               State          Ports
   scaleing_nginx_1                nginx -g 'daemon off;'   Up      0.0.0.0:8080->8080/tcp
   scaleing_webapp_1               node src/index.js        Up      80/tcp
   scaleing_webapp_2               node src/index.js        Up      80/tcp
   scaleing_webapp_3               node src/index.js        Up      80/tcp
   scaleing_dbserver_1             docker-entrypoint.sh ...  Up      6379/tcp
   ```

6. **Test Load Balancing**:
   Open `http://localhost:8080`. You should see the view counter page. Refresh multiple times. The page may show the IP of the `webapp` container serving the request (e.g., `172.20.0.5`, then `172.20.0.6`, etc.). The IP should change across refreshes, indicating Nginx is distributing requests across the three `webapp` containers (via Docker’s DNS round-robin). The counter should keep increasing, as all `webapp` instances share the same Redis (`dbserver`).

7. **Stop the App**:
   Shut down:
   ```bash
   docker-compose down
   ```
   **Command Prompt (single-line):**
   ```bash
   docker-compose down
   ```

**Debugging Tips**:
- **IP not changing**: Nginx may cache DNS for up to 5 seconds. Keep refreshing or reduce `valid=5s` in `nginx.conf` (e.g., to `1s`) and restart.
- **Counter not incrementing**: Check `webapp` logs for Redis connection errors:
  ```bash
  docker-compose logs webapp
  ```
  **Command Prompt (single-line):**
  ```bash
  docker-compose logs webapp
  ```
- **Error: "port is already allocated"**: Check port 8080 (similar to Step 1, replace `:5000` with `:8080`).
- **Error: "Cannot connect to the Docker daemon"**: See Step 1 for Docker Desktop restart.

**What We Did**: You scaled the `webapp` service to three instances using `--scale`. Nginx acted as a load balancer, distributing requests across them using Docker’s DNS, which rotates IPs for the `webapp` service name. The shared Redis ensured a consistent counter. This mimics real-world scaling, though production systems use more robust balancers.

## Step 6: Building Your Own Multi-Container App

Now it’s time to create your own multi-container app using Docker Compose! You’ll build a setup with:
- A database server (e.g., MySQL, PostgreSQL, or MongoDB).
- A small application (in your preferred language) that uses the database.
- Environment variables to pass database connection details.
- A persistent volume for the database.
- Optional: A mounted config file for customization.

### Guidelines

1. **Choose Your Stack**:
   Example:
   - **Database**: MySQL (`mysql:8.0`).
   - **App**: A Python Flask app that adds/retrieves records from MySQL.
   Alternatives: Node.js with MongoDB, Python with Postgres, etc.

2. **Create a Project Directory**:
   Make a new folder (e.g., `C:\Users\<YourName>\Desktop\my-compose-app`):
   ```bash
   mkdir my-compose-app
   cd my-compose-app
   ```
   **Command Prompt (single-line):**
   ```bash
   mkdir my-compose-app && cd my-compose-app
   ```

3. **Write the App**:
   Create an `app/` folder with a simple Flask app:
   ```bash
   mkdir app
   ```
   **Command Prompt (single-line):**
   ```bash
   mkdir app
   ```
   Example `app/app.py`:
   ```python
   from flask import Flask
   import mysql.connector
   import os

   app = Flask(__name__)

   @app.route('/')
   def home():
       conn = mysql.connector.connect(
           host=os.getenv('DB_HOST'),
           user=os.getenv('DB_USER'),
           password=os.getenv('DB_PASS'),
           database=os.getenv('DB_NAME')
       )
       cursor = conn.cursor()
       cursor.execute("CREATE TABLE IF NOT EXISTS visits (id INT AUTO_INCREMENT PRIMARY KEY, count INT)")
       cursor.execute("INSERT INTO visits (count) VALUES (1) ON DUPLICATE KEY UPDATE count = count + 1")
       cursor.execute("SELECT count FROM visits LIMIT 1")
       count = cursor.fetchone()[0]
       conn.commit()
       cursor.close()
       conn.close()
       return f"Page visited {count} times, connected to DB at {os.getenv('DB_HOST')}"
   if __name__ == '__main__':
       app.run(host='0.0.0.0', port=80)
   ```
   Create `app/requirements.txt`:
   ```text
   flask
   mysql-connector-python
   ```
   Create `app/Dockerfile`:
   ```dockerfile
   FROM python:3.9-slim
   WORKDIR /app
   COPY requirements.txt .
   RUN pip install -r requirements.txt
   COPY . .
   CMD ["python", "app.py"]
   ```

4. **Write the Compose File**:
   Create `docker-compose.yml`:
   ```bash
   type NUL > docker-compose.yml
   ```
   **Command Prompt (single-line):**
   ```bash
   type NUL > docker-compose.yml
   ```
   Edit with:
   ```yaml
   services:
     app:
       build: ./app
       ports:
         - "8000:80"
       environment:
         - DB_HOST=db
         - DB_USER=root
         - DB_PASS=secret
         - DB_NAME=myapp
       depends_on:
         - db
     db:
       image: mysql:8.0
       environment:
         - MYSQL_ROOT_PASSWORD=secret
         - MYSQL_DATABASE=myapp
       volumes:
         - db_data:/var/lib/mysql
   volumes:
     db_data:
   ```
   Breakdown:
   - `app`: Builds from `app/Dockerfile`, maps port 8000 to 80, uses environment variables for DB connection.
   - `db`: Uses `mysql:8.0`, sets up a database `myapp` with root password `secret`, persists data in `db_data` volume.
   - `depends_on`: Hints Compose to start `db` before `app` (though the app may need retry logic for DB readiness).
   - `volumes`: Declares `db_data` for persistence.

5. **Run the App**:
   Start:
   ```bash
   docker-compose up --build
   ```
   **Command Prompt (single-line):**
   ```bash
   docker-compose up --build
   ```
   Watch logs for MySQL starting (`ready for connections`) and Flask running.

6. **Test the App**:
   Open `http://localhost:8000`. You should see a message like “Page visited 1 times, connected to DB at db”. Refresh to increment the count. Stop and restart (`docker-compose down` then `up --build`) to confirm the count persists (thanks to the volume).

7. **Clean Up**:
   ```bash
   docker-compose down
   ```
   **Command Prompt (single-line):**
   ```bash
   docker-compose down
   ```
   To remove the volume:
   ```bash
   docker volume rm my-compose-app_db_data
   ```
   **Command Prompt (single-line):**
   ```bash
   docker volume rm my-compose-app_db_data
   ```

**Debugging Tips**:
- **App can’t connect to DB**: Add retry logic in `app.py` or check logs:
  ```bash
  docker-compose logs app
  ```
  **Command Prompt (single-line):**
  ```bash
  docker-compose logs app
  ```
- **MySQL errors**: Check logs (`docker-compose logs db`) for password or memory issues.
- **Volume issues**: Verify `db_data` exists (`docker volume ls`) and is mounted correctly.

**What We Did**: You built a custom multi-container app with a Flask app and MySQL, using environment variables for flexibility and a volume for persistence. This mirrors real-world app development.

## Conclusion

Congratulations on mastering Docker Compose in Week 8-B! 🎉 You:
- Learned YAML and ran a single Nginx container.
- Orchestrated Nginx and PostgreSQL together.
- Built a Node.js app with Redis, using Compose to handle the build.
- Explored service discovery with Compose’s DNS.
- Scaled a web app and used Nginx for load balancing.
- Built your own multi-container app with a database, environment variables, and persistent storage.

These skills are foundational for container orchestration, applicable to tools like Kubernetes. Keep experimenting! Try adding a third service (e.g., Adminer for MySQL) or explore the [Docker Compose file reference](https://docs.docker.com/compose/compose-file/compose-file-v3/) for advanced features like `env_file` or `healthchecks`.

**Sources**:
- [YAML Basics](https://circleci.com/blog/what-is-yaml-a-beginners-guide/)
- [Docker Compose Overview](https://docs.docker.com/compose/)
- [Docker Networking](https://docs.docker.com/compose/networking/)
- [Volumes in Compose](https://kinsta.com/knowledgebase/docker-compose-volumes/)
- [Redis Overview](https://www.ibm.com/topics/redis)
- [Nginx Documentation](https://nginx.org/en/docs/)
- Week 8-B Lab Manual