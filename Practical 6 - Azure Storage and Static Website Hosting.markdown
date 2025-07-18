# Practical 6 - Azure Storage and Static Website Hosting

## Objectives

This lab guides you through:

- Creating an Azure storage account using Azure CLI
- Understanding storage account structure and redundancy
- Enabling static website hosting in Azure
- Uploading and serving HTML content from the `$web` container
- Building and deploying a full multi-file static website portfolio (with HTML, CSS, JS, and images)

## Creating a Storage Account Using Azure CLI

### Opening Azure Cloud Shell

1. Navigate to [https://portal.azure.com](https://portal.azure.com)
2. Log in with your Microsoft account
3. Click the Cloud Shell icon (`>_`) at the top-right of the page
4. Select **Bash** if prompted
5. Wait for the terminal to load

### Registering CloudShell (if required)

If you encounter the error:

> Storage fileshare subscription ... is not registered to Microsoft.CloudShell Namespace.

Run the following command:

```bash
az account set --subscription "Azure for Students"
```
```bash
az provider register --namespace Microsoft.CloudShell
```

or 
```bash
az group create --name my-rg --location uksouth
```
Then verify registration:

```bash
az provider show --namespace Microsoft.CloudShell --query "registrationState"
```

Expected output:

```
"Registered"
```

### Creating a Resource Group

Run:

```bash
az group create \
  --name storage-resource-group \
  --location uksouth
```

**Explanation**:

- `--name`: Specifies the name for your resource group
- `--location`: Defines the region to host the resources (London = `uksouth`)

Expected output includes:

```json
"provisioningState": "Succeeded"
```

### Creating a Storage Account

Run:

```bash
az storage account create \
  --name mystorageuk123 \
  --resource-group storage-resource-group \
  --location uksouth \
  --sku Standard_RAGRS \
  --kind StorageV2
```

**Explanation**:

- `--name mystorageuk123`: Must be unique, lowercase, no spaces
- `--resource-group`: Places the account in the created resource group
- `--location`: Matches the resource group region
- `--sku Standard_RAGRS`: Provides redundant backup across multiple locations
- `--kind StorageV2`: Supports blob, table, queue, file, and static site hosting

Look for:

```json
"provisioningState": "Succeeded"
```

### Optional: Enabling Hierarchical Namespace

For Data Lake Gen2 features, run:

```bash
az storage account create \
  --name mystorageuk123 \
  --resource-group storage-resource-group \
  --location uksouth \
  --sku Standard_RAGRS \
  --kind StorageV2 \
  --enable-hierarchical-namespace true
```

### Verifying Your Storage Account

Run:

```bash
az storage account list --output table
```

Expected output:

```
Name             ResourceGroup           Location    SkuName          Kind      AccessTier
---------------  ----------------------  ----------  ----------------  --------  -----------
mystorageuk123   storage-resource-group  uksouth     Standard_RAGRS    StorageV2 Hot
```

## Hosting a Static Website

### Creating an HTML Page

Create a file named `index.html` with the following content:

```html
<html>
  <h1>Cloud Computing Lab</h1>
  <h2>Your Name</h2>
</html>
```

Save the file using a text editor like Notepad or VS Code.

### Enabling Static Website Hosting

1. In the Azure Portal, navigate to your Storage Account
2. Scroll to **Data Management** and click **Static website**
3. Set **Static website** to **Enabled**
4. Configure:
   - **Index document name**: `index.html`
   - **Error document path**: `error.html` (optional)
5. Click **Save**

Azure creates a special container called `$web`.

### Uploading Your HTML File

1. Navigate to **Containers** in the Storage Account
2. Click on `$web`
3. Click **Upload**
4. Select your `index.html` file
5. Click **Upload**

### Visiting Your Website

1. Return to the **Static website** section
2. Copy the **Primary endpoint**, e.g., `https://mystorageuk123.z13.web.core.windows.net/`
3. Paste into your browser

Expected output in the browser:

> Cloud Computing Lab  
> Your Name

## Optional: Deploying a Full Static Portfolio Site

### Project Folder Structure

Organize your project as follows:

```
project-folder/
├── index.html
├── about.html
├── style.css
├── script.js
└── images/
    └── logo.png
```

### Sample Files

#### `index.html`

```html
<!DOCTYPE html>
<html>
<head>
  <title>My Portfolio</title>
  <link rel="stylesheet" href="style.css">
  <script src="script.js" defer></script>
</head>
<body>
  <header>
    <img src="images/logo.png" alt="Logo" width="100">
    <h1>Welcome to My Portfolio</h1>
    <nav>
      <a href="index.html">Home</a> |
      <a href="about.html">About Me</a>
    </nav>
  </header>
  <main>
    <p>I'm learning cloud computing and web development.</p>
  </main>
</body>
</html>
```

#### `about.html`

```html
<!DOCTYPE html>
<html>
<head>
  <title>About</title>
  <link rel="stylesheet" href="style.css">
</head>
<body>
  <h1>About Me</h1>
  <p>This is a portfolio site hosted on Azure.</p>
  <a href="index.html">Back to Home</a>
</body>
</html>
```

#### `style.css`

```css
body {
  font-family: Arial, sans-serif;
  background-color: #f0f8ff;
  padding: 30px;
  text-align: center;
}
nav a {
  margin: 0 10px;
  text-decoration: none;
  color: #0078d4;
}
```

#### `script.js`

```javascript
document.addEventListener("DOMContentLoaded", () => {
  console.log("Portfolio loaded from Azure!");
});
```

#### `images/logo.png`

Use any image and name it `logo.png`.

### Uploading Steps

1. Navigate to the `$web` container
2. Click **Upload folder** and upload the `images/` folder
3. Upload the following files:
   - `index.html`
   - `about.html`
   - `style.css`
   - `script.js`

Maintain the folder structure during upload.

### Testing URLs

Access the following:

- `index.html`: `https://<storage-name>.z13.web.core.windows.net/`
- `about.html`: `https://<storage-name>.z13.web.core.windows.net/about.html`
- `images/logo.png`: `https://<storage-name>.z13.web.core.windows.net/images/logo.png`

## Summary

This practical covered:

- Creating a storage account via Azure CLI
- Enabling and hosting a static website
- Deploying a multi-file portfolio with HTML, CSS, JavaScript, and images

Your portfolio is now hosted on Azure, accessible worldwide.
