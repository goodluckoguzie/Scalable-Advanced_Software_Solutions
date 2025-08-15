# Week 9: Introduction to Kubernetes – Orchestrating and Deploying Containers

## Introduction

Welcome to Week 9 of our Web Application Development course! This week, we’re diving into **Kubernetes** (often called “K8s”), a powerful tool known as an **orchestrator**. If Docker containers are like shipping containers that package your app to run anywhere, Kubernetes is the shipping yard manager who organizes, schedules, and keeps everything running smoothly. Kubernetes ensures your app’s containers are always up, restarts them if they crash, scales them to handle more users, and manages updates without downtime. It’s like an autopilot for your containerized apps, making it easier to deploy and manage them in production. Why is this cool? Imagine your to-do list app from previous weeks getting super popular. You’d need multiple copies of it running, maybe alongside a database, all spread across different machines. Doing that manually would be a headache, but Kubernetes automates it all. In this lab, we’ll use **Docker Desktop’s built-in Kubernetes** to run our to-do app locally, learning the basics in a simple way. By the end, you’ll deploy the app to a Kubernetes cluster, access it in your browser, and understand how Kubernetes manages containers. Let’s get orchestrating! 🚀

## What You’ll Learn

- **Orchestration & Kubernetes**: What an orchestrator does and why Kubernetes is great for managing containers (automating deployment, scaling, and recovery).

- **Pods & Deployments**: How Kubernetes uses **pods** (small groups of containers that run together) and **deployments** (to manage pods, ensure the right number are running, and handle updates).

- **Services & NodePort**: How a Kubernetes **service** exposes your app to the network, and what a **NodePort** service does (opens a specific port on your PC to reach the app).

- **Kubernetes YAML Manifests**: Writing a YAML file to define your app’s setup, including fields like `apiVersion`, `kind`, `metadata`, and `spec`.

- **Kubectl Commands**: Using the Kubernetes command-line tool `kubectl` to deploy (`kubectl apply`), check status (`kubectl get`), and clean up (`kubectl delete`).

## What You’ll Do

1. **Enable Kubernetes in Docker Desktop**: Turn on Kubernetes to run a local single-node cluster.

2. **Build the “Getting-Started” App**: Clone the to-do app repository and build its Docker image (`getting-started`).

3. **Test the App Locally**: Run the app as a standalone container to confirm it works on `http://localhost:3000`.

4. **Write a Kubernetes YAML**: Create `bb.yaml` to define a **Deployment** (for the app) and a **Service** (to expose it).

5. **Deploy to Kubernetes**: Use `kubectl apply` to launch the app in Kubernetes.

6. **Verify and Access**: Check the deployment and service with `kubectl get`, and access the app via `http://localhost:30001`.

7. **Clean Up**: Remove the app with `kubectl delete` and review next steps.

## Prerequisites

- **Docker Desktop**: Installed and running on Windows 10/11. Verify with:

  ```bash

  docker version

  ```

  **Command Prompt (single-line):**

  ```bash

  docker version

  ```

  Look for the Docker whale icon (🐳) in the system tray and ensure you see Client and Server details (e.g., `Server: Docker Desktop 4.x.x`).

- **PowerShell or Command Prompt**: Basic skills to navigate directories (e.g., `cd`, `ls` or `dir`). We’ll use PowerShell for commands, but Command Prompt works too.

- **Git**: Installed to clone the app repository. Download from [git-scm.com](https://git-scm.com) or use the ZIP from [GitHub](https://github.com/docker/getting-started-app). Verify with:

  ```bash

  git --version

  ```

  **Command Prompt (single-line):**

  ```bash

  git --version

  ```

- **Internet Access**: Needed to clone the repository and pull base images (e.g., Node.js) during the Docker build.

- **Lab Files**: The W9 - Practical A containing `bb.yaml` and `Dockerfile`. If unavailable, we’ll create `bb.yaml` and `Dockerfile` manually.

- **Optional: “Getting-Started” App**: If you have the to-do app from Week 7/8, great! If not, we’ll clone it.

Let’s jump in and set up Kubernetes!

## Step 1: Enabling Kubernetes in Docker Desktop

First, we’ll activate Kubernetes in Docker Desktop to run a local single-node Kubernetes cluster.

1. **Open Docker Desktop Settings**:

   - Launch Docker Desktop (Start menu or system tray).

   - Click the **gear icon** (Settings) in the top-right corner.

2. **Enable Kubernetes**:

   - Go to the **Kubernetes** tab in Settings.

   - Check the box **Enable Kubernetes**.

   - Click **Apply & Restart**. Docker Desktop will restart and set up Kubernetes (this may take 1–2 minutes).

3. **Verify Kubernetes is Running**:

   - Once restarted, check the Kubernetes tab. You should see a **green light** or “Kubernetes running” status.

   - In PowerShell, confirm `kubectl` is available:

     ```bash

     kubectl version --client

     ```

     **Command Prompt (single-line):**

     ```bash

     kubectl version --client

     ```

     This should show the `kubectl` version (e.g., `Client Version: v1.27.x`). Docker Desktop includes `kubectl`, so no separate installation is needed.

**Debugging Tips**:

- **Kubernetes not starting**: Check Docker Desktop logs (Settings > Troubleshoot) or restart Docker Desktop. Ensure sufficient memory (Settings > Resources > at least 4GB RAM).

- **“kubectl: command not found”**: Verify Docker Desktop added `kubectl` to your PATH. Restart PowerShell or try:

  ```bash

  "C:\Program Files\Docker\Docker\resources\bin\kubectl.exe" version --client

  ```

  **Command Prompt (single-line):**

  ```bash

  "C:\Program Files\Docker\Docker\resources\bin\kubectl.exe" version --client

  ```

  If persistent, reinstall Docker Desktop.

**What We Did**: You enabled a local Kubernetes cluster in Docker Desktop, setting up a control plane and node. You’re ready to deploy apps!

## Step 2: Build and Run the “Getting-Started” App Locally

Before deploying to Kubernetes, let’s ensure the to-do app works as a standalone Docker container.

1. **Clone the Repository**:

   In PowerShell, navigate to your working directory (e.g., Desktop):

   ```bash

   cd C:\Users\<YourName>\Desktop

   ```

   **Command Prompt (single-line):**

   ```bash

   cd C:\Users\<YourName>\Desktop

   ```

   Clone the app:

   ```bash

   git clone https://github.com/docker/getting-started-app.git

   ```

   **Command Prompt (single-line):**

   ```bash

   git clone https://github.com/docker/getting-started-app.git

   ```

   This creates a `getting-started-app` folder. If you already have it, skip cloning.

2. **Navigate to the App Directory**:

   ```bash

   cd getting-started-app

   ```

   **Command Prompt (single-line):**

   ```bash

   cd getting-started-app

   ```

   Verify contents with:

   ```bash

   ls

   ```

   **Command Prompt (single-line):**

   ```bash

   dir

   ```

   You should see app files (e.g., `src/`, `package.json`). A `Dockerfile` may or may not be present, depending on your lab files.

3. **Create or Verify the Dockerfile**:

   If you have the lab files (W9 - Practical A) containing the `Dockerfile`, copy it to the `getting-started-app` directory:

   ```bash

   copy C:\Users\<YourName>\Desktop\W9-Practical\Dockerfile .

   ```

   **Command Prompt (single-line):**

   ```bash

   copy C:\Users\<YourName>\Desktop\W9-Practical\Dockerfile .

   ```

   If you do not have the `Dockerfile`, create one manually:

   ```bash

   type nul > Dockerfile

   ```

   **Command Prompt (single-line):**

   ```bash

   type nul > Dockerfile

   ```

   Open it for editing with:

   - Notepad:

     ```bash

     notepad Dockerfile

     ```

     **Command Prompt (single-line):**

     ```bash

     notepad Dockerfile

     ```

   - Or Visual Studio Code:

     ```bash

     code Dockerfile

     ```

     **Command Prompt (single-line):**

     ```bash

     code Dockerfile

     ```

   Paste and save this content:

   ```dockerfile

   # syntax=docker/dockerfile:1

   FROM node:18-alpine

   WORKDIR /app

   COPY . .

   RUN yarn install --production

   CMD ["node", "src/index.js"]

   EXPOSE 3000

   ```

4. **Verify and Update package.json**:

   The `Dockerfile` uses `node src/index.js`, so `package.json` should be compatible. Check `package.json`:

   ```bash

   notepad package.json

   ```

   **Command Prompt (single-line):**

   ```bash

   notepad package.json

   ```

   Or:

   ```bash

   code package.json

   ```

   **Command Prompt (single-line):**

   ```bash

   code package.json

   ```

   Ensure the `scripts` section includes:

   ```json

   "scripts": {

     "start": "node src/index.js",

     "dev": "nodemon -L src/index.js",

     "prettify": "prettier -l --write \"**/*.js\"",

     "test": "jest"

   }

   ```

   If the `start` script is missing, add `"start": "node src/index.js"`. Example full `package.json`:

   ```json

   {

     "name": "101-app",

     "version": "1.0.0",

     "main": "index.js",

     "license": "MIT",

     "scripts": {

       "start": "node src/index.js",

       "dev": "nodemon -L src/index.js",

       "prettify": "prettier -l --write \"**/*.js\"",

       "test": "jest"

     },

     "dependencies": {

       "express": "^4.18.2",

       "mysql2": "^2.3.3",

       "sqlite3": "^5.1.2",

       "uuid": "^9.0.0",

       "wait-port": "^1.0.4"

     },

     "resolutions": {

       "ansi-regex": "5.0.1"

     },

     "prettier": {

       "trailingComma": "all",

       "tabWidth": 4,

       "useTabs": false,

       "semi": true,

       "singleQuote": true

     },

     "devDependencies": {

       "jest": "^29.3.1",

       "nodemon": "^2.0.20",

       "prettier": "^2.7.1"

     }

   }

   ```

   Save the file. Note: The `Dockerfile` uses `yarn install --production`, so ensure a `yarn.lock` file exists or run `yarn install` locally to generate it.

5. **Copy bb.yaml (if provided)**:

   If you have the lab files (W9 - Practical A) containing `bb.yaml`, copy it:

   ```bash

   copy C:\Users\<YourName>\Desktop\W9-Practical\bb.yaml .

   ```

   **Command Prompt (single-line):**

   ```bash

   copy C:\Users\<YourName>\Desktop\W9-Practical\bb.yaml .

   ```

6. **Build the Docker Image**:

   Run:

   ```bash

   docker build -t getting-started .

   ```

   **Command Prompt (single-line):**

   ```bash

   docker build -t getting-started .

   ```

   This builds the image using the `Dockerfile`, tagging it `getting-started`. It pulls a Node.js base image (if needed), installs dependencies, and prepares the app.

7. **Run the Container**:

   Start the app:

   ```bash

   docker run -d -p 127.0.0.1:3000:3000 getting-started

   ```

   **Command Prompt (single-line):**

   ```bash

   docker run -d -p 127.0.0.1:3000:3000 getting-started

   ```

   - `-d`: Runs in detached mode.

   - `-p 127.0.0.1:3000:3000`: Maps port 3000 (container) to 3000 (localhost).

8. **Test the App**:

   Open `http://localhost:3000` in your browser. You should see the to-do list app. Add a task to confirm it works.

9. **Stop the Container**:

   Find the container ID:

   ```bash

   docker ps

   ```

   **Command Prompt (single-line):**

   ```bash

   docker ps

   ```

   Stop it:

   ```bash

   docker stop <container-id>

   ```

   **Command Prompt (single-line):**

   ```bash

   docker stop <container-id>

   ```

**Debugging Tips**:

- **“This site can’t be reached” (ERR_CONNECTION_REFUSED)**: If `http://localhost:3000` fails, the container may have crashed. Check:

  ```bash

  docker ps -a

  ```

  **Command Prompt (single-line):**

  ```bash

  docker ps -a

  ```

  Look for `getting-started` containers with `STATUS: Exited (1)`. View logs:

  ```bash

  docker logs <container-id>

  ```

  **Command Prompt (single-line):**

  ```bash

  docker logs <container-id>

  ```

  If you see `yarn: command not found`, ensure `yarn` is installed or generate a `yarn.lock` file:

  ```bash

  yarn install

  docker build -t getting-started .

  ```

  **Command Prompt (single-line):**

  ```bash

  yarn install

  docker build -t getting-started .

  ```

- **Verify `src/index.js` exists**:

  ```bash

  dir src

  ```

  **Command Prompt (single-line):**

  ```bash

  dir src

  ```

  Ensure `index.js` is present, as the `CMD` uses it.

- **“port is already allocated”**: Port 3000 is in use. Check:

  ```bash

  netstat -ano | findstr :3000

  ```

  **Command Prompt (single-line):**

  ```bash

  netstat -ano | findstr :3000

  ```

  Stop conflicting containers:

  ```bash

  docker stop <container-id>

  ```

  **Command Prompt (single-line):**

  ```bash

  docker stop <container-id>

  ```

  Or processes:

  ```bash

  taskkill /PID <pid> /F

  ```

  **Command Prompt (single-line):**

  ```bash

  taskkill /PID <pid> /F

  ```

- **“Cannot connect to the Docker daemon”**: Restart Docker Desktop (system tray > Quit, then relaunch). Verify:

  ```bash

  docker version

  ```

  **Command Prompt (single-line):**

  ```bash

  docker version

  ```

**What We Did**: You cloned the `getting-started` to-do app, created or verified a `Dockerfile` with `yarn` and a direct `node` command, updated `package.json` to include a `start` script if needed, built the image, and confirmed it works as a standalone container. This ensures our image is ready for Kubernetes.

## Step 3: Write the Kubernetes Manifest (YAML File)

Now we’ll create a Kubernetes YAML file (`bb.yaml`) to define a **Deployment** (to run the app) and a **Service** (to expose it). If you have the lab provided `bb.yaml`, you can use it; otherwise, we’ll create it.

1. **Create or Verify bb.yaml**:

   In the `getting-started-app` directory, if you do not have `bb.yaml`, create it:

   ```bash

   type nul > bb.yaml

   ```

   **Command Prompt (single-line):**

   ```bash

   type nul > bb.yaml

   ```

   Open it in a text editor such as VSCode (or notepad, notepad++):

   - Notepad:

     ```bash

     notepad bb.yaml

     ```

     **Command Prompt (single-line):**

     ```bash

     notepad bb.yaml

     ```

   - Visual Studio Code:

     ```bash

     code bb.yaml

     ```

     **Command Prompt (single-line):**

     ```bash

     code bb.yaml

     ```

   Add and save this content. Try to understand it in the context of Kubernetes:

   ```yaml

   apiVersion: apps/v1

   kind: Deployment

   metadata:

     name: bb-demo

     namespace: default

   spec:

     replicas: 1

     selector:

       matchLabels:

         bb: web

     template:

       metadata:

         labels:

           bb: web

       spec:

         containers:

         - name: bb-site

           image: getting-started

           imagePullPolicy: Never

   ---

   apiVersion: v1

   kind: Service

   metadata:

     name: bb-entrypoint

     namespace: default

   spec:

     type: NodePort

     selector:

       bb: web

     ports:

     - port: 3000

       targetPort: 3000

       nodePort: 30001

   ```

   If you copied the lab provided `bb.yaml`, open it and verify it matches this content.

2. **Understand the YAML**:

   - **Deployment (`bb-demo`)**:

     - `apiVersion: apps/v1`: Uses the `apps/v1` API for Deployments.

     - `kind: Deployment`: Defines a Deployment to manage pods.

     - `metadata`: Names the Deployment `bb-demo` in the `default` namespace.

     - `spec`:

       - `replicas: 1`: Runs one pod.

       - `selector: matchLabels: bb: web`: Manages pods with label `bb: web`.

       - `template`: Defines the pod.

         - `labels: bb: web`: Labels the pod for the Deployment and Service.

         - `containers`: One container named `bb-site`.

           - `image: getting-started`: Uses our local image.

           - `imagePullPolicy: Never`: Prevents Kubernetes from pulling from Docker Hub, using the local image.

   - **Service (`bb-entrypoint`)**:

     - `apiVersion: v1`: Uses the core `v1` API for Services.

     - `kind: Service`: Defines a Service.

     - `metadata`: Names the Service `bb-entrypoint`.

     - `spec`:

       - `type: NodePort`: Exposes the Service on a node port (accessible from `localhost`).

       - `selector: bb: web`: Routes traffic to pods with label `bb: web`.

       - `ports`:

         - `port: 3000`: Internal cluster port for the Service.

         - `targetPort: 3000`: Port on the pod’s container.

         - `nodePort: 30001`: Port on the host (`localhost:30001`).

**What We Did**: You created or verified `bb.yaml` to define a Deployment (running one pod with the `getting-started` app) and a NodePort Service (exposing it on `localhost:30001`). The `imagePullPolicy: Never` ensures Kubernetes uses the local image.

## Step 4: Deploy to Kubernetes with kubectl

Let’s deploy the app to Kubernetes using `kubectl`.

1. **Apply the YAML**:

   In the `getting-started-app` directory, run:

   ```bash

   kubectl apply -f bb.yaml

   ```

   **Command Prompt (single-line):**

   ```bash

   kubectl apply -f bb.yaml

   ```

   Expected output:

   ```

   deployment.apps/bb-demo created

   service/bb-entrypoint created

   ```

   This creates the Deployment and Service.

2. **Check Deployment Status**:

   Verify:

   ```bash

   kubectl get deployments

   ```

   **Command Prompt (single-line):**

   ```bash

   kubectl get deployments

   ```

   Expected output:

   ```

   NAME      READY   UP-TO-DATE   AVAILABLE   AGE

   bb-demo   1/1     1            1           40s

   ```

   `1/1` means one pod is ready. Optionally, check pods:

   ```bash

   kubectl get pods

   ```

   **Command Prompt (single-line):**

   ```bash

   kubectl get pods

   ```

   You’ll see a pod like `bb-demo-xxxxx` with `STATUS: Running`.

3. **Check Service Status**:

   Run:

   ```bash

   kubectl get services

   ```

   **Command Prompt (single-line):**

   ```bash

   kubectl get services

   ```

   Expected output:

   ```

   NAME            TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)          AGE

   bb-entrypoint   NodePort    10.106.145.116   <none>        3000:30001/TCP   53s

   kubernetes      ClusterIP   10.96.0.1        <none>        443/TCP          138d

   ```

   The Service routes `localhost:30001` to pod port 3000.

**Debugging Tips**:

- **Pod not running**: Check pod status:

  ```bash

  kubectl describe pod <pod-name>

  ```

  **Command Prompt (single-line):**

  ```bash

  kubectl describe pod <pod-name>

  ```

  Look for `ImagePullBackOff` (indicates image issues). Ensure `imagePullPolicy: Never` and the `getting-started` image exists (`docker images`).

- **Service not working**: Verify the Service selector matches the pod’s label (`bb: web`).

- **“kubectl: command not found”**: See Step 1 for `kubectl` path issues.

**What We Did**: You deployed the app to Kubernetes using `kubectl apply`, creating a pod via the Deployment and exposing it via the Service.

## Step 5: Access the App in Your Browser

1. **Open the App**:

   Go to `http://localhost:30001` in your browser. You should see the to-do list app, identical to `http://localhost:3000` from Step 2. Add tasks to confirm functionality.

2. **How It Works**:

   - Request to `localhost:30001` hits the Kubernetes node (Docker Desktop).

   - The `bb-entrypoint` Service (type `NodePort`) forwards to pod port 3000.

   - The pod’s `getting-started` container serves the app.

**Debugging Tips**:

- **“Connection refused”**: Ensure the Service is running (`kubectl get services`) and the pod is ready (`kubectl get pods`). Check logs:

  ```bash

  kubectl logs <pod-name>

  ```

  **Command Prompt (single-line):**

  ```bash

  kubectl logs <pod-name>

  ```

- **Wrong port**: Confirm `nodePort: 30001` in `bb.yaml`.

**What We Did**: You accessed the app through Kubernetes’ Service, confirming it runs as expected in a pod.

## Step 6: Clean Up

1. **Delete Resources**:

   Remove the Deployment and Service:

   ```bash

   kubectl delete -f bb.yaml

   ```

   **Command Prompt (single-line):**

   ```bash

   kubectl delete -f bb.yaml

   ```

   Expected output:

   ```

   deployment.apps "bb-demo" deleted

   service "bb-entrypoint" deleted

   ```

2. **Verify Deletion**:

   Check:

   ```bash

   kubectl get deployments

   kubectl get services

   ```

   **Command Prompt (single-line):**

   ```bash

   kubectl get deployments

   kubectl get services

   ```

   Only the `kubernetes` service should remain.

3. **Optional: Disable Kubernetes**:

   In Docker Desktop Settings > Kubernetes, uncheck **Enable Kubernetes** and click **Apply & Restart** to save resources (optional).

**What We Did**: You cleaned up Kubernetes resources, leaving your environment tidy.

## Conclusion and Next Steps

At this point, you have successfully used Docker Desktop to deploy your application to a fully-featured Kubernetes environment on your development machine. You can now add other components to your app and take advantage of all the features and power of Kubernetes, right on your own machine.

In addition to deploying to Kubernetes, you have also described your application as a Kubernetes YAML file. This simple text file contains everything you need to create your application in a running state. You can check it in to version control and share it with your colleagues. This lets you distribute your applications to other clusters (like the testing and production clusters that probably come after your development environments).

**Next Steps**:

- **Scale the Deployment**: Edit `bb.yaml` to set `replicas: 2`, reapply, and see multiple pods (`kubectl get pods`). The Service will load-balance across them.

- **Explore Kubernetes Dashboard**: Enable it in Docker Desktop (Settings > Kubernetes > Show system containers) for a visual interface.

- **Learn More**:

  - [Kubernetes Pods](https://kubernetes.io/docs/concepts/workloads/pods/)

  - [Kubernetes Deployments](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/)

  - [Kubernetes Services](https://kubernetes.io/docs/concepts/services-networking/service/)

- Try deploying a multi-container app (e.g., add a database) using Compose or Kubernetes.

Keep experimenting with Kubernetes to master orchestration! 🚢

**Sources**:

- [Kubernetes Documentation](https://kubernetes.io/docs/home/)

- [Docker Desktop Kubernetes](https://docs.docker.com/desktop/kubernetes/)

- W9 - Practical A Lab Manual
