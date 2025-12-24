
# MLMIC25 – Student Setup Guide

Welcome!
In this course you will run all Python code **inside a Docker container**, and connect to it from **Visual Studio Code (VS Code)** using the Jupyter extension.

This guide walks you through:

1. Installing **Docker** (macOS & Windows)
2. Installing **VS Code** and required extensions
3. Getting the **course files**
4. **Building** the course Docker image
5. **Running** the container
6. Connecting **VS Code to the Jupyter kernel** running in Docker

> If you get stuck: take a screenshot of the error and your command, and send it to your instructor.

---

## 1. Prerequisites

### 1.1. Hardware / OS

- A laptop with at least:
  - 8 GB RAM (16 GB recommended)
  - 10–15 GB free disk space
- Operating system:
  - **macOS** (Intel or Apple Silicon)
  - **Windows 10 / 11** (64-bit)

### 1.2. You will install

- **Docker Desktop** (macOS / Windows)
- **Visual Studio Code**
- VS Code extensions:
  - **Python**
  - **Jupyter**
  - (Optional) **Docker**
  - (Optional) **Quarto**

---

## 2. Install Docker

### 2.1. macOS (Docker Desktop)

1. Open a browser and go to:  
   https://www.docker.com/products/docker-desktop/
2. Download **Docker Desktop for Mac**:
   - Choose the right version:
     - **Apple Silicon (M1/M2/M3)** → ARM version
     - **Intel chip** → Intel version
3. Open the downloaded `.dmg` file.
4. Drag **Docker.app** into the **Applications** folder.
5. Start Docker:
   - Open **Launchpad** → click **Docker**
   - You may need to **accept security dialogs** and **sign in** (you can create a free Docker account).
6. Wait until the Docker whale icon in the menu bar stops animating and shows **“Docker Desktop is running”**.

To verify Docker works, open **Terminal** and run:

```bash
docker run hello-world
```

You should see a message saying: **“Hello from Docker!”**

---

### 2.2. Windows (Docker Desktop)

> **Important:** On Windows, Docker Desktop requires **WSL2** and virtualization enabled.

#### 2.2.1. Enable WSL2 and Virtualization (one-time)

1. Press **Start**, type `powershell`, right-click **Windows PowerShell**, choose **Run as administrator**.
2. Run:

```powershell
wsl --install
```

3. Reboot when prompted.

Verify WSL is installed:

```powershell
wsl -l -v
```

You should see at least one distro (e.g. Ubuntu).

Also make sure **virtualization is enabled** in your BIOS/UEFI (usually it already is; if not, look for “Intel VT-x” or “AMD-V”).

#### 2.2.2. Install Docker Desktop

1. Go to:  
   https://www.docker.com/products/docker-desktop/
2. Download **Docker Desktop for Windows**.
3. Run the installer:
   - During setup, **leave “Use WSL 2 backend” checked**.
4. After installation, start **Docker Desktop** from the Start menu.
5. Wait for it to say **“Docker Desktop is running”**.

Verify Docker works. In **PowerShell**:

```powershell
docker run hello-world
```

If you see **“Hello from Docker!”**, Docker is correctly installed.

---

## 3. Install Visual Studio Code and Extensions

### 3.1. Install VS Code

1. Go to: https://code.visualstudio.com/
2. Download and install **VS Code** for your OS (macOS or Windows).
3. Start VS Code.

### 3.2. Install required extensions

In VS Code:

1. Click the **Extensions** icon on the left (or press `Ctrl+Shift+X` / `Cmd+Shift+X`).
2. Search and install:

   - **Python** (by Microsoft)
   - **Jupyter** (by Microsoft)

3. Optional but recommended extensions:

   - **Docker** (helps see containers/images inside VS Code)
   - **Quarto** (if you will use `.qmd` documents)

Make sure the extensions are **enabled** (no “Enable” button visible).

---

## 4. Get the Course Files

Your instructor will give you either:

- A **ZIP file** with the course materials, or  
- A **Git repository** to clone.

### 4.1. Create a course folder

Decide on a folder where you will keep everything, for example:

- On macOS: `/Users/<yourname>/MLMIC25`
- On Windows: `C:\\Users\\<yourname>\\MLMIC25`

Extract or clone the course materials into that folder so you have something like:

```text
MLMIC25/
  Dockerfile_x86      (or Dockerfile_mac – depends on instructions)
  mlmiin26.yml        (conda environment file)
  1_1_Introduction/
    notebook1.ipynb
  1_2_SomethingElse/
    ...
```

We’ll call this folder your **course root**.

---

## 5. Build the Course Docker Image

You only need to **build the image once** (unless the course Dockerfile changes).

Your instructor will tell you the exact Dockerfile name and image tag.  
In this guide we’ll assume:

- Dockerfile: `Dockerfile_x86` (for most students)
- Image name: `mlmiin:latest`

### 5.1. macOS / Linux steps

1. Open **Terminal**.
2. Go to your course root folder, e.g.:

```bash
cd ~/MLMIC25
```

3. Build the image:

```bash
docker image build --file Dockerfile_x86 --tag mlmiin:latest .
```

This can take a while the first time (it creates the conda environment and installs all required libraries).

When it finishes, you can confirm the image exists:

```bash
docker images
```

You should see something like `mlmiin   latest` in the list.

### 5.2. Windows (PowerShell) steps

1. Open **PowerShell**.
2. Go to your course root folder, e.g.:

```powershell
cd C:\Users\<yourname>\MLMIC25
```

3. Build the image:

```powershell
docker image build --file Dockerfile_x86 --tag mlmiin:latest .
```

Again, check it with:

```powershell
docker images
```

You should see `mlmiin` listed.

---

## 6. Run the Docker Container

You will run the container from the **course root** so that Docker can see all the subfolders (e.g. `1_1_Introduction`, `1_2_...`).

We will mount the course root folder into the container at `/wd`.

### 6.1. macOS / Linux run command

In **Terminal**, from your course root folder:

```bash
cd ~/MLMIC25

docker run -it --rm \
  -p 8888:8888 \
  -v "$PWD":/wd \
  mlmiin:latest
```

Explanation:

- `-it` → interactive terminal
- `--rm` → delete the container when you stop it
- `-p 8888:8888` → map container port 8888 to your laptop’s port 8888
- `-v "$PWD":/wd` → share the current folder into the container as `/wd`
- `mlmiin:latest` → the image you built

### 6.2. Windows (PowerShell) run command

In **PowerShell**, from your course root folder:

```powershell
cd C:\Users\<yourname>\MLMIC25

docker run -it --rm `
  -p 8888:8888 `
  -v "${PWD}:/wd" `
  mlmiin:latest
```

(Note: the backticks ` are PowerShell line continuation characters.)

You can also write it on one line:

```powershell
docker run -it --rm -p 8888:8888 -v "${PWD}:/wd" mlmiin:latest
```

### 6.3. What you should see

In the terminal, you should see Jupyter starting and eventually a line like:

```text
http://127.0.0.1:8888/lab
```

In our course image, Jupyter is usually configured **without a token**, so the URL is simply `http://localhost:8888`.

Keep this terminal **open** while you work.  
If you close it, the container (and Jupyter) will stop.

---

## 7. Connect from VS Code to the Jupyter Kernel in Docker

You have two ways to work:

1. Open a browser and go directly to `http://localhost:8888` (JupyterLab).  
2. Or (recommended) use **VS Code** and connect to the same Jupyter server.

We’ll use option 2.

### 7.1. Open the course folder in VS Code

1. Start VS Code.
2. Go to **File → Open Folder...**
3. Select your course root folder (`MLMIC25`).
4. VS Code might ask if you **trust** the folder → click **Yes, I trust the authors**.

### 7.2. Open a notebook

In the VS Code **Explorer** panel:

1. Navigate to something like `1_1_Introduction/`.
2. Open the `.ipynb` notebook file (click it).

VS Code should now show the notebook editor.

### 7.3. Select the Jupyter server

If VS Code is not already connected to the Docker Jupyter server:

1. Press **Ctrl+Shift+P** (Windows/Linux) or **Cmd+Shift+P** (macOS) to open the Command Palette.
2. Type: `Jupyter: Select Jupyter Server`
3. Choose the option to **enter the URL of a running Jupyter server**.
4. Enter:

```text
http://localhost:8888
```

5. Press Enter.

VS Code should now show that it’s connected to the server at `http://localhost:8888` (check the status bar or the top of the notebook window).

### 7.4. Select the Python kernel

At the top-right of the notebook, you will see a **kernel selector** (often showing something like “Python 3.x”):

1. Click on the kernel name.
2. You should see a list of kernels.
3. Choose the one corresponding to the **conda environment inside Docker** (often just “Python 3 (ipykernel)” if only one is available).

If everything is configured correctly, running a cell should now execute **inside the Docker container**.

Try this test cell:

```python
import sys
print(sys.executable)

import os
print("Current directory:", os.getcwd())
```

You should see that Python is running from a path inside the container (e.g. `/opt/conda/...`) and that the current directory is something like `/wd` or `/wd/1_1_Introduction` depending on where the notebook lives.

---

## 8. Typical Workflow for Each Session

For **every course session**, you will usually repeat these steps:

1. **Start Docker Desktop** (Windows/macOS) → wait until it’s running.
2. Open a terminal (macOS) or PowerShell (Windows).
3. Go to your course root folder, e.g.:
   - macOS:
     ```bash
     cd ~/MLMIC25
     ```
   - Windows (PowerShell):
     ```powershell
     cd C:\Users\<yourname>\MLMIC25
     ```
4. Start the container:
   - macOS/Linux:
     ```bash
     docker run -it --rm -p 8888:8888 -v "$PWD":/wd mlmiin:latest
     ```
   - Windows (PowerShell):
     ```powershell
     docker run -it --rm -p 8888:8888 -v "${PWD}:/wd" mlmiin:latest
     ```
5. Open **VS Code**, open the **MLMIC25** folder.
6. Open the desired notebook (`.ipynb`).
7. Make sure VS Code is connected to `http://localhost:8888` as the Jupyter server.
8. Select the Python kernel and start running cells.

---

## 9. Troubleshooting

### 9.1. “docker: permission denied” (Linux only)

If you’re on Linux and see a **permission denied** error when running Docker:

1. Add your user to the `docker` group:

   ```bash
   sudo usermod -aG docker $USER
   ```

2. Log out and log back in, or reboot.
3. Try again.

(Windows/macOS users normally do **not** see this issue.)

---

### 9.2. Browser/VS Code cannot connect to `http://localhost:8888`

Check:

1. Is the container running?  
   Look at the terminal where you ran `docker run`. Jupyter logs should be visible.
2. Did you map the port?  
   Make sure you used `-p 8888:8888`.
3. Is Docker Desktop running?  
   On Windows/macOS, Docker Desktop must be open and running.

You can also run:

```bash
docker ps
```

to see if the container is listed.

---

### 9.3. VS Code “No Jupyter server running”

- Make sure the Docker container is running and Jupyter has started.
- Try `Jupyter: Select Jupyter Server` again and enter `http://localhost:8888`.
- Ensure the **Jupyter extension** is installed and enabled.
- Make sure you **trusted the workspace**.

---

### 9.4. ImportError / ModuleNotFoundError

If you see an error like:

```text
ModuleNotFoundError: No module named 'XYZ'
```

It usually means:

- You are not using the correct kernel (not the one inside the Docker env), or
- The course helper `.py` file is not located in the course folder mounted into `/wd`.

Check:

1. In the notebook, run:
   ```python
   import sys
   print(sys.executable)
   ```
   If it does **not** point to `/opt/conda/envs/...` inside the container, you are using the wrong kernel.
2. Make sure all `.py` helper files given by the instructor are present in the **course root** (or the correct subfolder).

---

## 10. Getting Help

If something doesn’t work:

1. Take a screenshot of:
   - The terminal where you ran `docker run`
   - VS Code (notebook, kernel indicator, and any error)
2. Copy any error messages.
3. Send them to your instructor, along with:
   - Your operating system (Windows/macOS, version)
   - Which command you ran.

Welcome to the course, and happy coding! 🚀
