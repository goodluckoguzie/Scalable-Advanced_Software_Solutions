# Week 7: Introduction to Docker – Building, Running, and Sharing Containers

## Introduction

Hello! In Week 7 of Web Application Development, we’re going to learn about **Docker**, a powerful tool for running applications in isolated environments called **containers**. Think of a container like a **shipping container for software** – it packages an app with everything it needs so it can run anywhere. We’ll explain everything step by step using Windows 10/11 and PowerShell. By the end of this tutorial, you’ll have a simple to-do list web app running in Docker, and you’ll know how to update it, save its data, and even share it with others via Docker Hub.

### What You’ll Learn

* **Docker & Containers:** What containers are and why they’re useful (isolate apps so “it works on my machine” every time).
* **Images:** A Docker **image** is like a recipe or blueprint for containers. You’ll build a custom image for a Node.js to-do app using a **Dockerfile**.
* **Docker Commands:** Basic Docker commands such as `docker build` (to build images), `docker run` (to run containers), `docker ps` (to list containers), `docker stop`/`rm` (to stop/remove containers), and `docker push` (to share images).
* **Port Mapping:** How to make a web application inside a container reachable from your browser (publishing container ports to your computer’s ports).
* **Data Persistence:** By default, data in a container disappears when the container is removed – you’ll learn to **persist data** using Docker **volumes** so your to-do list isn’t lost.
* **Bind Mounts:** A way to share a folder between your computer and the container, useful for development. This lets you edit code on your machine and have the running container update automatically.
* **Docker Hub:** Pushing your container image to Docker Hub (an online registry) so you or others can run it on any machine without rebuilding.

### What You’ll Do

1. **Set Up Docker:** Make sure Docker Desktop is installed and running on Windows, and verify the Docker command works in PowerShell.
2. **Run Hello World:** Pull and run Docker’s “hello-world” test container to check that Docker is set up correctly.
3. **Containerize an App:** Download a simple Node.js **to-do list** app and write a Dockerfile for it. Build a Docker image and run the app in a container. Access the running web app in your browser.
4. **Update the App:** Change the app’s code (e.g., text on the page), rebuild the image, and run a new container. Learn how to deal with an error when the old container is still using the same port, by stopping/removing containers.
5. **Share the Image:** Create a Docker Hub account (if you don’t have one), tag your image, and push it to Docker Hub. Then simulate running it on a new machine (for example, using an online sandbox) to see how Docker pulls the image and runs your app anywhere.
6. **Persist the To-Do Data:** Understand why the to-do list resets each time the container starts. Use a **named volume** to persist the app’s database file so that to-do items stay around between runs.
7. **Use Bind Mounts for Development:** Mount your app’s source code into a container so that as you edit files on Windows, the container picks up the changes. Use a tool called **nodemon** (via `yarn dev`) to automatically restart the app inside the container on changes, giving a fast development cycle.
8. **Clean Up & Summary:** Review what you learned and ensure no unnecessary containers are left running.

### Prerequisites

* **Docker Desktop installed on Windows:** If you haven’t installed it, download it from the Docker website and install. On lab PCs, Docker should already be installed. After installation, you might need to log out and log back in to Windows.
* **Basic PowerShell or Command Prompt skills:** e.g., using `cd` to change directories. (This was covered in earlier weeks.)
* **Git installed:** We will use `git clone` to download the example app. If Git isn’t installed, you can install it from [git-scm.com](https://git-scm.com/downloads) or download the ZIP of the app from GitHub as an alternative.
* **Text Editor or IDE:** You’ll need to edit a couple of files. You can use Notepad (simple, comes with Windows) or an editor like **VS Code** (recommended by Docker, but Notepad is okay for quick edits).
* **Internet Access:** Docker will download images from the internet (Docker Hub), so ensure your PC is online.
* **Docker Hub account (optional but needed for Step 5):** If you plan to push your image to the cloud, sign up for a free account on [hub.docker.com](https://hub.docker.com/signup). You can still do earlier steps without an account.

Alright, let’s dive in!

## Step 1: Setting Up Docker on Windows

Before using Docker, we need to ensure it’s properly installed and running on your computer.

1. **Start Docker Desktop:**
   Docker on Windows runs in the background via **Docker Desktop**. Look for “Docker Desktop” in your Start menu and open it. Give it a minute or two to start up (you might see the whale icon in your taskbar; it will say “Docker is running” once ready).

   *If Docker Desktop isn’t installed, refer to the Docker Docs installation guide and install it first. You may need to restart your PC after installation.*

2. **Open PowerShell:**
   Press the **Start** menu, type “PowerShell”, and open **Windows PowerShell**. This will be our terminal for running Docker commands.

3. **Verify Docker Command:**
   In the PowerShell window, type the command below and press Enter:

   ```bash
   docker --version
   ```

   This should output something like `Docker version 24.0.2, build eba92f..., platform linux/amd64` (the exact version may differ). This confirms the Docker CLI is installed.

   Now try running the `docker` command by itself:

   ```bash
   docker
   ```

   This will list available Docker commands (it’s a lot of text). Seeing this output means the Docker CLI can talk to the Docker engine.

   *If you get an error like “**docker: error during connect:** … **docker daemon is not running**,” it means Docker Desktop isn’t running. Make sure you started Docker Desktop and wait until it’s fully running (or try restarting it). Once the daemon (Docker’s background service) is up, rerun the command.*

4. **Familiarize with Docker (Optional):**
   Docker has many commands. Here are a few we’ll be using frequently:

   * `docker **pull**` – Download an image from a registry (like Docker Hub) to your computer.
   * `docker **build**` – Build a Docker image from a Dockerfile.
   * `docker **run**` – Run a container from an image. This is how we start containers.
   * `docker **ps**` – List running containers (add `-a` to list all, including stopped ones).
   * `docker **stop**` – Gracefully stop a running container.
   * `docker **rm**` – Remove a stopped container (add `-f` to force remove even if running).
   * `docker **exec**` – Run a command inside an existing running container (useful for debugging or checking files).
   * `docker **images**` – List the images you have on your system.
   * `docker **volume**` – Manage Docker volumes (for persisting data).
   * `docker **login/push**` – Authenticate and upload images to a registry (Docker Hub).

   Don’t worry about memorizing these now – we will explain each as we use them. The key point is: **Docker is a command-line tool** and we’ll be typing commands to build and run our application’s container.

Now that Docker is set up and responding, let’s run a quick test container to make sure everything is working.

## Step 2: Run the “Hello World” Container

Docker Hub has an official test image called **“hello-world”**. We’ll use this to check that Docker can download images and run containers on your machine.

1. **Pull and Run Hello-World:**
   In PowerShell, type the following and press Enter:

   ```bash
   docker run hello-world
   ```

   This command does two things: if the **hello-world** image isn’t already on your computer, it will **pull** (download) it from Docker Hub, then it will **run** a container from that image. You should see some output like this:

   ```
   Unable to find image 'hello-world:latest' locally
   latest: Pulling from library/hello-world
   2db29710123e: Pull complete
   Digest: sha256:xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
   Status: Downloaded newer image for hello-world:latest

   Hello from Docker!
   This message shows that your installation appears to be working correctly.

   To generate this message, Docker took the following steps:
    1. The Docker client contacted the Docker daemon.
    2. The Docker daemon pulled the "hello-world" image from Docker Hub.
    3. The Docker daemon created a new container from that image which ran the executable that produces this output.
    4. The Docker daemon streamed the output to the Docker client, which sent it to your terminal.

   To try something more ambitious, you can run an Ubuntu container with:
    $ docker run -it ubuntu bash

   ... (output truncated) ...
   ```

   Let’s break down what happened:

   * Docker noticed you didn’t have the image locally (“Unable to find image locally”), so it downloaded it from the internet (from the “library/hello-world” repository on Docker Hub). **Tip:** You don’t need to run `docker pull` manually for this; `docker run` will automatically pull an image if you don’t have it yet.
   * Then it created a container from that image. The container ran a small program that printed the “Hello from Docker!” message and some info about what Docker did.
   * After printing, the container exits (hello-world does its job and stops).

   The message confirms Docker is working correctly 🎉. It even suggests the next thing to try: running an Ubuntu container with a bash shell (which we’ll actually do a bit later for another experiment). For now, you can see that Docker successfully ran a container on your system.

   *Troubleshooting:* If you didn’t see the “Hello from Docker!” message, ensure Docker Desktop is running (see Step 1). Also, make sure you have internet so Docker can download images.

Now that we’ve verified Docker, let’s move on to something more interesting: containerizing a real application!

## Step 3: Containerize a Node.js To-Do App

For the rest of this tutorial, we’ll use a simple web application – a **To-Do List manager** written in Node.js (JavaScript). Don’t worry if you don’t know Node.js; we won’t be doing heavy coding. The goal is to learn Docker by containerizing this app and running it.

### 3.1 Get the Application Code

First, we need the app’s source code on our machine.

1. **Choose a Directory:**
   Decide where you want the project code to live. For example, you might create a folder in your Documents or on the Desktop for WAD projects. Let’s use the Desktop for simplicity. In PowerShell, navigate to your Desktop:

   ```bash
   cd $HOME\Desktop
   ```

   *Note:* `$HOME` is an environment variable that points to your user directory (e.g., `C:\Users\YourName`). `\$HOME\Desktop` changes directory to your Desktop.

2. **Clone the Repo:**
   Now run the **git clone** command to download the app’s repository from GitHub:

   ```bash
   git clone https://github.com/docker/getting-started-app.git
   ```

   This will create a folder named `getting-started-app` on your Desktop (or wherever you ran the command). It contains the to-do app’s code.

   If prompted by Git the first time (like accepting the host fingerprint), type “yes”. The download may take a few seconds.

3. **Explore the Files:**
   Change into the project directory and list the files:

   ```bash
   cd getting-started-app
   dir   # use 'ls' if you prefer
   ```

   You should see files like `package.json`, `README.md`, and directories like `src/` and `spec/`. Here’s a simplified layout:

   * **package.json** – Lists the app’s dependencies and scripts. It’s basically a manifest for the Node.js app.
   * **src/** – The source code of the app (JavaScript files for the server and front-end).
   * **spec/** – Some tests for the app (not needed for our purposes right now).
   * **yarn.lock** – A lockfile for the Yarn package manager (tracks exact versions of dependencies).

   There is **no Dockerfile yet** – we will create that next.

   *If you don’t have Git:* Instead of cloning, you can download the code as a ZIP. Go to the GitHub URL in a browser, download the ZIP of the repository, then extract it. Make sure the folder is named `getting-started-app` and navigate into it in PowerShell.

### 3.2 Write a Dockerfile

A **Dockerfile** is a text file with instructions on how to assemble our application into a Docker image. It’s like a recipe for Docker to bake an image.

1. **Create an empty Dockerfile:**
   In the `getting-started-app` directory, create a new file named **Dockerfile** (no extension). One quick way: run this in PowerShell:

   ```bash
   type NUL > Dockerfile
   ```

   This is a Windows trick to create an empty file. (On PowerShell, you could also do `New-Item Dockerfile`.) There will now be a file `Dockerfile` in the folder.

2. **Open the Dockerfile in an editor:**
   Use Notepad (or your preferred editor) to edit the Dockerfile:

   ```bash
   notepad Dockerfile
   ```

   If Notepad opens and asks to create the file (in case the above command didn’t create it), choose Yes. Then Notepad will show a blank file.

3. **Add Dockerfile content:** Copy and paste the following into the Dockerfile:

   ```Dockerfile
   # Use an official Node.js runtime as the base image
   FROM node:18-alpine

   # Set the working directory inside the container
   WORKDIR /app

   # Copy application files into the container
   COPY . . 

   # Install dependencies (using Yarn package manager)
   RUN yarn install --production

   # Specify the command to run when the container starts
   CMD ["node", "src/index.js"]

   # Document that the container listens on port 3000
   EXPOSE 3000
   ```

   Save the file (File > Save in Notepad). Let’s go through what each line means:

   * **FROM node:18-alpine:** Start from a lightweight official Node.js 18 image (based on Alpine Linux). This image has Node.js and npm/yarn ready to use.
   * **WORKDIR /app:** Set the working directory in the container to **/app**. All subsequent commands (COPY, RUN, etc.) will operate in this directory. It will also be the default working directory when the container runs.
   * **COPY . . :** Copy everything in our current directory (the app’s code on the host) into the current directory in the container (which is /app because of WORKDIR). This means all our app files get packaged into the image.
   * **RUN yarn install --production:** Run the Yarn package manager to install dependencies listed in package.json. The `--production` flag skips devDependencies, installing only what the app needs to run. This creates a `node_modules` folder inside the image with all the libraries.
   * **CMD \["node", "src/index.js"]:** This defines the default command when a container starts from our image. It will run `node src/index.js`, which starts the to-do app server.
   * **EXPOSE 3000:** This isn’t a command per se; it’s documentation. It signals that the container should listen on port 3000 (the app runs on port 3000 by default). This doesn’t by itself publish the port to your machine (we do that when running the container), but it’s a good reminder/metadata.

   Now our Dockerfile is ready. This tells Docker how to build an image for the to-do app.

4. **(Optional) Inspect package.json:** If you open `package.json` (e.g., with `notepad package.json`), you’ll see the app’s details:

   * It uses **Express** (a Node.js web framework) and **SQLite** (a file-based database) among other packages.
   * There’s a `"start"` script and a `"dev"` script. The **dev** script uses a tool called **nodemon** to auto-restart the server on code changes (we’ll use that in development mode later).
   * The default start command is likely `node src/index.js` (which our Dockerfile’s CMD runs).

   You don’t need to modify anything in package.json for now; just know that `yarn install` will install what's listed there.

### 3.3 Build the Docker Image

Now we’ll use the Dockerfile to build an image for our app.

1. **Make sure you’re in the app directory:** In PowerShell, ensure the current directory is `getting-started-app` (where your Dockerfile and package.json are). If not, `cd` into it.

2. **Run the build command:**

   ```bash
   docker build -t getting-started .
   ```

   Let’s explain this:

   * `docker build` – the command to build an image.
   * `-t getting-started` – this tags (names) the image as “getting-started”. You can choose any name, but we’ll use this for now. The name is useful later for running the container and pushing to Docker Hub.
   * `.` – the dot signifies the **build context** and Dockerfile location, which is the current directory. Docker will look for the Dockerfile here and include all files here (remember, we copy `.` in the Dockerfile, so everything in this folder will be sent into the image).

   **Expected output:** Docker will execute the Dockerfile step by step. The first time, you’ll see it download the base image `node:18-alpine` (this could be several MBs, so it may take a bit). Then it will execute the COPY (which is quick) and the Yarn install (which might take a little time as it downloads dependencies inside the image). You’ll see output for each step, something like:

   ```
   Sending build context to Docker daemon  ... (size)
   Step 1/6 : FROM node:18-alpine
    ---> [some image id]
   Step 2/6 : WORKDIR /app
    ---> Using cache
    ---> [id]
   Step 3/6 : COPY . .
    ---> [id]
   Step 4/6 : RUN yarn install --production
    ---> [id]
   Step 5/6 : CMD ["node", "src/index.js"]
    ---> [id]
   Step 6/6 : EXPOSE 3000
    ---> [id]
   Successfully built [image id]
   Successfully tagged getting-started:latest
   ```

   Key points:

   * The first time, “Using cache” won’t appear; it will actually do each step. (If you run the build again without changes, Docker might use cached layers for speed.)
   * After a successful build, Docker says it **tagged** the image as `getting-started:latest`. By default, if no tag is specified in `-t`, it uses “latest”.
   * You can run `docker images` now and you should see `getting-started` listed.

   Now we have a **Docker image** of our app. Think of the image as a snapshot of an environment containing Node.js + our app code + its dependencies, all ready to run.

   *If you encounter an error:* Double-check the Dockerfile content. Common issues are typos in the file names or commands. Also ensure you ran the command in the correct directory (the one with Dockerfile).

### 3.4 Run the Container

Time to run our app in a container! We’ll use `docker run` with a couple of options to map the network port so we can view the app.

1. **Start the container:** In PowerShell, run:

   ```bash
   docker run -dp 127.0.0.1:3000:3000 getting-started
   ```

   Breakdown of this command:

   * `docker run` – run a new container.
   * `-d` – run in **detached** mode (in the background). This means the container will keep running, and we get our terminal prompt back (instead of showing logs or blocking).
   * `-p 127.0.0.1:3000:3000` – **publish** port 3000 from the container to the host. This is how we can access the web app. Format is `host:container`, so here we map container’s port 3000 to the host’s address 127.0.0.1:3000. Using `127.0.0.1` means it’s only accessible from this machine (localhost). We could also write it as `-p 3000:3000` (which defaults to all interfaces, but Docker Desktop on Windows may require localhost; we’ll discuss `0.0.0.0` later for remote access).
   * `getting-started` – the name of the image to run (the one we just built).

   When you press Enter, Docker will start the container. Because we used `-d`, it will print the new container’s ID (a long hexadecimal string) and return to the prompt immediately. You can check that it’s running with:

   ```bash
   docker ps
   ```

   This should list the container with image `getting-started` and a name (Docker gives it a random fun name if you didn’t specify `--name`). It will also show the port mapping `0.0.0.0:3000->3000` (or `127.0.0.1:3000->3000`). This means your local port 3000 is forwarded to the container’s port 3000.

   **Fix Note: Container Crash Troubleshooting**

   *If your container starts and then exits immediately (for example, `docker ps` shows nothing, but `docker ps -a` shows it as “Exited”), here’s how to debug:*

   * **Check the logs:** Run `docker ps -a` to find the container ID with “Exited” status. Then use:

     ```bash
     docker logs <container-id>
     ```

     This displays the container’s output to identify why it crashed. Common issues include:

     * **“command not found”** or similar: This suggests a missing dependency (e.g., Yarn isn’t installed in the image, or a command in the Dockerfile’s CMD is incorrect). Verify the `CMD ["node", "src/index.js"]` and ensure `src/index.js` exists in the copied files.
     * **“Cannot find module”** or **“No such file or directory”**: This indicates missing files or dependencies. Check that `COPY . .` in the Dockerfile copied all necessary files, and that `yarn install --production` installed all required packages. You might need to remove `--production` to include dev dependencies if the app requires them.
     * **Stack trace or app-specific error**: This means the Node.js app itself crashed (e.g., a bug in `src/index.js` or a missing configuration). Check the error message for clues.
   * **Inspect the container:** To explore the container’s filesystem (even if exited), start a new container in interactive mode:

     ```bash
     docker run -it getting-started sh
     ```

     This opens a shell inside a temporary container. Run `ls /app` to verify files, or check `node --version` and `yarn --version` to ensure the environment is correct.
   * **Fix and rebuild:** Based on the logs or inspection, update the Dockerfile (e.g., fix paths, install missing tools like `yarn` via `RUN apk add yarn` for Alpine-based images, or remove `--production` from `yarn install`). Then rebuild:

     ```bash
     docker build -t getting-started .
     ```

     Remove the exited container:

     ```bash
     docker rm <container-id>
     ```

     And retry the run command. Repeat until the container stays running (`docker ps` shows it).
   * **Network issues:** If the container runs but you can’t access `localhost:3000`, ensure the port mapping is correct and no other process is using port 3000 on your host (check with `netstat -a -n -o` in PowerShell and stop conflicting processes).

2. **Open the app in your browser:**
   Launch your web browser (Chrome, Edge, etc.) and navigate to **[http://localhost:3000](http://localhost:3000)**. You should see the to-do app’s webpage load. It might be a simple page with a title, an input box, and initially a message that there are no to-do items.

   *Figure: The to-do list web app running in a container. It greets with "Hello world!" at the top, shows an input box for new items with an "Add Item" button, and displays a message "No items yet! Add one above!" when the list is empty.*

   Try using the app:

   * In the text box (maybe labeled “New Item”), type a to-do item (e.g., “Finish Docker lab”) and click **Add Item**.
   * The item should appear in the list below, with a checkbox. Add a couple more items to see the list grow.
   * Mark an item as complete by clicking its checkbox, or remove an item by clicking a delete button (if provided).
   * The app is interactive – it uses a backend API to store items in a database (more on that soon).

   Congratulations, you have a web application running inside a Docker container! 🎉 The Node.js server inside the container is listening on port 3000, and thanks to our `-p` option, your browser was able to talk to it via `localhost:3000`.

3. **Understanding what just happened:**
   When we ran the container, Docker:

   * Created a new container from our **getting-started** image.
   * Connected port 3000 of that container to port 3000 on your PC.
   * Started the container, which ran `node src/index.js` (from our Dockerfile’s CMD).
   * The Node app inside the container started up. This app uses **SQLite** to save data in a file (by default, it likely created a `todo.db` somewhere inside the container’s filesystem to store the tasks).
   * The app served the HTML/JS for the webpage (the front-end is probably a static site served by Express) and handled API calls (for adding/removing todos) via a backend.

   *Tip:* If you want to see the logs of the running container (what the app is printing to console), you can use:

   ```bash
   docker logs -f <container-id>
   ```

   Replace `<container-id>` with the ID or name from `docker ps`. The `-f` means “follow” (like tailing the log). You can stop viewing logs by pressing Ctrl+C. This can be useful to debug if something went wrong.

4. **Keep the container running:**
   Leave the container running for now as we continue. If you close the PowerShell window, the container will keep running in the background. We’ll explicitly stop/remove containers when needed.

**Summary (so far):** You cloned a Node.js app, wrote a Dockerfile for it, built a Docker image, and ran it as a container. You accessed the app via browser and saw that it works. We essentially **containerized** a web application. Great job! Next, we will make a small change to the app and see how to update our container.

## Step 4: Updating the Application and Container

Real-world apps change over time – you fix bugs or add features. Let’s simulate that by changing a piece of text in the app. We’ll then rebuild the image and run a new container. This will teach us how to update a running app in Docker and how to deal with containers that were already running.

### 4.1 Change the App’s Text

As a simple update, we’ll change the message that appears when the to-do list is empty. Currently it says “No items yet! Add one above!” We’ll make it a bit more explanatory.

1. **Open the source file for editing:**
   The text is part of the front-end. In this app, the front-end HTML is generated by a JavaScript file. Let’s open the file that likely contains this message. According to the app structure, it’s in `src/static/js/app.js`. Open it with Notepad:

   ```bash
   notepad src\static\js\app.js
   ```

   This file is a bit large, but we’re interested in finding “No items yet! Add one above!”. Scroll or search (Ctrl+F in Notepad) for that phrase. You should find a line like:

   ```jsx
   <p className="text-center">No items yet! Add one above!</p>
   ```

   (It might be around line 50-60 of the file.)

2. **Edit the text:**
   Change that line to:

   ```jsx
   <p className="text-center">You have no todo items yet! Add one above!</p>
   ```

   In other words, we’re adding “You have no todo items yet!” to the message. It’s a minor change, but noticeable.

   Save the file in Notepad (File > Save). Close Notepad.

3. **(Optional) Verify locally:** This change only affects the source code. If you refresh your browser *right now*, you **won’t** see the change because the container currently running is using the old code (the one baked into the image). We need to rebuild the image and start a new container for changes to take effect in the app.

### 4.2 Rebuild the Image

We’ll rebuild our Docker image to include the updated code.

1. **Build again:**
   In the same directory (`getting-started-app`), run the same build command:

   ```bash
   docker build -t getting-started .
   ```

   Docker will again go through the Dockerfile. This time, it might use cache for earlier steps (like not needing to re-download Node 18). However, since we changed a file that is copied into the image, Docker will notice that during the `COPY . .` step and re-run the steps after that. The Yarn install might run again (though if dependencies haven’t changed, it might also be cached depending on how Docker handles it – but let’s assume it runs quickly).

   Once done, you’ll have a new image tagged `getting-started:latest` (it replaced the previous one with the same name). Essentially, we built a **new version** of our app image with the updated text.

   *Aside:* If you wanted to version your images, you could tag them like `getting-started:v2`, etc. But for simplicity, we’re reusing the same tag.

2. **Confirm image updated:**
   You can run `docker images` – it will show `getting-started` with an updated CREATED time just now. The old image is likely overwritten (actually, the old one still exists until we delete it or it gets untagged, but that’s an advanced topic).

### 4.3 Run the Updated Container

Now, let’s run a new container using the updated image.

1. **Run the new container:**
   Use the same run command as before:

   ```bash
   docker run -dp 127.0.0.1:3000:3000 getting-started
   ```

   Uh oh – this time you might *not* get a new container ID instantly. You might see an error like:

   ```
   docker: Error response from daemon: driver failed programming external connectivity on endpoint <container-name> (...): Bind for 127.0.0.1:3000 failed: port is already allocated.
   ```

   This error happened because you still have the **old container running** from before, which is using port 3000 on your host. You can’t have two different containers (or any two processes) listening on the same port 3000 on the same address. Docker tried to map 127.0.0.1:3000 for the new container, but it’s already taken.

2. **List running containers:**
   Run `docker ps`. You’ll see your first `getting-started` container still up. We need to stop/remove it to free the port.

3. **Stop and remove the old container:**
   We’ll use Docker commands to stop and remove the previous container.

   * First, get the **Container ID** or name from `docker ps` for the old container. Let’s say the ID is `abc1234567` (it will be something else).
   * Stop the container:

     ```bash
     docker stop abc1234567
     ```

     This will gracefully shut down the Node app in that container. (If it doesn’t respond in a few seconds, Docker will force kill it.)
   * Remove the container:

     ```bash
     docker rm abc1234567
     ```

     Once a container is stopped, removing it deletes it from Docker’s list of containers. We do this because we don’t need the old container anymore and to free up resources.

   *Shortcut:* You could combine stop & remove in one step with `docker rm -f <ID>` (the `-f` forces removal, which stops it if running). For example:

   ```bash
   docker rm -f abc1234567
   ```

   This is a handy command to quickly nuke a container.

4. **Run the container again:**
   Now try running the updated container again:

   ```bash
   docker run -dp 127.0.0.1:3000:3000 getting-started
   ```

   This time it should start successfully (you’ll get a container ID output). `docker ps` should show one `getting-started` container running (the new one).

5. **Test the update:**
   Go back to your browser ([http://localhost:3000](http://localhost:3000)). If you still have it open from before, hit refresh. You should see the updated message when no items are present:
   **“You have no todo items yet! Add one above!”** (with our new phrasing).

   This confirms that the new container is running the updated code. The rest of the app should function as before (your previously added items are gone because this is a fresh instance – we’ll address that persistent data issue soon).

   If you see the old message still, double-check that you refreshed and that only one container is running. Maybe the browser loaded a cached page; a hard refresh (Ctrl+F5) can help. If it’s still not updating, ensure you actually edited the correct file and rebuilt the image.

6. **Cleanup tip:** At this point, if everything is working, you don’t need any stopped containers sitting around. You can run `docker ps -a` to show all containers (including exited ones). If you see the old container in an “Exited” state (because we stopped it), you can remove it if not already removed. It’s good to keep your environment tidy by removing containers you no longer need.

**Summary:** You successfully updated the application’s code and saw how to deploy that change via Docker. We encountered a common scenario – a port conflict – and learned that only one container can use a specific host port at a time. We resolved it by stopping/removing the old one. Now you know how to rebuild images for app updates and manage multiple containers.

## Step 5: Sharing Your Application Image (Docker Hub)

Up to now, everything has been on your local machine. But one of Docker’s superpowers is the ability to share your container images easily. If you want to run this app on another computer, you could repeat the build process there – but it’s much easier to push your image to **Docker Hub** (a cloud registry) and then simply run it on another machine by pulling from the registry.

Imagine you want to deploy this app to a server or give it to a friend. In this step, we’ll **push** our image to Docker Hub and simulate running it on a different machine (using an online sandbox called Play with Docker).

### 5.1 Prepare Docker Hub Repository

1. **Log into Docker Hub:**
   Make sure you have a Docker Hub account (username). In PowerShell, log in with your credentials:

   ```bash
   docker login -u goodluckoguzie
   ```

   It will prompt you for your Docker Hub password. (Note: Characters won’t show as you type, for security, but hit Enter when done.) If successful, it will say “Login Succeeded”.

   *If you don’t have an account:* Go to [hub.docker.com](https://hub.docker.com/) in your browser, sign up for a free account. Choose a Docker ID (username) – we’ll use `goodluckoguzie` as specified. After verifying email, etc., come back and do the login step above.

2. **Create a Repository on Docker Hub:**
   On Docker Hub’s website, after logging in, click the **“Create Repository”** button (usually on your dashboard or under the Repositories tab).

   * Name the repository **getting-started** (to match our image name, though you could choose another name if you want).
   * Keep it **Public** for now (so others or the sandbox can pull it without authentication).
   * Add a description if you like, then click **Create**.

   You should now have a Docker Hub repository like `goodluckoguzie/getting-started`. Docker Hub will even show you commands to push an image there.

### 5.2 Push the Image to Docker Hub

Currently, our image is named `getting-started` locally. To push it to our Docker Hub repo, we should tag it with our Docker Hub username. Docker images are named in the format `[username]/[repository:tag]` for pushes.

1. **Tag the image:**
   Use the `docker tag` command to give our image a new name that includes your Docker ID. For example, with the Docker ID `goodluckoguzie`:

   ```bash
   docker tag getting-started goodluckoguzie/getting-started:latest
   ```

   This does *not* make a copy of the image; it’s just adding another label to the same image ID. Now if you run `docker images`, you’ll see `goodluckoguzie/getting-started` listed (alongside the original `getting-started` tag). By default, we add the `:latest` tag.

2. **Push the image:**
   Now push the tagged image to Docker Hub:

   ```bash
   docker push goodluckoguzie/getting-started:latest
   ```

   The `:latest` tag is optional to specify, since it’s the default.

   You should see Docker uploading layers. It will push all layers that are not already in the Hub. The base layer (node:18-alpine) might already exist on Docker Hub’s side, so it doesn’t need to be uploaded from scratch (Docker uses layer caching on pushes too). The final layers which include our app will be uploaded. Once complete, you’ll get a success message.

   On the Docker Hub website, refresh the repository page – you should see your image there, tagged “latest”. Congrats, your containerized app is now published!

   *Common pitfall:* If you forget to tag with your username and try `docker push getting-started`, it will fail (because “library/getting-started” is not your repository). Always tag with your Docker ID for personal images. If you see an error like “denied: requested access to the resource is denied” or “authentication required,” it means you’re either not logged in or you tagged the image under a name you don’t have access to. Make sure you ran `docker login` and that the image tag includes your exact Docker Hub username (for example, don’t tag it as `docker/getting-started`, since “docker” is the official Docker account, not yours).

### 5.3 Run the Image on a New Instance

Let’s pretend we have a brand new machine that’s never seen our code. Maybe it’s a Linux server, or a colleague’s computer, or a cloud service. We want to run the app there. We don’t need the source code or to rebuild – we can just use Docker to pull and run the image we pushed.

We’ll simulate this using **Play with Docker (PWD)**, which is an online sandbox provided by Docker. If you prefer, you could also just imagine running this on another computer with Docker.

1. **Open Play with Docker:**
   Go to [labs.play-with-docker.com](https://labs.play-with-docker.com/) in your browser. Click “Login” and choose Docker (sign in with your Docker Hub account). Once in, click “Start” to begin a session. Click the “Add New Instance” button to get a temporary Docker terminal in your browser (it’s like a fresh Linux VM with Docker).

   You’ll see a terminal interface. It’s already logged in (no need for password). This instance has Docker but no images except hello-world perhaps.

2. **Pull and run your image:**
   In the PWD terminal, run:

   ```bash
   docker run -dp 0.0.0.0:3000:3000 goodluckoguzie/getting-started:latest
   ```

   Notice we used `0.0.0.0:3000:3000` instead of `127.0.0.1`. This is because on Play with Docker, to access the port from your local browser, the container needs to listen on all network interfaces (0.0.0.0). On your own PC, 127.0.0.1 was fine because you were directly on that machine. In a cloud or VM environment, 0.0.0.0 makes the port accessible externally (with proper routing).

   You should see Docker pulling the image layers from Hub (it will download the layers we pushed). Then it runs the container.

3. **Access the running app:**
   After the container starts, PWD usually shows a little badge or link for the forwarded port. Look for a **“3000”** badge or an “Open Port” option. Click it, or manually open a new browser tab and go to the URL provided by PWD (often it’s something like `http://ip<something>.ip.linodeusercontent.com:30000` or so – PWD will give you the exact URL).

   The to-do app should appear, running from that sandbox instance! It’s the same app, served from the container we pushed.

   Try adding an item to confirm it works. It should, since it’s the same image.

   Pretty cool – you were able to run your app without moving any code manually, just using the Docker image from the cloud. This is how deploying with Docker often works.

   *Note on 0.0.0.0 vs 127.0.0.1:* By using `0.0.0.0:3000:3000`, we told Docker to publish the container’s port on all network interfaces of the host. On PWD (or any server), that means others can reach it (if permitted). When we used `127.0.0.1` on our local PC, it was restricted to our machine only (which was fine for local dev). So remember:

   * Use `127.0.0.1` (localhost) binding if you want the service only accessible on that machine.
   * Use `0.0.0.0` if you need to expose it to other machines.

4. **Stop the sandbox container (when done):**
   In the PWD terminal, you can stop the container with `Ctrl+C` if you ran it without `-d`. But since we used `-d`, you’d do `docker ps` to get the ID and then `docker stop <ID>`. The sandbox will anyway vanish after some time. Close the PWD tab when finished.

You have now shared your Dockerized app and run it on a completely different environment with minimal effort. This demonstrates Docker’s power in consistent deployments.

**Summary:** We pushed our Docker image to a registry (Docker Hub) and pulled it on a fresh environment to run the app. In practice, this is how you’d deploy containers to servers or Kubernetes clusters: build an image, push to registry, then instruct the server/cluster to run that image. You also learned about port binding differences for local vs external access. Great work!

## Step 6: Persisting To-Do List Data with Volumes

If you’ve been paying attention, you might have noticed something: every time we ran the container fresh, our to-do list started empty. Add items, stop/remove the container, run a new one – the list is empty again. Why? Because by default, **containers don’t keep data between runs.** Each container has its own filesystem that exists only as long as that container is alive. When we removed the container, its filesystem (including the SQLite database file where todos are saved) was discarded.

In this step, we’ll explore how container storage works and use a Docker **volume** to make the to-do data persist even if the container is destroyed or replaced.

### 6.1 Understanding Container Filesystems (Ephemeral Storage)

Every Docker image has layers with the files it contains. When you run a container from an image, Docker mounts those image layers as a read-only base, and then adds a **writable layer** (often called the “container layer” or scratch space) on top. Any file creations, modifications, or deletions you do in the container at runtime affect this container-specific layer.

Key point: **Changes inside a container do not affect the original image, and each container’s changes are isolated.** If you start another container from the same image, it doesn’t see the files created by other containers.

#### Demonstration: Isolated Container Storage

Let’s do a quick experiment to prove this isolation:

1. **Create a file in one container:**
   Run an Ubuntu container that creates a file, then keeps running:

   ```bash
   docker run -d ubuntu bash -c "echo Hello > /data.txt && tail -f /dev/null"
   ```

   What this does:

   * `ubuntu` is the image (if not present, Docker will pull it).
   * The command `bash -c "echo Hello > /data.txt && tail -f /dev/null"` does two things:

     * `echo Hello > /data.txt` writes “Hello” to a file `/data.txt` inside the container.
     * `&& tail -f /dev/null` is just a trick to keep the container running indefinitely (tailing /dev/null does nothing forever). We run in detached mode (`-d`), so container stays alive.

   Now we have a running Ubuntu container (in background) with a file `/data.txt` containing the word “Hello”.

2. **Check the file’s content:**
   We can read the file by executing a command in the running container:

   * First, get the container’s ID with `docker ps` (it’s the one with image `ubuntu`).
   * Then run:

     ```bash
     docker exec <container-id> cat /data.txt
     ```

     This will print the content of `/data.txt` from inside that container. It should output “Hello”. Great.

3. **Start another container and look for the file:**
   Now run a second, separate Ubuntu container and list files:

   ```bash
   docker run --rm ubuntu ls /
   ```

   Here:

   * We didn’t use `-d`, so it will run, list the root directory, then exit.
   * `--rm` just means automatically remove this container after it exits (to keep things clean).
   * The output of `ls /` will show a bunch of standard directories (`bin`, `etc`, `home`, `root`, etc.), but **you will not see `data.txt`**. That file only existed in the first container’s writable layer, not in the base image (Ubuntu doesn’t have it by default) and not shared with others.

   This confirms that one container’s changes are invisible to another when using the same image.

4. **Clean up the first container:**
   We don’t need that first Ubuntu container anymore. You can remove it:

   ```bash
   docker rm -f <container-id>
   ```

   (This stops and removes it.)

So, each container is isolated – which is usually good (no unintended interference). But for things like databases, we **do** want data to persist beyond the container life. This is where **volumes** come in.

### 6.2 Introducing Volumes for Persistent Storage

A **Docker volume** is a mechanism for storing data outside the container’s writable layer. Think of it as plugging an external drive into the container. The data on that drive isn’t destroyed when the container stops, and can be plugged into another container later.

There are two main types of volumes:

* **Named volumes:** Docker manages where these are on disk. You just give them a name, and Docker stores the data in its internal data directory (on Windows, inside the Docker Desktop VM or WSL2 backend).
* **Bind mounts:** You specify an exact path on the host filesystem to share with the container (we’ll cover this in the next step for development use-cases).

For our database, a named volume is a great choice:

* We don’t care exactly where the data is stored on the host, we just want it to persist.
* Named volumes are easy and portable; Docker will handle them across environments.

**Where does our app store data?** In this to-do app, it uses SQLite which stores data in a single file. By checking the app code or documentation, it turns out the database file is at **`/etc/todos/todo.db`** inside the container. (We didn’t explicitly set that, it’s built into the app.) That means if we mount a volume at `/etc/todos`, the `todo.db` file stored there will actually live in the volume, not just in the container’s ephemeral layer.

### 6.3 Create a Volume and Run the Container with it

Let’s create a volume and use it for our to-do app’s data directory.

1. **Create a named volume:**
   You can create a volume with a simple command (though Docker will also create it automatically if it doesn’t exist when you run a container). Let’s be explicit:

   ```bash
   docker volume create todo-db
   ```

   This makes a volume named **todo-db**. You can list volumes with `docker volume ls` (you’ll see it listed, possibly with local driver).

2. **Remove any running to-do container:**
   If our app’s container is still running from the previous steps, we should remove it so we can start a new one with the volume. Do:

   ```bash
   docker ps        # get the container ID for the getting-started app
   docker rm -f <container-id>
   ```

   This stops and deletes the current to-do app container. (We’re about to start another with persistent storage.)

3. **Run the app with the volume attached:**
   Use `docker run` with a volume mount:

   ```bash
   docker run -dp 127.0.0.1:3000:3000 \
     --mount type=volume,src=todo-db,target=/etc/todos \
     getting-started
   ```

   This is a longer command, broken into multiple lines here for clarity (the `\` allows line break in Bash/PowerShell). It’s actually one command. Let’s explain:

   * `--mount type=volume` – we are using a named volume.
   * `src=todo-db` – the source volume name is `todo-db` (the one we created). If it didn’t exist, Docker would create it.
   * `target=/etc/todos` – inside the container, we mount the volume at the path `/etc/todos`. This is where our app writes the database file.
   * Everything else (the `-d -p ... getting-started`) is same as before.

   When this container starts, here’s what happens:

   * Docker checks if volume `todo-db` is empty (it is, since we just made it). By default, when mounting a **named volume** to a path that has content in the image, Docker will **copy the existing content** from the image into the volume (this is a handy feature so you don’t lose default files). The image might have a default empty `todo.db` or some initial files in `/etc/todos`. If so, those are now in the volume. (If the path was empty in image, then volume is just empty.)
   * The app starts and if it creates/updates `todo.db`, it’s writing to the volume, which stores it on the host.

   You won’t see anything different yet, but we’ll test persistence next.

4. **Use the app (add some items):**
   Go to [http://localhost:3000](http://localhost:3000) once again. The list will be empty (fresh DB). Add a couple of items like “Test volume persistence”. Mark one as done, etc., just to have some data.

5. **Stop and remove the container:**
   Now simulate an upgrade or restart: remove this container.

   ```bash
   docker rm -f <new-container-id>
   ```

   (Get the ID via `docker ps` if you need, or since we know only one container was running, you can do it directly.)

6. **Run a new container with the same volume:**
   Use the same run command from step 3 to start the app again with `--mount type=volume,src=todo-db,target=/etc/todos`. (The volume `todo-db` still exists with the data in it.)

   When this new container starts, it uses the same volume, which already has `todo.db` file with our previous items.

7. **Check the app data:**
   Refresh the web app in the browser. **Your to-do items should still be there!** 🎉 Even though we completely removed the container (which normally would lose all data), the tasks persisted because they were stored in the `todo-db` volume on the host.

   You’ve successfully persisted the app’s database beyond the life of a container.

A few notes on volumes:

* You can inspect a volume’s details with `docker volume inspect todo-db`. This will show (among other info) the “Mountpoint” – the location on the host where Docker is storing the data. On Linux, it might be under `/var/lib/docker/volumes/todo-db/_data`. On Docker Desktop (Windows/Mac), it’s inside the Docker VM. Either way, you typically don’t need to access it directly; Docker manages it.
* The data in `todo-db` will remain until you remove the volume with `docker volume rm todo-db`. Even if no container is using it, it stays around (in case you need it later). So if you have persistent data you no longer need, remember to remove the volume to free space.
* If you re-run `docker run ... --mount src=todo-db ... getting-started` in the future, it will reuse the same data. If you *wanted* a clean slate, you’d use a different volume or delete the volume.

**Summary:** By using a named volume, we have made the to-do list’s data persistent. The app’s SQLite database file is now stored in a stable location on the host, separate from containers. Containers can come and go, and each new container can pick up the existing data by mounting the same volume. This is how you avoid “losing data” when containers shut down.

Now that we’ve got persistence for our database, let’s look at another volume type (bind mounts) which are super useful for development.

## Step 7: Using Bind Mounts for Development (Live Code Editing)

While named volumes are great for databases and persistent data, **bind mounts** are very useful during development. A bind mount lets a container directly use a folder from your host machine. This means if you change files on your host (like editing source code), the container can instantly see those changes. This is perfect for a development workflow where you want to edit code and have the app reload without rebuilding a new image each time.

In this step, we’ll set up a bind mount so we can run our Node.js app in a container but edit the files on our Windows host with immediate effect. We’ll use a tool called **nodemon** (already included in the project’s dev dependencies) to watch for file changes and restart the Node app inside the container automatically.

### 7.1 Volume vs. Bind Mount Quick Comparison

Before diving in, let’s clarify:

* A **Named Volume** is managed by Docker, path decided by Docker, good for production data persistence. It’s isolated from your host’s regular files (unless you inspect Docker’s internal data).
* A **Bind Mount** uses an explicit host folder. For example, you can bind mount `C:\Users\Alice\projects\getting-started-app` to `/app` in a container. Any changes in that folder on Windows are reflected in the container, and vice versa.
* In a bind mount, if the host directory has files, they show up in the container (and will override any files that might be in the image at that location).
* Bind mounts are great for development because you can use your normal editor/IDE on the host, and run the code in a Linux container with all dependencies, and see changes live.

Now, let’s do an experiment to see how bind mounts reflect changes instantly.

### 7.2 Try a Bind Mount (Experiment)

We’ll mount our app folder into a simple Ubuntu container to play with creating and deleting files.

1. **Ensure your app directory is accessible to Docker:**
   Docker Desktop on Windows has settings for file sharing. By default, it allows bind mounting locations under your user directory (like Desktop, Documents, etc.). Our `getting-started-app` is on Desktop, so it should be fine. (If Docker couldn’t access it, we’d adjust settings, but by default it’s okay.)

2. **Run an interactive container with a bind mount:**
   In PowerShell, from the `getting-started-app` directory (this is important, as we’ll use the current path), run:

   ```bash
   docker run -it --mount type=bind,src="${PWD}",target=/src ubuntu bash
   ```

   Explanation:

   * `-it` – interactive mode with a terminal (so we get a shell inside the container).
   * `--mount type=bind` – specifying a bind mount.
   * `src="${PWD}"` – this uses the PowerShell variable `${PWD}` which is the current directory path (e.g., `C:\Users\Alice\Desktop\getting-started-app`). We put it in quotes because it might contain spaces. This is the host directory we want to share.
   * `target=/src` – inside the container, we mount it at `/src`.
   * `ubuntu` – use the Ubuntu image.
   * `bash` – the command to run, so we get a Bash shell in the container.

   After running this, your terminal prompt should change to something like `root@<container-id>:/#`. You are now “inside” the Ubuntu container, with `/src` pointing to your host files.

3. **Look around inside container:**
   Run:

   ```bash
   ls /
   ```

   You’ll see the usual directories plus `src` (because we mounted it). Now run:

   ```bash
   cd /src
   ls
   ```

   You should see the files from your app directory: Dockerfile, package.json, src/, spec/, etc. The container is seeing the exact files from Windows through this mount.

4. **Create a new file from inside the container:**
   Still in the container shell, run:

   ```bash
   touch myfile.txt
   ls
   ```

   The `touch` command creates an empty file named `myfile.txt`. The `ls` should now list it among the files in /src.

5. **Verify on the host:**
   Open your Windows File Explorer and navigate to your `getting-started-app` folder (Desktop > getting-started-app). You should see `myfile.txt` appear there! The container writing to /src created the file on your host.

6. **Delete the file from host:**
   On Windows, delete the `myfile.txt` file (either via Explorer or you could even do it in a separate PowerShell with `rm myfile.txt` in that directory).

7. **Check in container again:**
   Back in the container shell, run:

   ```bash
   ls
   ```

   `myfile.txt` is gone from /src inside the container as well. The deletion on the host was immediately reflected in the container’s view of that folder.

8. **Exit the container:**
   Type `exit` or press `Ctrl+D` in the container shell to leave. The container will stop and be removed (since we didn’t specify `--rm`, it will exist in “exited” state, but no big deal; you can `docker rm` it later if you care).

This shows how changes on one side (host or container) sync to the other through a bind mount. This is exactly what we want for development: we’ll run the app in a container, but edit the code on our machine.

### 7.3 Running the App in Development Mode with Bind Mount

Now, instead of the Ubuntu container, we’ll use the Node 18 image to run our app. We’ll mount our code into it and use the dev script (with nodemon) to auto-reload on changes.

1. **Stop any running “getting-started” container:**
   If the app is still running from earlier steps (with volume), stop and remove it. We want to free port 3000 and not confuse which container is which:

   ```bash
   docker ps   # find the ID for getting-started container
   docker rm -f <id>
   ```

2. **Run the dev container:**
   In the `getting-started-app` directory on PowerShell, execute:

   ```bash
   docker run -dp 127.0.0.1:3000:3000 ^
     -w /app --mount type=bind,src="${PWD}",target=/app ^
     node:18-alpine ^
     sh -c "yarn install && yarn run dev"
   ```

   (Here I used Windows PowerShell line continuation `^` for readability; you can write it in one line too.)

   Let’s parse this:

   * `-w /app` – sets the working directory inside the container to `/app`. We’ll mount our code at /app, so this is convenient.
   * `--mount type=bind,src="${PWD}",target=/app` – bind mount the current host directory into `/app` in the container.
   * `node:18-alpine` – using the official Node 18 image. This image has Node and yarn (hopefully yarn; if not, Node’s npm can install yarn globally, but Node:18-alpine might actually have npm only. However, our project uses yarn.lock. If yarn isn’t present, the `yarn` command might fail. We could use `npm install` instead. Alternatively, use `node:18-alpine` and add `&& npm install -g yarn && yarn install && yarn run dev`. To keep it simple, let’s assume yarn is there – if not, we’ll adjust. Node’s official images often do include yarn now.)
   * `sh -c "yarn install && yarn run dev"` – this is the command to run in the container:

     * `yarn install` will install all dependencies (including devDependencies like nodemon) into `node_modules`. **Importantly, because we mounted the whole project, the `node_modules` folder will be created on the host as well** (in your project folder). It might take a bit to download packages the first time.
     * `yarn run dev` runs the “dev” script from package.json, which typically starts nodemon to watch files and start the server.

   After running this, the container will start up in detached mode. Use `docker ps` to confirm it’s running. The name might be random or something like “serene\_goldberg” etc. The image is `node:18-alpine`.

3. **Check logs to see if it started successfully:**
   To ensure everything installed and the server is running, you can check the logs:

   ```bash
   docker logs -f <container-id>
   ```

   Look for output indicating that the app started. Likely you’ll see something like:

   ```
   nodemon 2.0.xx
   [nodemon] starting `node src/index.js`
   Using sqlite database at /etc/todos/todo.db
   Listening on port 3000
   ```

   If you see an error about `yarn: not found`, it means the Node base image didn’t have yarn. In that case, we can quickly fix by installing via npm:

   * Stop the container (`docker rm -f <id>`).
   * Run again with command: `sh -c "npm install -g yarn && yarn install && yarn run dev"`. This will first install yarn inside container, then proceed. (It might add a minute but fine.)
   * Then check logs again.

   Once you see “Listening on port 3000”, the server is up. (Press Ctrl+C to stop following logs.)

4. **Open the app in browser:**
   Go to [http://localhost:3000](http://localhost:3000) again. The app should load. It might be empty or if the bind mount is reusing the `todo.db` from earlier volume? Actually, note: we did not mount the volume this time, we mounted the code. The SQLite database file is part of the `getting-started-app` directory? Actually, originally in our image we put it in /etc/todos (which wasn’t in our project folder). In this dev mode, the code likely uses the default path `/etc/todos/todo.db`. But we didn’t mount /etc/todos; we only mounted our source code into /app. So where is it writing the DB now?

   Possibly, since we didn’t provide the volume, it writes to container’s /etc/todos which is ephemeral. It might create that path automatically. Our bind mount only covers /app (where code is). The `todo.db` is created at /etc/todos inside container. We didn’t bind that, so it’s ephemeral for dev. That’s okay for now since dev mode we can re-add tasks. If we wanted, we could also mount a volume or a bind for /etc/todos, but let’s not complicate.

   The important part is code editing, not data persistence in dev (though could mount a volume for data too if needed).

   Try adding an item to confirm the app works in this dev container. It should.

5. **Edit code and see live reload:**
   Now comes the fun part. Let’s make a change and watch it update without rebuilding:

   * Open the `src/static/js/app.js` file on your host (you can use Notepad or any editor you like, or VS Code for a better experience).
   * Find the section where the “Add Item” button text is set. In the React-ish code, it might be something like:

     ```jsx
     <button ...>{submitting ? 'Adding...' : 'Add Item'}</button>
     ```

     Let’s change **“Add Item”** to just **“Add”**.
   * Save the file.

   Because of the bind mount, as soon as you save, the container’s copy of that file is updated. **Nodemon**, running in the container, should detect the file change and automatically restart the Node server. Check the container logs (`docker logs <id>` or the PowerShell window if you had logs streaming) – you might see it saying it restarted.

   Now, refresh the web app in the browser. The **Add Item** button should now just say **Add**. The change took effect immediately, without rebuilding a Docker image or restarting the container manually!

   This is exactly how you can develop your app inside a Docker container environment but edit code on your machine. The container running Node+nodemon gives quick feedback loop, and you don’t have to install Node or dependencies on your host – the container has everything isolated.

6. **Try more changes (optional):**
   Feel free to make other small changes to the frontend text or style to see it update. Each time you save a file, nodemon will restart the server inside the container and you can refresh the browser to see the change.

7. **Stopping the dev container:**
   When you’re done, you can stop the container:

   ```bash
   docker rm -f <container-id>
   ```

   Or if you plan to keep developing later, leave it running. Remember, if you reboot or quit Docker, you might need to re-run it (and possibly re-run yarn install if `node_modules` got cleared, but since we mounted the whole project, `node_modules` are now a folder on your host too – you might notice it appeared in your project directory after the dev container ran, which is fine).

8. **Optional – Rebuild image with changes:**
   If the changes you made are permanent improvements, you’d eventually bake them into a new image. For instance, we changed the button text to “Add”. To get that into the final image, we’d rebuild: `docker build -t getting-started .` (ensuring Dockerfile COPY picks up changes) and possibly push to Hub again. But that’s beyond this dev workflow demonstration.

**Summary:** Using a bind mount and a development mode server, we were able to edit the application in real time. This method allows a rapid development cycle: you write code on your machine, and Docker ensures the app (inside the container) reflects those changes instantly. We didn’t have to rebuild the image for each tweak. This approach is commonly used when developing Dockerized apps – you develop with bind mounts for speed, and when ready for production, you build a fresh image (which would use the updated files).

## Step 8: Summary and Next Steps

You’ve completed Week 7! 🎊 This was a big journey, covering Docker basics through more advanced concepts. Let’s recap what you accomplished and learned:

* **Docker Setup & Hello World:** You set up Docker on Windows and ran the `hello-world` container to verify everything worked. You learned that Docker uses images from Docker Hub, and `docker run` will pull an image if you don’t have it.
* **Containerizing an App:** You took a Node.js to-do list app and wrote a **Dockerfile** to containerize it. By building an image with `docker build`, you packaged the app and its dependencies into a portable image.
* **Running Containers & Ports:** Using `docker run -d -p`, you launched the app in a container and mapped its port so you could access the web interface via `localhost:3000`. You saw your app running in Docker just as it would on a normal server.
* **Updating Containers:** When you changed the app’s code (updating text in the interface), you rebuilt the image and ran a new container. Docker taught you that you can’t have two containers using the same port – you resolved this by stopping/removing the old one. You learned to use `docker stop`/`rm` and the convenience of `docker rm -f` for quick removal.
* **Sharing Images (Docker Hub):** You created a Docker Hub repository, tagged your image with your username, and pushed it to the cloud. Then you simulated running it on another machine (using Play with Docker). This showed that with Docker, you can share and deploy apps easily – the environment setup is all in the image.
* **Volumes for Data Persistence:** You discovered that container files are ephemeral by default. Using a **named volume**, you gave the to-do app a persistent storage for its SQLite database. After that, the to-do list data survived container restarts – no more losing all tasks on each run.
* **Bind Mounts for Development:** Finally, you set up a bind mount to sync code between your host and a container, and used nodemon to auto-reload the app on changes. This allowed you to develop the app inside Docker (so it runs in a consistent Linux environment) while editing with your familiar tools on Windows, with instant feedback.

By mastering these steps, you’ve learned many core Docker concepts: **images, containers, ports, volumes, bind mounts, and the use of Docker Hub.** You used Docker both for deployment and for development scenarios.

**Next Steps:** With these fundamentals, you can explore more:

* Try writing Dockerfiles for other simple apps or languages (maybe a Python app or a static website).
* Look into Docker Compose, a tool for defining multi-container applications (for instance, separating the front-end, back-end, and database into different containers).
* In later weeks, you might orchestrate containers or use cloud services to deploy them. But the knowledge you’ve gained here will apply everywhere containers are used.

Great job on completing the practical! Now you have a solid foundation in Docker – an essential tool in modern software development and deployment. Happy containerizing! 🐳
