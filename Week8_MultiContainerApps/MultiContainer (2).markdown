# Week 8: Multi-Container Applications and Docker Compose

## Introduction

Hello again! In Week 8 of Web Application Development, we’re going to level up our Docker skills by running an app that uses multiple containers. Last week, we ran a Node.js to-do app in a single container (with a simple SQLite database file). This week, we’ll split the app into two containers: one for the to-do app and one for a MySQL database. You’ll learn how to make these containers talk to each other using a Docker network, and how to simplify running multi-container apps using Docker Compose. All instructions will be for Windows 10/11 using PowerShell, and we’ll explain everything in simple terms. By the end, you’ll have the to-do app storing its data in a MySQL database container, all managed with a single easy Compose command!

## What You’ll Learn

- **Multi-Container Apps:** What they are and why it’s better to split an application into multiple containers (for example, separate app and database containers).
- **Container Networking:** How Docker networks allow containers to communicate with each other while keeping them isolated from everything else.
- **Docker Volumes:** Using a volume for MySQL data so that the database data persists (doesn’t disappear) even if the MySQL container is recreated.
- **Environment Variables:** Configuring containers by passing in settings like database passwords and hostnames, and how our app uses these to find the database.
- **Docker Compose:** A tool to define and run multi-container applications easily. You’ll learn to write a Compose YAML file to set up both containers and bring the whole app up or down with one command.
- **Docker Compose YAML Syntax:** What a compose.yaml file looks like – defining services, networks, volumes, and environment variables in a simple format.
- **Compose Commands:** How to start the app stack with docker compose up and tear it down with docker compose down.
- **Docker Dashboard Integration:** Seeing how Docker Desktop’s Dashboard shows a Compose app as a unified project with multiple containers.
- **Image Build Best Practices:** An introduction to Docker image layering, caching, and multi-stage builds – advanced techniques that make building images faster and more efficient.

## What You’ll Do

1. **Understand Multi-Container Apps:** Learn what multi-container applications are and why using separate containers (for example, separating the database from the front-end app) is useful.
2. **Create a Docker Network:** Make a new Docker network to allow our app container and database container to communicate with each other.
3. **Run a MySQL Database Container:** Start a MySQL database in its own container, attach it to the network, and set it up with a password and initial database using environment variables.
4. **Connect the App to MySQL:** Reconfigure and run the to-do app container so it connects to the MySQL database via the network. We’ll pass connection info (like host, user, password, database name) to the app through environment variables.
5. **Confirm Database Functionality:** Add some items to the to-do list and then confirm that they are being saved in the MySQL database (by checking the data directly in the MySQL container).
6. **Learn About Docker Compose:** Discover how Docker Compose can simplify running multi-container apps by using a single YAML configuration file.
7. **Write a Compose File:** Create a compose.yaml file that defines both the app service and the MySQL service, along with their network and volume setup.
8. **Start the App with Compose:** Use docker compose up to launch the entire app (both containers) with one command, using the configuration from the Compose file.
9. **Use Docker Dashboard:** Open Docker Desktop’s Dashboard to see how it displays our multi-container app as a grouped project, making it easy to manage.
10. **Tear Everything Down:** Shut down and remove the containers and network with docker compose down (and learn how to clean up volumes if needed) once you’re done.
11. **Understand Image Layering & Multi-Stage Builds:** Get a simple explanation of how Docker builds images in layers (caching to speed up rebuilds) and how multi-stage builds can help create lean production images. Links to further reading will be provided for deeper exploration.

## Prerequisites

- **Docker Desktop installed and running on Windows** (as set up in Week 7). Ensure Docker is running and you have a PowerShell or Command Prompt open and ready.
- **Week 7 To-Do App Code/Image:** We will use the same getting-started to-do app from Week 7. Make sure you still have the app’s code on your machine (the getting-started-app folder you cloned from GitHub) and the Docker image you built for it. If not, refer back to Week 7 to clone the app’s code and build the Docker image for the Node.js to-do app. (Alternatively, we will demonstrate running it using the official Node image and bind mounting the code for development, so having the code is important.)

Alright, let’s dive into multi-container applications!

## Step 1: Multi-Container Apps – Why Use Multiple Containers?

Up to now, you’ve been running everything in a single container. For example, the Node.js to-do app and its SQLite database ran inside one container. Multi-container apps split different pieces into separate containers. In our case, we will have one container for the Node.js application and another for the MySQL database. But why do this? Why not just install MySQL inside the same container as the app? There are several good reasons to use multiple containers instead of one big container:

- **Independent Scaling:** You might need to run more copies of the front-end/web app to handle users, but you usually only need one database (or a different scaling strategy for databases). Separating them means you can scale (run multiple containers of) the app without cloning the database each time, and vice versa.
- **Isolation of Concerns:** Each container has a single responsibility. For example, the database container only runs MySQL and manages data, and the app container only runs the Node.js app. This keeps things simpler and each container “does one thing well.” It also means you can update or debug one part without affecting the other.
- **Different Environments & Versions:** You can upgrade or change the app or database separately. Maybe you want to update your app to a new version but keep the database the same (or update MySQL to a new version without changing your app code). Separate containers let you do this in isolation.
- **Production Parity:** In a real deployment, you might use a managed database service (like a cloud database) rather than a container for the DB. By developing with the database in a separate container, it’s easier later to switch the database to an external service. You wouldn’t ship your database inside your app’s container in production.
- **Simplicity of Process:** Docker containers are designed to run a single process. Running both MySQL and the Node app in one container complicates startup and can cause issues (you’d need a special process manager to run multiple processes in one container). It’s cleaner to keep them separate.

In short, multi-container apps let each part of your stack run in its own container. Our to-do app will run in one container, and MySQL will run in another. They will communicate with each other through a Docker network. Think of it like a team: one team member is in charge of the app, another is in charge of the database. They’re in separate rooms (containers), but we’ll give them a telephone line (a network) so they can talk to each other and work together. Next, we’ll set up that “telephone line” by creating a Docker network.

## Step 2: Creating a Docker Network for the App

For two containers to communicate, they need to be on the same Docker network. By default, Docker containers are isolated – they can’t talk to each other unless you explicitly connect them. A Docker network is like a virtual LAN (Local Area Network) inside Docker: if two containers are on the same network, they can send messages to each other; if not, they’re invisible to each other. We will create a network called todo-app that will link our to-do app and MySQL containers.

Create a Docker network: In PowerShell, run the following command:

```bash
docker network create todo-app
```

**Command Prompt (single-line):**

```bash
docker network create todo-app
```

This command creates a new network named todo-app. We’re not specifying any special settings, so it will be a default bridge network. Think of this as creating a virtual switch that containers can be plugged into. Any container connected to todo-app will be able to talk to other containers on todo-app. (Containers not on this network won’t be able to reach them, which provides isolation.) You can verify the network was created by running docker network ls to list networks – you should see todo-app in the list.

(Optional) What is a bridge network? By default, docker network create makes a bridge network. It’s like a private network inside your computer that connects Docker containers. The name "bridge" comes from the way Docker uses your host to bridge network traffic. Containers on the same bridge network can communicate via IP addresses or names, but they’re isolated from containers on other networks. This is different from the default bridge network that Docker has out-of-the-box; by creating our own named network, we have more control and a clear scope for our app.

**Debugging Tips:**
- **Error: "network todo-app already exists"**: The network was created previously. Verify with `docker network ls` and proceed if `todo-app` exists. To remove and recreate, use `docker network rm todo-app` then rerun the create command.
- **Error: "docker: command not found"**: Ensure Docker Desktop is running (check the system tray icon). If not, start Docker Desktop via the Start menu and wait for it to stabilize.

Now that we have the todo-app network, let’s launch the MySQL database container and attach it to this network.

## Step 3: Running a MySQL Database Container

We’re going to run a MySQL database in a separate container and configure it for our to-do app. We’ll attach it to the todo-app network we created, and we’ll also use a volume to store the database data. Using a volume ensures that the data (our to-do items) won’t vanish if the MySQL container is removed or updated – the data will persist on the Docker-managed volume on your disk. Why MySQL? MySQL is a popular open-source relational database. Our to-do app has code to use MySQL if available (it checks environment variables for a MySQL connection). This will replace the SQLite file database with a more traditional database server. Here’s how to start the MySQL container:

Run the MySQL container on the network: In PowerShell, run the following command (we use backticks ` for line continuation in PowerShell to keep it readable):

```powershell
docker run -d `
    --network todo-app --network-alias mysql `
    -v todo-mysql-data:/var/lib/mysql `
    -e MYSQL_ROOT_PASSWORD=secret `
    -e MYSQL_DATABASE=todos `
    mysql:8.0
```

**Command Prompt (single-line):**

```bash
docker run -d --network todo-app --network-alias mysql -v todo-mysql-data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=secret -e MYSQL_DATABASE=todos mysql:8.0
```

Let’s break down this long command:

- **docker run -d**: docker run starts a new container from an image. The -d flag means “detached” mode – it runs the container in the background so our terminal isn’t tied up (you’ll just get a container ID back).
- **--network todo-app**: This connects the container to the todo-app network (the one we created in Step 2). This is like plugging the MySQL container into the virtual network switch so other containers on that network can reach it.
- **--network-alias mysql**: This gives the container a hostname alias on the network. In simple terms, we are saying “on the todo-app network, this container will also respond to the name mysql.” This is handy because our app will look for a host named "mysql" when trying to connect to the database. By setting this alias, the app container can just use mysql as the database host, and Docker’s internal DNS will resolve it to the IP address of this MySQL container. (If this sounds complicated, just remember: thanks to the alias, our app can find the database by using the name "mysql" instead of an IP address.)
- **-v todo-mysql-data:/var/lib/mysql**: This sets up a named volume called todo-mysql-data and mounts it at /var/lib/mysql inside the container. MySQL stores its database files in /var/lib/mysql by default. By using a volume, all the data MySQL writes to that folder will actually be stored in a persistent volume on the host. This way, if the container stops or is removed, the data (the to-do items in the database) remains saved in todo-mysql-data. We didn’t create todo-mysql-data beforehand, but Docker will see we named a volume that doesn’t exist and create it automatically for us. (Named volumes are managed by Docker – think of it as Docker plugging in a durable USB drive for your database container).
- **-e MYSQL_ROOT_PASSWORD=secret**: The -e flag sets an environment variable in the container. Here we set the MySQL root user’s password to "secret". The MySQL Docker image looks at the MYSQL_ROOT_PASSWORD variable when it first starts; if it’s set, the MySQL server will use that password for the root user. We choose "secret" as a simple password for our lab – in real life, you’d use a stronger password and not hard-code it in a command.
- **-e MYSQL_DATABASE=todos**: Another environment variable; this one tells the MySQL image to create a new database named "todos" on startup. Our to-do app will use this database to store its data (instead of using a SQLite file). By setting this variable, when the container initializes, it will automatically create an empty database called todos for us to use. (The MySQL image can do a lot more via environment variables – you can create users, set their passwords, etc., but for now we just need a database and the root password.)
- **mysql:8.0**: This is the image name and tag. We’re using the official MySQL image, version 8.0. If you don’t have this image yet, Docker will download it from Docker Hub automatically before running the container.

When you run this command, you should get back a long container ID (hexadecimal string). The MySQL database server is now running inside that container, in the background.

Verify MySQL is running: To check, you can run docker ps to list running containers. You should see an entry for mysql:8.0 (the image) with a name like some-auto-generated-name or todo-app_mysql_1 (the name might vary). The STATUS should be “Up ...” if it’s running. You now have a MySQL database up and running in Docker! 🎉 Docker automatically put this MySQL container on the todo-app network (because we used the flag), so it’s ready to communicate with any other container on that network. Next, we’ll connect our to-do app to this database.

**Debugging Tips:**
- **Error: "Cannot connect to the Docker daemon"**: Ensure Docker Desktop is running (check the system tray icon). Start it via the Start menu and wait for it to stabilize.
- **Error: "no such file or directory" or volume issues**: Ensure the volume name `todo-mysql-data` is correct and hasn’t been deleted manually. Check with `docker volume ls`.
- **MySQL fails to start**: Run `docker logs <mysql-container-id>`. Look for errors like invalid passwords (must be non-empty) or memory issues (increase Docker Desktop’s memory in Settings > Resources).
- **Image pull fails**: Ensure internet access. Retry or check Docker Hub for `mysql:8.0` availability.

## Step 4: Connecting the App Container to MySQL via Environment Variables

Now that MySQL is up, we need to run our to-do app container in a way that it knows about the MySQL database and uses it. In our Node.js to-do app code, it will look for certain environment variables (like MYSQL_HOST, MYSQL_USER, etc.) to decide whether to use MySQL instead of the default SQLite. So, we will start the app container with those environment variables set, and also connect the app container to the same network so it can reach MySQL. Usually, you might have built a Docker image for the app (e.g., an image named getting-started from last week). You could run that image with the appropriate environment variables. Here, we’ll demonstrate a common development setup using the Node.js image directly with a bind mount, just as we did in Week 7 for live reload:

Run the app container with MySQL settings: Make sure you are in the getting-started-app project directory (where the app’s code is, and where the Dockerfile is located). Then run:

```powershell
docker run -dp 127.0.0.1:3000:3000 `
    -w /app -v "$(pwd):/app" `
    --network todo-app `
    -e MYSQL_HOST=mysql `
    -e MYSQL_USER=root `
    -e MYSQL_PASSWORD=secret `
    -e MYSQL_DB=todos `
    node:18-alpine `
    sh -c "yarn install && yarn run dev"
```

**Command Prompt (single-line):**

```bash
docker run -dp 127.0.0.1:3000:3000 -w /app -v "%cd%:/app" --network todo-app -e MYSQL_HOST=mysql -e MYSQL_USER=root -e MYSQL_PASSWORD=secret -e MYSQL_DB=todos node:18-alpine sh -c "yarn install && yarn run dev"
```

Breaking this down:

- **-dp 127.0.0.1:3000:3000**: We’ve seen -d (detached) already. -p 127.0.0.1:3000:3000 publishes the container’s port 3000 to 127.0.0.1:3000 on the host. This means you can open http://localhost:3000 in your browser and reach the app. We bind it to 127.0.0.1 (the loopback address) to restrict access to just your local machine. (If you used simply -p 3000:3000, it would also be accessible via your local network, which we don’t need right now.)
- **-w /app**: This sets the working directory inside the container to /app. That means any commands run in the container (like the sh -c "..." we’ll provide) will run with /app as the current directory.
- **-v "$(pwd):/app"** (PowerShell) or **-v "%cd%:/app"** (Command Prompt): This is a bind mount. It takes the current folder on your host (PowerShell $(pwd) or Command Prompt %cd% expands to the present working directory path) and mounts it to /app in the container. Essentially, the container will see the app’s code. We do this so that the container is running our local code – if you edit the code files, the running app (with the help of yarn run dev) will reload. This is great for development because you don’t have to rebuild the image each time you change something. In short, we’re using the Node.js image and mounting our source code into it.
- **--network todo-app**: Connects the app container to the todo-app network (just like we did for MySQL). Now both containers (app and DB) are on the same network and can talk to each other.
- **Environment Variables (-e ... flags)**: Here’s where we configure the app to use MySQL:
  - **-e MYSQL_HOST=mysql**: This tells the app that the MySQL server is reachable at host mysql. Remember, when we ran MySQL, we set --network-alias mysql. This is where we take advantage of that: by using the name "mysql" here, the app container will resolve “mysql” to the IP address of the MySQL container on the network. In other words, we don’t need to figure out the IP manually; Docker’s network allows us to use the name.
  - **-e MYSQL_USER=root**: The username for the database connection. We’ll use the MySQL root user for now (the default administrative user in MySQL).
  - **-e MYSQL_PASSWORD=secret**: The password for the database. We set the root password to "secret" when starting the MySQL container, so we use the same value here so the app can authenticate with MySQL.
  - **-e MYSQL_DB=todos**: The database name to use. We created a database named "todos" in MySQL (via that MYSQL_DATABASE env). This tells the app which database to use.
- These environment variables are how the app knows “Use MySQL instead of SQLite, and here’s how to connect to it.” In a development environment, using env vars like this is fine. (In production, you might use more secure methods like Docker secrets or configuration files, but env vars are okay for now.)
- **node:18-alpine**: This is the image we’re using for the container. It’s the official Node.js v18 image (Alpine variant). We’re using it to run our app. Essentially, this is like saying “start a temporary container with Node.js installed, so we can run our Node app inside it.” If you built a custom image for the app in Week 7 (like an image named getting-started), you could use that here instead. But using node:18-alpine with a bind mount is a quick way to run the app without rebuilding the image for every change.
- **sh -c "yarn install && yarn run dev"**: This is the command that the container will run on startup. sh -c "..." tells the container’s shell to execute the commands inside the quotes. We do yarn install && yarn run dev:
  - yarn install will install any dependencies (just in case they aren’t already installed in the node_modules folder – since we mounted our code, we want to ensure all packages are there).
  - yarn run dev runs the app in development mode. In our to-do app, the dev script uses a tool called nodemon which keeps the app running and auto-restarts it if files change. This is perfect for development – it means if you edit a file, the container will restart the Node app automatically so you can see your changes immediately.

After running this command, you should get another container ID back. This is the to-do app container, now running and (hopefully) connected to the MySQL database.

Check that the app connected to MySQL: One way to verify is by looking at the container’s logs. Run docker logs -f <container-id> (replace <container-id> with the ID of the Node app container that was just started; you can find it with docker ps). The logs should show the app starting up. You will likely see a line that says:

```vbnet
Connected to mysql db at host mysql
Listening on port 3000
```

This message (specifically the “Connected to mysql db at host mysql”) indicates that the app successfully found the MySQL database and connected to it. Hooray! Your app container is now talking to your MySQL container through the network. The Node app is running on port 3000 inside the container, and we mapped it to localhost:3000, so you can access it from your browser.

Open the app in your browser: Go to http://localhost:3000 in your web browser. You should see the familiar to-do app web interface (just like last week). The difference now is that under the hood, the to-do items you add will be saved to the MySQL database, not a local file.

**Debugging Tips:**
- **Error: "Error: connect ECONNREFUSED"**: The app can’t reach MySQL. Ensure the MySQL container is running (`docker ps`) and on the `todo-app` network (`docker network inspect todo-app`). Verify `MYSQL_HOST=mysql` and the MySQL alias is set.
- **Error: "Access denied for user 'root'"**: Ensure `MYSQL_PASSWORD=secret` matches the MySQL container’s root password. Check logs with `docker logs <mysql-container-id>`.
- **Error: "bind: address already in use"**: Port 3000 is in use. Check with `netstat -ano | findstr :3000` (Command Prompt) or `Get-NetTCPConnection -LocalPort 3000` (PowerShell). If the process is a Docker container, identify it with `docker ps` and stop it gracefully with:
  ```bash
  docker stop <container-id>
  ```
  **Command Prompt (single-line):**
  ```bash
  docker stop <container-id>
  ```
  If the process is not a Docker container (e.g., a local Node.js app or another application), stop it with:
  ```bash
  taskkill /PID <pid> /F
  ```
  **Command Prompt (single-line):**
  ```bash
  taskkill /PID <pid> /F
  ```
  To confirm if a PID belongs to a Docker container, use:
  ```bash
  docker inspect --format '{{.State.Pid}}' <container-id>
  ```
  **Command Prompt (single-line):**
  ```bash
  docker inspect --format "{{.State.Pid}}" <container-id>
  ```
  Compare the PID from `netstat` or `Get-NetTCPConnection` to the container’s PID. If they match, use `docker stop` instead of `taskkill` for a graceful shutdown, allowing the container to clean up resources. Alternatively, use a different port (e.g., `127.0.0.1:3001:3000`).
- **Error: "Invalid HTTP response"**: The app isn’t listening on port 3000. Check logs (`docker logs -f <node-container-id>`) for errors in `yarn install` or `yarn run dev`. Ensure the app code has `app.listen(3000)`.

At this point, we have two containers working together: the app container (Node.js server) and the database container (MySQL). Next, let’s prove that the data is actually being stored in MySQL.

## Step 5: Confirming MySQL is Storing the To-Do Data

Let’s test our multi-container setup by adding an item to the to-do list and then checking directly in the database to see if it’s there.

Add a few to-do items: In the web app (http://localhost:3000), add a couple of items to your to-do list (e.g., “Do math homework” or “Walk the dog”). Mark one as done, leave another as not done – any data will do. The app will send these to the server, and the server will save them in the MySQL database (since it’s connected to MySQL now).

Connect to the MySQL container to inspect the data: We can run a MySQL client within the MySQL container to query the database directly. Docker has a command for running commands in a running container: docker exec. We’ll use it to open a MySQL shell. First, find the container ID or name of the MySQL container. If you still have the PowerShell where you ran it, you might have the ID. Otherwise, run docker ps and look for the container running mysql:8.0. It will have an ID and a name (the name might be something like todo-app_mysql_1 if started via Compose, or a random name like strange_austin if started by docker run unless you gave it one). For example, from your output, the MySQL container is `bdd76e2a38b7` (named strange_austin). Now run a MySQL shell inside that container:

```bash
docker exec -it <mysql-container-id> mysql -u root -p todos
```

**Command Prompt (single-line):**

```bash
docker exec -it <mysql-container-id> mysql -u root -p todos
```

For your specific setup, replace `<mysql-container-id>` with `bdd76e2a38b7` (or `strange_austin`):

```bash
docker exec -it bdd76e2a38b7 mysql -u root -p todos
```

**Command Prompt (single-line):**

```bash
docker exec -it bdd76e2a38b7 mysql -u root -p todos
```

Let’s unpack that:

- **docker exec -it <container> <command>** runs a command inside a running container. -it makes it interactive (so we can interact with the shell).
- **<mysql-container-id>** should be replaced with your MySQL container’s ID or name (e.g., `bdd76e2a38b7` or `strange_austin`).
- **mysql -u root -p todos** is the command we run inside the container. This launches the MySQL client program, logging in as user root (-u root) and -p means it will prompt for the password. todos at the end tells it to directly open the todos database after login.

After running this, you should see a password prompt in the terminal. Enter the MySQL root password, which we set to secret. (Note: when you type the password, it won’t show any characters, not even * – that’s normal for terminal password prompts. Just type secret and hit Enter.) If successful, you’ll get a MySQL prompt like mysql> inside the container. You are now essentially “inside” the MySQL server, connected to the todos database.

Run a SQL query to see the table data: At the mysql> prompt, type the following SQL command and hit Enter:

```sql
SELECT * FROM todo_items;
```

This will show all rows in the todo_items table (which is where the to-do app stores each to-do item). You should see output listing the items you added. For example, it might look something like this:

```text
+--------------------------------------+------------------+-----------+
| id                                   | name             | completed |
+--------------------------------------+------------------+-----------+
| 51477f78-a433-415e-8f36-c9e1d8d43020 | Do math homework |         1 |
| 1fd43713-8ffb-49a6-b686-f0bd65ea9861 | Walk the dog     |         0 |
+--------------------------------------+------------------+-----------+
```

Each to-do item has a unique id (UUID), a name (the text you entered), and a completed status (0 for not done, 1 for done). The exact output will vary based on what you added, but the key point is that you can see your to-do entries in the database table now! If you see them, congratulations – your to-do app is successfully using MySQL as its data store. If you don’t see any rows, double-check that you ran the SELECT on the todos database and that you added items via the web interface. Also, ensure the app was connected (the logs showed “Connected to mysql db…” earlier).

Exit the MySQL shell: Type exit at the mysql> prompt to quit the MySQL shell and return to your normal PowerShell terminal.

**Debugging Tips:**
- **Error: "executable file not found in $PATH" for mysql**: You ran `docker exec` on the Node.js container (e.g., `b17c09de6153`) instead of the MySQL container (e.g., `bdd76e2a38b7`). The Node.js image (`node:18-alpine`) doesn’t have the MySQL client installed. Use `docker ps` to identify the MySQL container (image `mysql:8.0`, e.g., `bdd76e2a38b7` or `strange_austin`) and run `docker exec -it bdd76e2a38b7 mysql -u root -p todos`.
- **Error: "You have an error in your SQL syntax" (ERROR 1064)**: You may have entered an invalid command like `cls` in the MySQL shell. The MySQL shell does not support commands like `cls` (used in some terminals to clear the screen). Instead, ensure you enter valid SQL commands. If you need to clear the terminal, do so in PowerShell or Command Prompt before entering the MySQL shell, or proceed with valid SQL like `USE todos;` or `SELECT * FROM todo_items;`.
- **Error: "No database selected" (ERROR 1046)**: You ran a query without selecting the `todos` database. Before running `SELECT * FROM todo_items;`, ensure you select the database with:
  ```sql
  USE todos;
  ```
  You can verify available databases with `SHOW DATABASES;`.
- **Error: "Access denied for user 'root'"**: Ensure the password is `secret`. Check MySQL logs (`docker logs <mysql-container-id>`) for setup errors.
- **Error: "Table 'todo_items' doesn't exist"**: The app may not have created the table. Ensure the app connected to MySQL (`docker logs <node-container-id>`) and items were added via the web interface.
- **No rows returned**: Confirm items were added at http://localhost:3000. Re-check app logs for connection issues (`docker logs -f <node-container-id>`). Verify the app connected with:
  ```bash
  docker logs <node-container-id>
  ```
  Look for:
  ```vbnet
  Connected to mysql db at host mysql
  Listening on port 3000
  ```

What we just did: We verified that the app’s data is indeed being saved in the MySQL database container. We did this by querying the database directly. This confirms our multi-container setup works: the Node app container talked to the MySQL container over the Docker network, and the data was stored in the MySQL container’s volume (so it’s persisted). Now, while having two containers is great, you might have noticed it’s a bit tedious to start each one with the right options (network, volumes, env vars) every time. In fact, running this app required:

- Creating a network,
- Running the MySQL container with a long command of options,
- Running the app container with another long command of options (and we had to remember to do it in the right order, with the right network and env settings).

That’s a lot of steps to remember and repeat. Wouldn’t it be nice if we could just define all this in a file and run one command to start everything? That’s exactly what Docker Compose helps us do. 🎉

## Step 6: Simplifying with Docker Compose

Docker Compose is a tool that makes it easier to run multi-container applications. Instead of typing out long docker run commands for each container and remembering to create networks or volumes, we write all the configuration in a Compose file (a YAML file, usually named compose.yaml or docker-compose.yaml). Then a single command docker compose up will set up everything as described in that file. Think of Docker Compose as a conductor for an orchestra of containers:

- You write down the “sheet music” (the YAML file) describing which containers (services) should play, what images they use, how they connect (networks), volumes, environment variables, etc.
- Then Docker Compose (the conductor) reads that and brings up all the containers in harmony with one command, and likewise can stop them all with one command.

Why use Compose?

- **It’s repeatable and shareable:** The Compose YAML file can be committed to your project’s code repository. Anyone who checks out the project can simply run docker compose up and have the exact same multi-container environment. No need to send a list of commands or a long README of how to run each part – the file is the single source of truth.
- **It simplifies complex setups:** You no longer have to remember all the flags for each container. The YAML file can also serve as documentation of what’s going on (ports, volumes, env vars are all listed clearly).
- **It manages the network for you:** Compose will automatically create a network for your app stack so your containers can talk to each other, without you having to manually create one (unless you want to customize it). By default, Compose will make a network that all services in the stack use.
- **It manages named volumes:** If your Compose file declares a named volume (like our todo-mysql-data for MySQL), Compose will ensure it’s created before bringing up the containers. It also won’t delete the volume on down by default, to protect your data (unless you ask it to).
- **It groups logs and lifecycle:** You can start all containers with one command, and stop them all with one command. You can also watch logs from all services with one command (docker compose logs), which is super handy for debugging timing issues between containers because you see everything in one combined stream.

Overall, Docker Compose is like a recipe for your multi-container application. Let’s write that recipe for our to-do app + MySQL setup.

## Step 7: Writing a Docker Compose YAML File

We will create a Compose file that describes our two services: app (the Node to-do app) and mysql (the MySQL database). In this file, we’ll specify the same information we used in the docker run commands, but in a structured way.

Create the Compose file: In the getting-started-app directory (or wherever your app code is), create a new file named compose.yaml. You can do this with a text editor or via PowerShell:

```bash
cd getting-started-app
type NUL > compose.yaml
```

**Command Prompt (single-line):**

```bash
cd getting-started-app && type NUL > compose.yaml
```

Open compose.yaml in a text editor (Notepad, VS Code, etc.). Now we’ll fill it in.

Define the services in the file: Below is the complete compose.yaml we need. It might look a bit complex, but it’s just a translation of what we’ve already done via command-line flags into YAML format:

```yaml
services:
  app:
    image: node:18-alpine
    command: sh -c "yarn install && yarn run dev"
    ports:
      - "127.0.0.1:3000:3000"
    working_dir: /app
    volumes:
      - ./:/app
    environment:
      MYSQL_HOST: mysql
      MYSQL_USER: root
      MYSQL_PASSWORD: secret
      MYSQL_DB: todos

  mysql:
    image: mysql:8.0
    volumes:
      - todo-mysql-data:/var/lib/mysql
    environment:
      MYSQL_ROOT_PASSWORD: secret
      MYSQL_DATABASE: todos

volumes:
  todo-mysql-data:
```

Let’s explain what each part means:

- **services:** This is the top-level section where we list our two services (containers).
- **app:** This is the name of the first service. We call it “app” (you can choose names, but these are logical identifiers). This name will also become the network alias for the container. In other words, other containers can find this container by using the hostname "app" on the network (we’ll see how that’s useful in a moment for MySQL). Compose will also use this name in container names (it combines it with the project name).
- **image: node:18-alpine**: We want this service to use the Node.js 18 Alpine image. This is exactly what we did on the command line. Compose will pull the image if it’s not available.
- **command: sh -c "yarn install && yarn run dev"**: This specifies the command to run in the container (overriding the default for the image). It’s the same as what we passed after the image name in docker run. It installs dependencies and starts the dev server. By putting it here, we ensure that whenever this service starts, it runs our app in development mode.
- **ports: - "127.0.0.1:3000:3000"**: This publishes the container’s port 3000 to localhost:3000 on the host. In YAML, we specify it as a string in quotes. It’s equivalent to -p 127.0.0.1:3000:3000 in Docker run. (The quotes are needed in YAML because the : in the value might confuse the parser otherwise.) This means our web app will be accessible in the browser as before.
- **working_dir: /app**: Sets the working directory inside the container to /app (like -w /app).
- **volumes: - ./:/app**: Attaches the current directory (indicated by ./) into the container at /app. This is the bind mount for our source code. In Compose, using a relative path like ./ refers to the directory where the compose.yaml file is located (so make sure the compose file is in the root of the project, which it is in our case). This line is equivalent to -v "$(pwd):/app" in the docker run command.
- **environment:** Here we list environment variables for the service. We can write them as key-value pairs on separate lines (YAML mapping). We set:
  - MYSQL_HOST: mysql
  - MYSQL_USER: root
  - MYSQL_PASSWORD: secret
  - MYSQL_DB: todos
- These do the same thing as all the -e flags we used earlier, telling the app how to connect to the database. Notice MYSQL_HOST: mysql – how will the app know what “mysql” means? This is where Compose’s magic comes in: by default, Compose will create a network for these services, and every service can be reached by its name on that network. We named our database service mysql, so the app can reach it at hostname “mysql”. That matches perfectly what we put here (and what our app expects). So we no longer need to manually specify network aliases; Compose does it by naming services.
- **mysql:** Now the second service, named “mysql”. This will be our database container.
- **image: mysql:8.0**: Use the MySQL 8.0 image.
- **volumes: - todo-mysql-data:/var/lib/mysql**: We mount a volume for MySQL data. On the left of the colon is todo-mysql-data, which is the name of a named volume we want to use. On the right is /var/lib/mysql, the path in the container where MySQL stores its database files. This is the same mapping we did with -v in the docker run command. One key difference: Docker Compose will not automatically create a named volume just because you named it here. We have to declare this volume in the top-level volumes: section of the compose file (see at the bottom). We’ll do that in a second. By declaring it here, we’re saying “attach the volume named todo-mysql-data to this container at /var/lib/mysql.”
- **environment:** We set environment vars for MySQL:
  - MYSQL_ROOT_PASSWORD: secret – sets the root password (same as before).
  - MYSQL_DATABASE: todos – creates the todos database on startup (same as before).
- (If you needed other settings like a user, you could add more, but we’re keeping it simple.)
- After listing services, we have a separate top-level section:
- **volumes:** Here we declare any named volumes we used in the services. We have one named volume: todo-mysql-data:. We just write its name under volumes. We don’t have to specify any further options for it (by default, it will be a local volume managed by Docker). This declaration tells Compose “make sure a volume named todo-mysql-data exists (create it if not)”. Now our MySQL service can use it. If we didn’t declare this, Compose would complain that todo-mysql-data volume is not defined.

That’s it! We have described everything needed for our two-container application. Make sure to save the compose.yaml file.

Tip: Notice we did not explicitly define a network in this file. Docker Compose will automatically create a network for your app stack (and a default name for it, usually based on the directory name). All services are connected to that default network unless specified otherwise. This means our app and mysql services are on the same network out of the box and can communicate. The service names (app, mysql) automatically become hostnames on that network. So app can reach mysql and vice versa. We leveraged that for MYSQL_HOST: mysql. Compose’s auto-networking simplifies what we had to do manually with docker network create and --network flags before.

**Debugging Tips:**
- **YAML syntax error**: Ensure indentation is 2 spaces (no tabs) and colons are followed by spaces (e.g., `image: mysql:8.0`). Use a YAML validator like [yamlLint](http://www.yamllint.com/) if errors persist.
- **Volume not found**: Ensure `todo-mysql-data` is listed under `volumes:`. Check `docker volume ls` for existing volumes.
- **File not found**: Confirm `compose.yaml` is in the `getting-started-app` directory and the path in `volumes: - ./:/app` points to the correct directory.

## Step 8: Starting the App with Docker Compose

Now that our Compose file is ready, running our multi-container application is super easy!

Bring up the application stack: In the terminal (and in the directory containing compose.yaml), run:

```bash
docker compose up -d
```

**Command Prompt (single-line):**

```bash
docker compose up -d
```

(Note: This is docker compose, not docker **-**compose. In recent versions of Docker, Compose is integrated into the docker command. If you have an older setup, you might need to run docker-compose up -d with a hyphen. But Docker Desktop on Windows 10/11 should support the docker compose syntax.) What this does:

- Docker Compose reads your compose.yaml.
- It checks the defined services. If any images need to be built or pulled, it will do that. In our case, node:18-alpine and mysql:8.0 are official images, so it will pull them if not already downloaded.
- It creates a new network for the app stack (since we didn’t specify one, it uses a default name, likely based on your project folder name, e.g., if your folder is getting-started-app, the network might be named getting-started-app_default).
- It creates the named volume todo-mysql-data (since we declared it).
- Then it starts both containers: one for app and one for mysql. The names of the containers will be prefixed with the project name (again, usually the folder name) and the service name. You’ll see something like getting-started-app_app_1 and getting-started-app_mysql_1 when they start.

Because we used -d, Compose will run in detached mode, so it won’t stream logs to the console. You should just get your prompt back after it sets everything up. The output might show something like:

```csharp
Creating network "getting-started-app_default" with the default driver
Creating volume "getting-started-app_todo-mysql-data" with default driver
Creating getting-started-app_mysql_1 ... done
Creating getting-started-app_app_1   ... done
```

(The exact naming might differ based on your directory/project name). This indicates it made the network, created the volume, and started the two containers.

Verify the containers are running: You can again use docker ps to see active containers. You should see both the app and mysql containers up. Alternatively, as we’ll do in the next step, you can use Docker Dashboard for a nice visual confirmation.

Check the logs (optional): If you want to see the combined logs of both containers, you can use:

```bash
docker compose logs -f
```

**Command Prompt (single-line):**

```bash
docker compose logs -f
```

This will follow (-f) the logs of all services in the Compose app. You’ll see intermixed logs, each line prefixed by the service name, e.g., you might see lines starting with app_1 | ... and mysql_1 | .... The MySQL container might log startup messages and say it’s ready for connections, and the app container should log that it connected to the database and is listening on port 3000. Press Ctrl+C to stop following the logs (this doesn’t stop the containers, just stops showing logs).

Test the app quickly: Just like before, open http://localhost:3000 in your browser. The app should be running (since the app container is up). Try adding a to-do item to double-check everything is working. It should behave exactly as it did in Step 5.

**Debugging Tips:**
- **Error: "port is already allocated"**: Check port 3000 with `netstat -ano | findstr :3000` (Command Prompt) or `Get-NetTCPConnection -LocalPort 3000` (PowerShell). If the process is a Docker container, identify it with `docker ps` and stop it gracefully with:
  ```bash
  docker stop <container-id>
  ```
  **Command Prompt (single-line):**
  ```bash
  docker stop <container-id>
  ```
  If the process is not a Docker container (e.g., a local Node.js app or another application), stop it with:
  ```bash
  taskkill /PID <pid> /F
  ```
  **Command Prompt (single-line):**
  ```bash
  taskkill /PID <pid> /F
  ```
  To confirm if a PID belongs to a Docker container, use:
  ```bash
  docker inspect --format '{{.State.Pid}}' <container-id>
  ```
  **Command Prompt (single-line):**
  ```bash
  docker inspect --format "{{.State.Pid}}" <container-id>
  ```
  Compare the PID from `netstat` or `Get-NetTCPConnection` to the container’s PID. If they match, use `docker stop` instead of `taskkill` for a graceful shutdown, allowing the container to clean up resources. Alternatively, change the port in `compose.yaml` to `127.0.0.1:3001:3000`.
- **Error: "Cannot connect to the Docker daemon" or "open //./pipe/dockerDesktopLinuxEngine: The system cannot find the file specified"**: Docker Desktop is not running or is misconfigured. Quit Docker Desktop (right-click system tray icon > Quit Docker Desktop), then restart it via the Start menu. Wait 1–2 minutes for it to stabilize. Verify with:
  ```bash
  docker version
  ```
  **Command Prompt (single-line):**
  ```bash
  docker version
  ```
  Look for Server info (e.g., `Server: Docker Desktop 4.x.x (Windows)`). If it still fails, restart the Docker service in PowerShell (Admin) with:
  ```powershell
  Restart-Service com.docker.service
  ```
  **Command Prompt (single-line, Admin):**
  ```bash
  net stop com.docker.service && net start com.docker.service
  ```
  If using WSL2 backend, ensure WSL2 is enabled (`wsl --list`) and Docker Desktop is set to use it (Settings > General > Use WSL2 based engine). Retry `docker compose up -d`.
- **App not reachable at http://localhost:3000**: Check logs (`docker compose logs -f`). Ensure `app_1` logs show “Listening on port 3000” and no errors in `yarn install`.
- **MySQL not reachable**: Verify `mysql_1` is running (`docker ps`) and logs show “ready for connections” (`docker compose logs mysql`). Ensure `MYSQL_HOST: mysql` in `compose.yaml`.

The huge benefit now is that if you were to share this project with someone, all you need to tell them is: “Install Docker and run docker compose up.” They would get the same multi-container app running without needing to follow dozens of steps. 👍

## Step 9: Viewing the App in Docker Dashboard

Docker Desktop comes with a nice Dashboard GUI that lets you see containers and manage them. When using Docker Compose, the Dashboard groups containers by the project (which by default is the folder name or you can set it with a flag).

Open Docker Dashboard: Click the Docker icon in your system tray and choose "Dashboard" (or search for Docker Desktop application). Make sure “Containers” is selected in the left menu.

Find your app stack: You should see a listing for your Compose project, likely named after your directory. For example, it might show getting-started-app (if that’s your folder name). It will appear as a group or folder containing multiple containers. This group corresponds to the Compose application we just launched.

Expand the group: Click on your app’s name to see the containers inside. You should see two containers listed: one for app and one for mysql. Docker Compose gives them names like <project>_<service>_1. For instance, “getting-started-app_app_1” and “getting-started-app_mysql_1” – these correspond to the app and mysql services we defined. Docker appended “_1” because it’s the first instance of each service (Compose can scale services to multiple instances, but that’s beyond our scope here).

Explore details (optional): You can click each container to see logs, stats (CPU/memory usage), environment variables, etc., in the dashboard. It’s a convenient way to monitor your multi-container app. You can also stop, pause, or delete containers from the UI (but be careful, if you want to manage them as a group it’s usually better to use Compose commands or the group actions).

The Docker Dashboard essentially shows that our two containers are part of one application “stack” (the Compose project). This grouping makes it clear which containers go together. If you had other unrelated containers running, they’d be listed separately outside this group.

**Debugging Tips:**
- **No project in Dashboard**: Ensure `docker compose up -d` ran successfully. Check `docker ps` for containers and `docker compose logs` for errors.
- **Containers stopped**: Restart with `docker compose up -d`. Check logs for crash reasons (e.g., MySQL out of memory or app code errors).

## Step 10: Tearing Everything Down

When you’re done with the app, you’ll want to stop and remove the containers (especially to avoid using up resources or to prepare for a clean start later). Docker Compose makes this easy:

Shut down the Compose application: In your project directory, run:

```bash
docker compose down
```

**Command Prompt (single-line):**

```bash
docker compose down
```

This will stop and remove all containers defined in the compose file, and also remove the default network that was created for the app stack. Essentially, it cleanly unplugs and packs away all the containers that up started. You should see output indicating it’s stopping and removing containers, e.g.,:

```bash
Stopping getting-started-app_app_1   ... done
Stopping getting-started-app_mysql_1 ... done
Removing getting-started-app_app_1   ... done
Removing getting-started-app_mysql_1 ... done
Removing network getting-started-app_default
```

Volumes are not removed by default: One important thing to know: by default, named volumes you declared (like our todo-mysql-data) are NOT deleted when you run docker compose down. This is a safety feature. It means if you bring the app back up later, your to-do data is still there in the database (the volume persists). If you want to also remove the volumes when bringing down the app (for a completely clean state), you can add the --volumes flag, like docker compose down --volumes. Be careful with that – it will delete the volume and all data in it. The Docker Dashboard doesn’t remove volumes when you delete an app stack either, so if you remove the app via the Dashboard GUI, the volume may still remain on your system.

Confirm everything is down: After docker compose down, run docker ps. You shouldn’t see the app or mysql containers anymore (since they’ve been removed). You can run docker volume ls to see that the volume todo-mysql-data still exists (unless you removed it). If you know you won’t need the data or want to reclaim space, you can remove that volume with docker volume rm todo-mysql-data at any time.

**Command Prompt (single-line for volume removal):**

```bash
docker volume rm getting-started-app_todo-mysql-data
```

**Debugging Tips:**
- **Error: "volume in use"**: Ensure containers are stopped (`docker ps -a` and `docker rm -f <container-id>`). Then retry `docker volume rm`.
- **Containers still running**: Run `docker compose down` again or manually stop with `docker stop <container-id>` and remove with `docker rm <container-id>`.

## Step 11: Image Build Best Practices – Layering, Caching, and Multi-Stage Builds

This step is a little different – it’s not about running the containers, but about building Docker images efficiently. When developing apps, you’ll often rebuild images as you update code. Docker has some features that make this faster and produce smaller images. Image Layers and Caching:

When you built the Docker image for the to-do app (Week 7’s Dockerfile), each instruction in the Dockerfile (like FROM, COPY, RUN, etc.) created a layer in the image. Think of layers like a stack of transparent sheets, each layer adding something on top of the previous. Docker caches these layers. So, if you build the image again and a layer hasn’t changed (for example, you didn’t modify the lines above it in the Dockerfile or the files it depends on), Docker can reuse the cached layer from last time instead of re-executing that step. This can speed up rebuilds dramatically. In our case, if you only changed the app’s source code but not the dependencies, Docker would reuse the layer that had yarn install (as long as package.json didn’t change) and only redo the final steps, making the build much faster on the second run. Layer caching also means when you push images to a registry or pull them, if some layers are unchanged from a previous build, those layers don’t need to be sent again. Multi-Stage Builds:

Sometimes, you need a lot of stuff to build your application (compilers, build tools, etc.), but you don’t want all those things in the final image that you deploy, because they can make it huge or less secure. Docker supports multi-stage builds, which allow you to use multiple FROM statements in one Dockerfile. You can have a “build stage” that has all the tools to compile your app, and a “final stage” that’s minimal, just to run your app. The final stage can copy only the necessary artifacts (like the compiled code or binary) from the build stage and leave everything else behind. This results in a smaller final image and you don’t ship your build tools with it. For example, imagine you’re compiling a C++ application. You might use a big image with a compiler to build the app, then in a second stage, use a tiny base image and copy the compiled binary over. Or for our Node app, perhaps we could use a multi-stage build to install dev dependencies in one stage and then only copy production files to a slimmer image. Multi-stage builds help keep images lean and mean, and also logically separate the build environment from the runtime environment. Why does this matter to you?

As you start writing your own Dockerfiles or modifying them, knowing about layering and caching can help you structure your Dockerfile for speed. For instance, if you put the most frequently changing commands last, you maximize reuse of cached layers for the earlier steps. Multi-stage builds are an advanced technique, but it’s good to be aware that they exist – they’re very useful for production images. This lab doesn’t require you to implement multi-stage builds; it just encourages you to read about them and layer caching. If you’re curious to learn more, check out Docker’s official guide on Image Best Practices (which covers layering and multi-stage builds in more depth). The guide has examples and a thorough explanation of how Docker builds images and how you can optimize your Dockerfiles. Further Reading:

- Docker Docs – Image building best practices: [Part 8: Image-building Best Practices (Docker Get Started Guide)](https://docs.docker.com/get-started/workshop/09_image_best/) – This is a great resource to understand image layers, caching, and multi-stage build examples.
- Docker Docs – Dockerfile Best Practices: [Dockerfile Best Practices](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/) – General tips from Docker on writing efficient Dockerfiles (like ordering of commands, reducing image size, etc.).

**Debugging Tips:**
- **Slow image builds**: Ensure `package.json` and `yarn.lock` are copied before `RUN yarn install` in the Dockerfile to leverage caching. Reorder Dockerfile commands to place frequently changing files (e.g., source code) last.
- **Large image sizes**: Consider multi-stage builds to exclude dev dependencies. Check image size with `docker images` and compare with `node:18-alpine`.

## Congratulations on getting through Week 8! 🙌

You’ve successfully taken a simple app and split it into multiple containers, connected them with a network, used a volume for data persistence, and then simplified the setup with Docker Compose. This mirrors how real-world applications are managed – databases, back-end services, front-ends, etc., all in separate containers, orchestrated together. You also got a peek into how Docker builds images efficiently. In the upcoming lessons, you might explore even more tools (like Kubernetes or advanced Docker Compose features), but the fundamentals you learned here will apply everywhere. Keep experimenting and happy containerizing! 🐳🚀

**Sources:** Docker documentation and Week 8 lab manual content