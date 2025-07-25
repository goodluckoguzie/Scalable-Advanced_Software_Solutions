# 🐳 Docker & Containers – Complete Beginner’s Guide


## Introduction

Hello! Welcome to this beginner’s guide to Docker and containers. We’ll create two simple apps: one that prints “Hello from Docker!” in the Command Prompt and another that shows a webpage in your browser. We’ll also pull ready-made containers from Docker Hub, including a “hello-world” app and an Ubuntu environment to explore.You’ll use the Windows Command Prompt and Visual Studio Code (VS Code, preferred) or Notepad, like opening a notebook to write instructions. By the end, you’ll have two working Docker apps, know how to use pre-built containers, and understand how Docker makes apps work anywhere. Let’s dive in!

### What You’ll Learn

- **What is an App**: A program, like a game or website, that needs tools to run.
- **What is a Container**: A box that holds your app and all its tools, so it works everywhere.
- **What is Docker**: A tool to create and run containers, like a toy box maker.
- **Dockerfile and Images**: Instructions (like a recipe) and blueprints for your containers.
- **Docker Hub**: A place to find ready-made container blueprints.
- **How to Build and Run Apps**: Use Docker to pack and run apps, like setting up a game anywhere.

### What You’ll Do

1. **Exercise A1**: Pull and run a ready-made “hello-world” container from Docker Hub.
2. **Exercise A2**: Create a simple app that prints “Hello from Docker!” in the Command Prompt.
3. **Exercise B**: Build a webpage that says “Hello from Docker Web App!” in your browser.
4. **Exercise C**: Run an Ubuntu container to explore a Linux environment interactively.
5. **Save Your Work**: Keep your project organized in a folder and optionally save it to GitHub.

### Prerequisites

- You have Docker Desktop installed (download from [https://www.docker.com/products/docker-desktop/](https://www.docker.com/products/docker-desktop/) if not).
- You’re comfortable typing in the Windows Command Prompt.
- You have Visual Studio Code installed (preferred):
  - Download from [https://code.visualstudio.com/](https://code.visualstudio.com/).
  - During installation, check **Add to PATH** to enable the `code` command in Command Prompt (e.g., `code .` to open VS Code).
- Alternatively, use Notepad (comes with Windows) to write code.
- No coding experience needed—we’ll explain every step!

## Step 1: Understanding the Basics

Before we start, let’s learn what we’re working with, like reading the rules of a new board game.

### What is an App?

An app is any program you use, like a calculator on your phone, a game, or a website. Apps need tools to work, like how you need crayons and paper to draw. For example, a website might need a tool called Python to run.

### The Problem

Sometimes, when you move an app to another computer (like giving a game to a friend), it doesn’t work because that computer is missing a tool, like Python or a specific file. This is like trying to play a game but missing the dice!

### What is a Container?

A container is like a lunchbox that holds:
- Your app (like a sandwich).
- All the tools it needs (like a spoon, napkin, and juice).

With a container, your app works anywhere, because everything it needs is packed inside!

### Simple Example: The Lunchbox Analogy

Imagine you pack a lunchbox for school with:
- Food (your app).
- A spoon, napkin, and drink (the tools).

You don’t need to borrow anything from the school cafeteria—just open the lunchbox and eat! A container does the same for apps, making sure they work on any computer.

### What is Docker?

Docker is a tool that:
- 📦 Creates containers (packs the lunchbox).
- 🚀 Runs containers (opens the lunchbox to use the app).
- 📤 Shares containers (lets others use your app).

It’s like a magic toy box maker that ensures your toys work anywhere!

### Key Terms

Here’s what we’ll use, explained simply:

| **Term**        | **What It Means**                           | **Example**                     |
|------------------|---------------------------------------------|----------------------------------|
| **App**         | Your program                                | A game or website               |
| **Container**   | A box with your app and its tools           | A lunchbox with food and a spoon|
| **Image**       | A blueprint for your container              | A recipe for your lunchbox      |
| **Dockerfile**  | Instructions to make the image              | A recipe card                   |
| **Docker**      | The tool that builds and runs containers    | A chef who packs the lunchbox   |
| **Docker Hub**  | A website with ready-made images            | A recipe book online            |

### Containers vs. Virtual Machines (VMs)

Sometimes people use virtual machines (VMs) instead of containers. Here’s how they compare:

| **Feature**     | **Container**         | **Virtual Machine**   |
|------------------|-----------------------|-----------------------|
| **Speed**       | Fast (starts in seconds) | Slow (takes minutes)  |
| **Size**        | Small (like a lunchbox)  | Large (like a suitcase)|
| **Setup**       | Easy (just a few steps)  | Complex (many settings)|
| **Use**         | Runs apps                | Runs a whole computer  |

Containers are lighter and faster, perfect for apps like ours!

## Step 2: Set Up Your Project

Let’s create a folder for our project, like setting up a desk to build a toy model.

1. **Start Docker Desktop**:
   - Open Docker Desktop from the Start Menu (search for “Docker Desktop”).
   - Wait until the whale 🐳 icon in the system tray (bottom right corner) shows “Docker is running.” This ensures the Docker Engine is active, which is required for all Docker commands.
   - **Why this matters**: Without Docker Desktop running, you’ll get errors like “cannot connect to the Docker daemon” when running commands like `docker run`.

2. **Open Command Prompt**:
   - Press `Win + R`, type `cmd`, and press Enter. This opens a black window where you type commands, like telling your computer what to do.
   - Check Docker is installed and running:
     ```bash
     docker --version
     ```
     - You should see a version number (e.g., `Docker version 20.10.17`). If you see an error like “cannot connect to the Docker daemon,” ensure Docker Desktop is running. Restart it if needed.
     - If Docker is not installed, download it from [https://www.docker.com/products/docker-desktop/](https://www.docker.com/products/docker-desktop/).

3. **Create a Project Folder**:
   - In Command Prompt, create a folder called `docker-project` on your Desktop:
     ```bash
     cd C:\Users\Desktop
     mkdir docker-project
     cd C:\Users\Desktop\docker-project
     ```
     - `mkdir` makes the folder, and `cd` takes you inside, like opening a new notebook.

## Step 3: Exercise A - Getting Started with Docker

Let’s start with two exercises to get comfortable with Docker. First, we’ll pull a ready-made container from Docker Hub, then we’ll build our own simple app that prints “Hello from Docker!” in the Command Prompt.

### Exercise A1: Pull and Run the Hello-World Container

Let’s use Docker Hub to pull a pre-built container called `hello-world`. This is a simple test app that prints a message to confirm Docker is working, and it doesn’t require writing any code or a `Dockerfile`.

#### Goal
Run a container that prints:
```
Hello from Docker!
This message shows that your installation appears to be working correctly.
```

#### Tools You’ll Use
- **Command Prompt**: To run Docker commands, like giving instructions to a robot.
- **Docker Desktop**: To pull and run the container, like a toy box opener.

#### Step-by-Step Instructions

1. **Ensure Docker Desktop is Running**:
   - Open Docker Desktop from the Start Menu (search for “Docker Desktop”).
   - Wait until the whale 🐳 icon in the system tray shows “Docker is running.” If not, restart Docker Desktop and wait 1–2 minutes.
   - **Why this matters**: Docker commands like `docker pull` or `docker run` will fail if Docker Desktop isn’t running.

2. **Pull the hello-world image**:
   - In Command Prompt, type:
     ```bash
     docker pull hello-world
     ```
   - **Explanation**:
     - `docker pull hello-world`: Downloads the `hello-world` image from Docker Hub, like picking up a ready-made toy box.
   - You’ll see output like:
     ```
     Using default tag: latest
     latest: Pulling from library/hello-world
     ...
     Status: Downloaded newer image for hello-world:latest
     ```

3. **Run the container**:
   - Type:
     ```bash
     docker run hello-world
     ```
   - **You’ll see**:
     ```
     Hello from Docker!
     This message shows that your installation appears to be working correctly.
     ...
     ```
   - **Explanation**:
     - `docker run hello-world`: Creates and runs a container from the `hello-world` image, like opening a toy box to see what’s inside.
   - 🎉 **You just ran a container without writing any code!**

#### Why This Matters
- This shows how easy it is to use Docker Hub to get pre-built apps.
- It confirms your Docker installation works perfectly, without needing to build anything yourself.

### Exercise A2: Create a Simple Docker App

Now let’s make an app that prints “Hello from Docker!” in the Command Prompt, like writing a note and showing it to your friend.

#### Goal
Make an app that shows:
```
Hello from Docker!
```

#### Tools You’ll Use
- **VS Code (preferred) or Notepad**: To write code, like writing a note.
- **Command Prompt**: To run Docker commands and open VS Code, like giving instructions to a robot.
- **Docker Desktop**: To build and run containers, like a toy box maker.

#### Step-by-Step Instructions

1. **Ensure Docker Desktop is Running**:
   - Open Docker Desktop and confirm the whale 🐳 icon in the system tray shows “Docker is running.” If not, start it and wait 1–2 minutes.
   - **Why this matters**: Commands like `docker build` and `docker run` require Docker Desktop to be active.

2. **Navigate to the project folder**:
   - In Command Prompt, type:
     ```bash
     cd C:\Users\Desktop\docker-project
     ```

3. **Open VS Code from the terminal**:
   - Type:
     ```bash
     code .
     ```
   - This opens VS Code in `C:\Users\Desktop\docker-project`.
   - **If you get “‘code’ is not recognized”**:
     - Download VS Code from [https://code.visualstudio.com/](https://code.visualstudio.com/).
     - During installation, check **Add to PATH** to enable the `code` command.
     - After installation, open a new Command Prompt and retry `code .`.
     - Alternatively, open VS Code manually, go to **File > Open Folder**, and select `C:\Users\Nneka\Desktop\docker-project`.

4. **Create the app file**:
   - **Using VS Code** (preferred):
     - In the Explorer pane (left sidebar), right-click and select **New File**.
     - Type `app.py` (exactly, no extension) and press Enter.
     - Double-click `app.py` to open it, then paste:
       ```python
       print("Hello from Docker!")
       ```
     - Save with `Ctrl + S` (this preserves the name `app.py` without adding an extension).
   - **Using Notepad** (alternative):
     - Type:
       ```bash
       notepad app.py
       ```
     - Paste the code above.
     - Save: Go to **File > Save As**, set **Save as type** to **All Files (*.*)**, ensure the file name is `app.py`, and save in `C:\Users\Desktop\docker-project`. Close Notepad.
   - **Explanation**:
     - `print("Hello from Docker!")`: Tells the computer to show this message.
     - This is a simple Python program, like writing a note that says “Hello!”

5. **Create the Dockerfile**:
   - **Using VS Code** (preferred):
     - In the Explorer pane, right-click and select **New File**.
     - Type `Dockerfile` (exactly, no extension) and press Enter.
     - Double-click `Dockerfile` to open it, then paste:
       ```dockerfile
       FROM python:3.11-slim
       WORKDIR /app
       COPY app.py .
       CMD ["python", "app.py"]
       ```
     - Save with `Ctrl + S` (this preserves the name `Dockerfile` without adding `.dockerfile` or `.txt`).
   - **Using Notepad** (alternative):
     - Type:
       ```bash
       notepad Dockerfile
       ```
     - Paste the code above.
     - Save: Go to **File > Save As**, set **Save as type** to **All Files (*.*)**, ensure the file name is `Dockerfile`, and save in `C:\Users\Desktop\docker-project`. Close Notepad.
   - **Line-by-Line Explanation**:
     - `FROM python:3.11-slim`: Uses a small version of Python from Docker Hub, like picking a basic toy box.
     - `WORKDIR /app`: Sets a folder inside the container, like choosing a shelf for your toys.
     - `COPY app.py .`: Copies `app.py` into the container, like putting your note in the box.
     - `CMD ["python", "app.py"]`: Tells the container to run your app, like saying “read the note!”

6. **Verify file names**:
   - In Command Prompt, type:
     ```bash
     cd C:\Users\Desktop\docker-project
     dir
     ```
   - Ensure you see:
     ```
     app.py
     Dockerfile
     ```
   - If you see `app.py.txt`, `Dockerfile.txt`, or `Dockerfile.dockerfile`, rename them:
     ```bash
     ren app.py.txt app.py
     ren Dockerfile.txt Dockerfile
     ren Dockerfile.dockerfile Dockerfile
     ```
   - **Using VS Code**: In the Explorer pane, check that file names are `app.py` and `Dockerfile` with no extensions. If incorrect (e.g., `Dockerfile.dockerfile`), right-click, select **Rename**, type the correct name (e.g., `Dockerfile`), and press Enter.

7. **Build the Docker image**:
   - Type:
     ```bash
     docker build -t hello-docker .
     ```
   - **Explanation**:
     - `docker build`: Tells Docker to make an image, like following a recipe.
     - `-t hello-docker`: Names the image “hello-docker,” like labeling your toy box.
     - `.`: Uses the `Dockerfile` in the current folder (`C:\Users\Nneka\Desktop\docker-project`).
   - You’ll see Docker download Python and build the image. This is like packing your lunchbox.

8. **Run the app in Docker**:
   - Type:
     ```bash
     docker run hello-docker
     ```
   - **You’ll see**:
     ```
     Hello from Docker!
     ```
   - **Explanation**:
     - `docker run hello-docker`: Opens the container and runs your app, like opening the lunchbox to read the note.
   - 🎉 **You just ran your first custom Docker app!**

#### Troubleshooting Common Issues
- **Dockerfile not found error**:
  - If you see `failed to read dockerfile: open Dockerfile: no such file or directory`, the `Dockerfile` may have been saved with an extension (e.g., `Dockerfile.txt` or `Dockerfile.dockerfile`).
  - **Fix with Command Prompt**:
    - Check with:
      ```bash
      cd C:\Users\Desktop\docker-project
      dir
      ```
    - If you see `Dockerfile.txt` or `Dockerfile.dockerfile`, rename it:
      ```bash
      ren Dockerfile.txt Dockerfile
      ren Dockerfile.dockerfile Dockerfile
      ```
  - **Fix with VS Code**:
    - Open VS Code (`code C:\Users\Desktop\docker-project`).
    - In the Explorer pane, right-click the file (e.g., `Dockerfile.txt` or `Dockerfile.dockerfile`), select **Rename**, type `Dockerfile`, and press Enter.
  - **Fix with File Explorer**:
    - Navigate to `C:\Users\Desktop\docker-project`.
    - Enable **File name extensions** (View > Show > File name extensions).
    - Right-click the file and rename to `Dockerfile`.
  - If the file is missing, recreate it as described in step 5.
- **VS Code not opening from terminal**:
  - If `code .` fails with “‘code’ is not recognized,” ensure VS Code is installed and added to PATH:
    - Download VS Code from [https://code.visualstudio.com/](https://code.visualstudio.com/) and check **Add to PATH** during installation.
    - Alternatively, open VS Code manually and use **File > Open Folder** to select `C:\Users\Nneka\Desktop\docker-project`.
- **Docker Desktop not running**:
  - Ensure Docker Desktop is open and shows “Docker is running” (whale 🐳 icon). If not, start it from the Start Menu and wait 1–2 minutes.
  - If you see “cannot connect to the Docker daemon,” restart Docker Desktop.

#### Why This Matters
- You’ve packed a Python app into a container, like putting a toy and its batteries in a box.
- This app will work on any computer with Docker, no matter what tools are installed!

## Step 4: Exercise B - Create a Web App in Docker

Now let’s make a webpage that says “Hello from Docker Web App!” in your browser, like putting a poster on the internet.

#### Goal
Open a website at `http://localhost:5000` that shows:
```
Hello from Docker Web App!
```

#### Tools You’ll Use
- **VS Code (preferred) or Notepad**: To write code, like creating a poster.
- **Command Prompt**: To run Docker commands and open VS Code.
- **Docker Desktop**: To build and run containers, like a poster frame maker.

#### Step-by-Step Instructions

1. **Ensure Docker Desktop is Running**:
   - Open Docker Desktop from the Start Menu and confirm the whale 🐳 icon in the system tray shows “Docker is running.” If not, start it and wait 1–2 minutes.
   - **Why this matters**: Commands like `docker build` and `docker run` require Docker Desktop to be active.

2. **Create a new folder**:
   - In Command Prompt, type:
     ```bash
     cd C:\Users\Desktop
     mkdir docker-web-app
     cd C:\Users\Nneka\Desktop\docker-web-app
     ```

3. **Open VS Code from the terminal**:
   - Type:
     ```bash
     code .
     ```
   - This opens VS Code in `C:\Users\Desktop\docker-web-app`.
   - **If you get “‘code’ is not recognized”**:
     - Download VS Code from [https://code.visualstudio.com/](https://code.visualstudio.com/).
     - Check **Add to PATH** during installation.
     - Retry `code .` in a new Command Prompt, or open VS Code manually and select **File > Open Folder** > `C:\Users\Nneka\Desktop\docker-web-app`.

4. **Create the app file**:
   - **Using VS Code** (preferred):
     - In the Explorer pane, right-click and select **New File**.
     - Type `app.py` (exactly, no extension) and press Enter.
     - Double-click `app.py` to open it, then paste:
       ```python
       from flask import Flask
       app = Flask(__name__)

       @app.route('/')
       def home():
           return "Hello from Docker Web App!"

       if __name__ == "__main__":
           app.run(host="0.0.0.0", port=5000)
       ```
     - Save with `Ctrl + S` (this preserves the name `app.py`).
   - **Using Notepad** (alternative):
     - Type:
       ```bash
       notepad app.py
       ```
     - Paste the code above.
     - Save: Go to **File > Save As**, set **Save as type** to **All Files (*.*)**, ensure the file name is `app.py`, and save in `C:\Users\Desktop\docker-web-app`. Close Notepad.
   - **Line-by-Line Explanation**:
     - `from flask import Flask`: Loads Flask, a tool to make websites, like borrowing a paintbrush.
     - `app = Flask(__name__)`: Creates a web app, like starting a new poster.
     - `@app.route('/')`: Says “when someone visits the main page, show this.”
     - `def home(): return "Hello from Docker Web App!"`: Shows this message on the page.
     - `app.run(host="0.0.0.0", port=5000)`: Runs the website on port 5000, like putting your poster at a specific address.

5. **Create the requirements file**:
   - **Using VS Code** (preferred):
     - In the Explorer pane, right-click and select **New File**.
     - Type `requirements.txt` (exactly, no extension) and press Enter.
     - Double-click `requirements.txt` to open it, then paste:
       ```
       flask
       ```
     - Save with `Ctrl + S`.
   - **Using Notepad** (alternative):
     - Type:
       ```bash
       notepad requirements.txt
       ```
     - Paste the code above.
     - Save: Go to **File > Save As**, set **Save as type** to **All Files (*.*)**, ensure the file name is `requirements.txt`, and save in `C:\Users\Desktop\docker-web-app`. Close Notepad.
   - **Explanation**:
     - `flask`: Tells Docker to install Flask, like saying “include the paintbrush in the box.”

6. **Create the Dockerfile**:
   - **Using VS Code** (preferred):
     - In the Explorer pane, right-click and select **New File**.
     - Type `Dockerfile` (exactly, no extension) and press Enter.
     - Double-click `Dockerfile` to open it, then paste:
       ```dockerfile
       FROM python:3.11-slim
       WORKDIR /app
       COPY requirements.txt .
       RUN pip install -r requirements.txt
       COPY app.py .
       CMD ["python", "app.py"]
       ```
     - Save with `Ctrl + S`.
   - **Using Notepad** (alternative):
     - Type:
       ```bash
       notepad Dockerfile
       ```
     - Paste the code above.
     - Save: Go to **File > Save As**, set **Save as type** to **All Files (*.*)**, ensure the file name is `Dockerfile`, and save in `C:\Users\Nneka\Desktop\docker-web-app`. Close Notepad.
   - **Line-by-Line Explanation**:
     - `FROM python:3.11-slim`: Uses Python, like in Exercise A2.
     - `WORKDIR /app`: Sets a folder in the container.
     - `COPY requirements.txt .`: Copies the requirements file.
     - `RUN pip install -r requirements.txt`: Installs Flask, like adding the paintbrush.
     - `COPY app.py .`: Copies the app file.
     - `CMD ["python", "app.py"]`: Runs the web app.

7. **Verify file names**:
   - In Command Prompt, type:
     ```bash
     cd C:\Users\Desktop\docker-web-app
     dir
     ```
   - Ensure you see:
     ```
     app.py
     Dockerfile
     requirements.txt
     ```
   - If you see `app.py.txt`, `Dockerfile.txt`, `Dockerfile.dockerfile`, or `requirements.txt.txt`, rename them:
     ```bash
     ren app.py.txt app.py
     ren Dockerfile.txt Dockerfile
     ren Dockerfile.dockerfile Dockerfile
     ren requirements.txt.txt requirements.txt
     ```
   - **Using VS Code**: In the Explorer pane, check that file names are correct with no extensions. If incorrect (e.g., `Dockerfile.dockerfile`), right-click, select **Rename**, and type the correct name.

8. **Build the image**:
   - Type:
     ```bash
     docker build -t web-hello .
     ```
   - **Explanation**:
     - `docker build`: Creates an image from the `Dockerfile`.
     - `-t web-hello`: Names the image “web-hello,” like labeling a new toy box.
     - `.`: Uses the `Dockerfile` in `C:\Users\Nneka\Desktop\docker-web-app`.

9. **Run the container**:
   - Type:
     ```bash
     docker run -p 5000:5000 web-hello
     ```
   - **Explanation**:
     - `-p 5000:5000`: Connects port 5000 on your computer to port 5000 in the container, like opening a window to see the poster.

10. **Open the app in your browser**:
    - Open your browser (e.g., Chrome, Edge) and go to: **http://localhost:5000**
    - **You’ll see**:
      ```
      Hello from Docker Web App!
      ```
    - **Explanation**:
      - Your browser shows the webpage from the container, like seeing your poster on the wall!
    - To stop the container, press `Ctrl + C` in the Command Prompt.
    - 🎉 **You just ran a website inside Docker!**

#### Troubleshooting Common Issues
- **Dockerfile not found error**:
  - If you see `failed to read dockerfile: open Dockerfile: no such file or directory`, check for incorrect file names.
  - **Fix with Command Prompt**:
    - Run:
      ```bash
      cd C:\Users\Desktop\docker-web-app
      dir
      ```
    - If you see `Dockerfile.txt` or `Dockerfile.dockerfile`, rename it:
      ```bash
      ren Dockerfile.txt Dockerfile
      ren Dockerfile.dockerfile Dockerfile
      ```
  - **Fix with VS Code**:
    - Open VS Code (`code C:\Users\Nneka\Desktop\docker-web-app`).
    - In the Explorer pane, right-click the file (e.g., `Dockerfile.dockerfile`), select **Rename**, type `Dockerfile`, and press Enter.
  - **Fix with File Explorer**:
    - Navigate to `C:\Users\Desktop\docker-web-app`.
    - Enable **File name extensions** (View > Show > File name extensions).
    - Right-click the file and rename to `Dockerfile`.
  - If the file is missing, recreate it as described in step 6.
- **Browser shows “This site can’t be reached”**:
  - Ensure the `docker run` command is active in the Command Prompt (don’t press `Ctrl + C` yet).
  - Check that you used `-p 5000:5000` in the `docker run` command.
  - Try accessing `http://127.0.0.1:5000` instead of `http://localhost:5000`.
  - Verify the `app.py` content, especially `host="0.0.0.0"` and `port=5000`.
- **Docker Desktop not running**:
  - Ensure Docker Desktop shows “Docker is running” (whale 🐳 icon). If not, start it and wait 1–2 minutes.
  - If you see “cannot connect to the Docker daemon,” restart Docker Desktop.
- **VS Code not opening**:
  - If `code .` fails, install VS Code from [https://code.visualstudio.com/](https://code.visualstudio.com/) with **Add to PATH** checked, or open VS Code manually and select `C:\Users\Desktop\docker-web-app`.

#### Why This Matters
- You’ve built a web app in a container, like packing a poster and its frame to display anywhere.
- This app works on any computer with Docker, even if Flask or Python isn’t installed.

## Step 5: Exercise C - Explore an Ubuntu Container

Let’s run an Ubuntu container interactively to explore a Linux environment, like opening a new toy box to play with its tools. Ubuntu is a popular Linux operating system, and we’ll use its container to try basic commands.

#### Goal
Run an Ubuntu container and use commands like:
```
whoami
ls
```
You’ll see the container’s user and files, like checking who’s playing with the toy box and what’s inside it.

#### Tools You’ll Use
- **Command Prompt**: To run Docker commands.
- **Docker Desktop**: To pull and run the Ubuntu container.

#### Step-by-Step Instructions

1. **Ensure Docker Desktop is Running**:
   - Open Docker Desktop from the Start Menu and confirm the whale 🐳 icon shows “Docker is running.” If not, start it and wait 1–2 minutes.
   - **Why this matters**: Commands like `docker pull` and `docker run` require Docker Desktop to be active.

2. **Pull the Ubuntu image**:
   - In Command Prompt, type:
     ```bash
     docker pull ubuntu
     ```
   - **Explanation**:
     - `docker pull ubuntu`: Downloads the latest Ubuntu image from Docker Hub, like picking up a Linux toy box.
   - You’ll see output like:
     ```
     Using default tag: latest
     latest: Pulling from library/ubuntu
     ...
     Status: Downloaded newer image for ubuntu:latest
     ```

3. **Run the Ubuntu container interactively**:
   - Type:
     ```bash
     docker run -it ubuntu
     ```
   - **Explanation**:
     - `-it`: Runs the container interactively with a terminal, like opening the toy box and playing inside it.
     - `ubuntu`: Uses the Ubuntu image.
   - You’ll see a prompt like:
     ```
     root@<container-id>:/#
     ```
   - This means you’re inside the container’s terminal, acting as the “root” user (like the toy box owner).

4. **Try basic commands**:
   - Type:
     ```bash
     whoami
     ```
     - **You’ll see**:
       ```
       root
       ```
     - This shows you’re the “root” user, like the boss of the toy box.
   - Type:
     ```bash
     ls
     ```
     - **You’ll see** (may vary):
       ```
       bin  boot  dev  etc  home  lib  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
       ```
     - This lists folders in the container, like checking what’s in the toy box.
   - Type:
     ```bash
     pwd
     ```
     - **You’ll see**:
       ```
       /root
       ```
     - This shows your current location in the container, like knowing which room you’re in.

5. **Optional: Create a file**:
   - The Ubuntu image is minimal and doesn’t include text editors like `nano`. Let’s install `nano`:
     ```bash
     apt-get update
     apt-get install -y nano
     ```
     - `apt-get update`: Updates the package list, like checking for new toys.
     - `apt-get install -y nano`: Installs the `nano` editor without prompting for confirmation.
   - Create a file:
     ```bash
     nano myfile.txt
     ```
     - Type: `Hello from Ubuntu!`
     - Save: Press `Ctrl + O`, Enter, then exit with `Ctrl + X`.
   - Check the file:
     ```bash
     cat myfile.txt
     ```
     - **You’ll see**:
       ```
       Hello from Ubuntu!
       ```

6. **Exit the container**:
   - Type:
     ```bash
     exit
     ```
   - This returns you to the Windows Command Prompt.
   - **Explanation**:
     - The container stops when you exit, like closing the toy box. Your changes (e.g., `myfile.txt`) are lost unless you save them (advanced topic).

#### Troubleshooting Common Issues
- **“Cannot connect to the Docker daemon”**:
  - Ensure Docker Desktop is running (whale 🐳 icon shows “Docker is running”). Restart it if needed.
- **Pull fails with network error**:
  - Check your internet connection.
  - Try again with:
    ```bash
    docker pull ubuntu
    ```
- **Prompt doesn’t appear after `docker run -it ubuntu`**:
  - Ensure you used `-it` in the command.
  - Run `docker ps -a` to check if the container started. If it exited, retry:
    ```bash
    docker run -it ubuntu
    ```
- **Command not found (e.g., `nano`)**:
  - The Ubuntu image is minimal. Install tools as shown in step 5 (e.g., `apt-get install -y nano`).

#### Why This Matters
- You’ve explored a Linux environment inside a container, like playing with a new toy box.
- This shows how Docker lets you use different operating systems without installing them on your computer.

## Step 6: Summary and Next Steps

You’ve done something amazing! You:
- Learned what containers and Docker are, like packing a lunchbox for apps.
- Pulled and ran a ready-made `hello-world` container from Docker Hub (**Exercise A1**).
- Built a simple app that prints “Hello from Docker!” in the Command Prompt (**Exercise A2**).
- Created a webpage that says “Hello from Docker Web App!” in your browser (**Exercise B**).
- Explored an Ubuntu container interactively, using Linux commands (**Exercise C**).
- Used Dockerfiles and images to pack and run apps, like following a recipe to build a toy box.

### Summary Table

| **Concept**                 | **What You Learned**                              |
|-----------------------------|--------------------------------------------------|
| **What is a container**     | A box with your app and its tools, works anywhere |
| **What is Docker**          | A tool to create and run containers              |
| **Dockerfile**              | Instructions to make an image, like a recipe     |
| **Image**                   | A blueprint for a container, like a toy design   |
| **How to build an app**     | Use `docker build` to create an image            |
| **How to run an app**       | Use `docker run` to start the container          |
| **View a web app**          | Visit `http://localhost:5000` in a browser       |
| **Explore a system**        | Use `docker run -it` to interact with a container|

### Next Steps
You’ve mastered the basics of Docker! Here’s what you can do next:
- **Try more Docker projects**: Build a game or another website using Docker (see resources below).
- **Learn Docker Compose**: Run multiple containers together, like organizing a toy store. Start with the Docker Docs: [https://docs.docker.com/compose/](https://docs.docker.com/compose/).
- **Share on Docker Hub**: Push your images to Docker Hub, like posting a recipe online.
- **Explore more containers**: Try pulling and running other images, like `nginx` for a web server:
  ```bash
  docker run -p 8080:80 nginx
  ```
  - Visit `http://localhost:8080` to see the Nginx welcome page.
- **Troubleshoot issues**: If anything didn’t work, share the `dir` output or error messages from any exercise.

## Step 7: Further Reading

Here are resources to learn more about Docker, like extra guidebooks for your adventure:

- **Docker 101 Tutorial | Docker**  
  [https://www.docker.com/101-tutorial/](https://www.docker.com/101-tutorial/)  
  An official, easy tutorial from Docker on building and running containers.

- **Get Started | Docker Docs**  
  [https://docs.docker.com/get-started/](https://docs.docker.com/get-started/)  
  The official Docker guide, with steps for beginners on installation and basics.

- **Docker Tutorial - GeeksforGeeks**  
  [https://www.geeksforgeeks.org/docker-tutorial/](https://www.geeksforgeeks.org/docker-tutorial/)  
  A detailed tutorial covering Docker from scratch, including containers and images.

- **Docker for Beginners: A Practical Guide to Containers | DataCamp**  
  [https://www.datacamp.com/blog/docker-for-beginners](https://www.datacamp.com/blog/docker-for-beginners)  
  A hands-on guide with examples to understand containers.

- **Docker Tutorial for Beginners - YouTube**  
  [https://www.youtube.com/watch?v=pTFZFxd4hOI](https://www.youtube.com/watch?v=pTFZFxd4hOI)  
  A video for beginners, great for visual learners starting with Docker.

These will help you dive deeper into Docker and try more cool projects!

## Optional: Save to GitHub

Want to save your work online, like putting your toy designs in a shared album? Here’s how to save the `docker-project` and `docker-web-app` folders:

1. **Initialize Git for docker-project**:
   - In Command Prompt:
     ```bash
     cd C:\Users\Nneka\Desktop\docker-project
     git init
     ```
   - **Using VS Code**:
     - Open VS Code (`code C:\Users\Nneka\Desktop\docker-project`).
     - Go to the Source Control tab (Ctrl + Shift + G), click **Initialize Repository**.

2. **Create .gitignore**:
   - **Using VS Code** (preferred):
     - In the Explorer pane, right-click and select **New File**.
     - Type `.gitignore` (exactly, no extension) and press Enter.
     - Paste:
       ```
       .DS_Store
       ```
     - Save with `Ctrl + S`.
   - **Using Notepad** (alternative):
     - Type:
       ```bash
       notepad .gitignore
       ```
     - Paste the code above.
     - Save: Go to **File > Save As**, set **Save as type** to **All Files (*.*)**, ensure the file name is `.gitignore`, and save in `C:\Users\Nneka\Desktop\docker-project`. Close Notepad.

3. **Commit and Push**:
   - In Command Prompt:
     ```bash
     git add .
     git commit -m "Docker Beginner Guide: Hello App"
     ```
   - **Using VS Code**:
     - Go to the Source Control tab, enter a commit message like “Docker Beginner Guide: Hello App,” and click the checkmark to commit.
   - Create a GitHub repo named `docker-project`, then run:
     ```bash
     git remote add origin https://github.com/your-username/docker-project.git
     git branch -M main
     git push -u origin main
     ```
   - **Using VS Code**: Follow prompts in the Source Control tab to push to GitHub after setting the remote.

4. **Repeat for docker-web-app**:
   - Follow the same steps in `C:\Users\Nneka\Desktop\docker-web-app`, creating a separate GitHub repo named `docker-web-app`.

## Final Thought

You’ve done something most beginners don’t: you ran pre-built containers, built **two Docker apps**, and explored a Linux environment! Your “Hello from Docker!” app prints a message, your web app runs a website, and your Ubuntu container lets you play with Linux commands. Even if you’ve never coded or used a terminal before, you made it happen. 
