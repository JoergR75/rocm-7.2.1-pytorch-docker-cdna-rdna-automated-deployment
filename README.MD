# ⚙️ ROCm 7.2.1 + OpenCL 2.x + PyTorch (Preview (Nightly)) + Transformers + Docker Setup

[![ROCm](https://img.shields.io/badge/ROCm-7.2.0-ff6b6b?logo=amd)](https://rocm.docs.amd.com/en/docs-7.2.0/about/release-notes.html)
[![PyTorch](https://img.shields.io/badge/PyTorch-2.11.0%20%28Preview%29-ee4c2c?logo=pytorch)](https://pytorch.org/get-started/locally/)
[![Docker](https://img.shields.io/badge/Docker-29.2.0-blue?logo=docker)](https://www.docker.com/)
[![Ubuntu](https://img.shields.io/badge/Ubuntu-22.04%20%7C%2024.04-e95420?logo=ubuntu)](https://ubuntu.com/download/server)
[![AMD Radeon AI PRO R9700](https://img.shields.io/badge/AMD-RDNA4%20Radeon(TM)%20AI%20PRO%20R9700-8B0000?logo=amd)](https://www.amd.com/en/products/graphics/workstations/radeon-ai-pro/ai-9000-series/amd-radeon-ai-pro-r9700.html)
[![AMD CDNA MI300 Series](https://img.shields.io/badge/AMD-CDNA%20Instint(TM)%20Architecture-8B0000?logo=amd)](https://www.amd.com/en/technologies/cdna.html)

## 📌 Overview
The script provisions a fully automated, non-interactive AMD GPU software development environment for AI and HPC software engineering on **Ubuntu 22.04** and **24.04**, centered on **ROCm 7.2.1** and **PyTorch** Preview Nightly.

At the platform layer, it installs the AMD GPU kernel driver (**amdgpu-dkms**) and the ROCm 7.2.1 runtime, including **HIP** and **OpenCL 2.x**, ensuring compatibility across **CDNA1**, **CDNA2**, **CDNA3** **CDNA4**, **RDNA3**, **RDNA4** GPUs and **Strix APUs**. The script configures **OpenCL ICD** paths, user group permissions (video, render, sudo), and kernel headers required for compiling GPU-accelerated native extensions.

For the AI framework layer, the script installs **PyTorch 2.12.x Preview Nightly** (**ROCm 7.2.1 wheels**) directly from the official PyTorch ROCm nightly repository, enabling access to the latest HIP backends, kernel fusion paths, and compiler features. It complements PyTorch with Transformers, Accelerate, Diffusers, Datasets, SentencePiece, and supporting Python build tooling, allowing immediate development, testing, and profiling of modern LLM, diffusion, and data-parallel workloads.

The developer toolchain is rounded out with C/C++ build and system utilities required for low-level GPU software engineering and extension development, including **cmake**, **libstdc++ dev headers**, **git** / **git-lfs**, **libmsgpack**, and **rocm-bandwidth-test** for validating PCIe and HBM bandwidth. Runtime observability and system inspection are supported via htop, ncdu, and ROCm diagnostics (rocminfo, rocm-smi, amd-smi).

A validation script is generated to verify end-to-end GPU availability, confirming ROCm detection, PyTorch HIP enablement, GPU enumeration, and successful on-device tensor execution.

The setup is fully **non-interactive** and optimized for both **desktop** and **server** deployments. In addition it checks whether ROCm or PyTorch (installed via pip) is already present on the system.
If an existing ROCm installation is detected, it removes ROCm and related packages to ensure a clean environment. It also **detects** and **uninstalls** any PyTorch packages (including ROCm-specific builds) to prevent version conflicts before proceeding with a fresh installation.

---

## 🖥️ Supported Platforms

| **Component**      | **Supported Versions**                                |
|---------------------|------------------------------------------------------|
| **OS**            | Ubuntu 22.04.x (Jammy Jellyfish), Ubuntu 24.04.x (Noble Numbat) |
| **Kernels** tested       | 5.15.0-171 (22.04.5) • 6.8.0-100 (24.04.4)                       |
| **GPUs**          | AMD **CDNA1** • **CDNA2** • **CDNA3** • **CDNA4** • **RDNA3** • **RDNA4**              |
| **APUs**        | AMD **Strix** • **Strix Halo**                                       |
| **ROCm**          | 7.2.1                                                |
| **PyTorch**       | torch 2.12.0.dev20260307+rocm7.1, torchvision 0.26.0.dev20260308+rocm7.1       |       |

**⚠️ Note**: **Ubuntu 20.04.x (Focal Fossa)** is **not supported**. The last compatible ROCm version for 20.04 is **6.4.0**.

---

## ⚡ Features
- Automated **ROCm GPU drivers + HIP + OpenCL SDK** installation
- **PyTorch ROCm Preview Nightly** with GPU acceleration
- Preinstalled **Transformers**, **Accelerate**, **Diffusers**, and **Datasets**
- Integrated **Docker environment** with ROCm GPU passthrough
- **vLLM Docker images** for **RDNA4** & **CDNA**
- Optimized for **AI workloads**, **LLM inference**, and **model fine-tuning**

---

## 🚀 Installation

### 1️⃣ **System preperation**
Install **Ubuntu 22.04.5 LTS** or **Ubuntu 24.04.4 LTS** (Server or Desktop version).

**Recommendations:**
- Use a fresh Ubuntu installation if possible
- Assign the full storage capacity during installation
- Install **OpenSSH** for remote SSH management
- The script automatically checks the system for installed versions of ROCm, PyTorch, and Docker, and removes them if found
  - On a fresh Ubuntu installation, the script automatically skips the deinstallation routine, as illustrated below
    <img width="697" height="188" alt="{DB29AEE6-CF12-4D0D-BA9F-611E73DBE146}" src="https://github.com/user-attachments/assets/48516cb6-e7bd-4c7e-94bb-f3ec9a95b243" />
  - If an existing version is detected, it will be deleted, regardless of whether it is the same or an older release.
    <img width="724" height="312" alt="{ABC66E35-246B-49CD-B988-5C19DA511ACB}" src="https://github.com/user-attachments/assets/c0a87932-fb0e-4adb-8bf9-cbe80d13f528" />
- SBIOS settings:
  - When using Linux, you should disable Secure Boot
  - On WRX80 and WRX90 motherboard solutions, make sure SR-IOV is enabled — there are known issues with Ubuntu Linux detecting the network otherwise

### 2️⃣ **Download the Script from the Repository**
```bash
wget https://raw.githubusercontent.com/JoergR75/rocm-7.2.1-pytorch-docker-cdna-rdna-automated-deployment/refs/heads/main/script_module_ROCm_721_Ubuntu_22.04-24.04_pytorch_server.sh
```


### 3️⃣ **Run the Installer**
```bash
bash script_module_ROCm_721_Ubuntu_22.04-24.04_pytorch_server.sh
```
**⚠️ Note**: Entering the user password may be required.

<img width="956" height="327" alt="{036AB9F1-945C-461E-B497-C8F977804405}" src="https://github.com/user-attachments/assets/df538538-32d7-40c3-a67e-cc065c5b8bc0" />

The installation takes ~15 minutes depending on internet speed and hardware performance.

### 4️⃣ **Reboot the System**
After the successful installation, press "y" to reboot the system and activate all installed components.

<img width="962" height="542" alt="{C72A982C-F406-4CFD-AF9E-78706250C21B}" src="https://github.com/user-attachments/assets/dc00f682-b27d-4fc8-8d24-973aa8b9f0c9" />

## 🧪 Testing ROCm + PyTorch

After rebooting, verify your setup:

This script creates a simple diagnostic python file (test.py) to verify that PyTorch with ROCm support is correctly installed and working.

What it does:

- Shows the CPU and installed memory
- Prints the PyTorch version and ROCm version.
- Checks if ROCm is available and how many GPUs are detected.
- Displays the name of the first GPU (if available).
- Creates two random 3×3 tensors directly on the GPU (if available).
- Performs a simple tensor addition operation on the GPU.
- Prints confirmation that the operation was successful and shows the result.

Example usage:
```bash
python3 test.py
```
Expected Output Example:

<img width="901" height="290" alt="{BA60D6AC-8DEA-45C2-BB42-BB2F42A9F346}" src="https://github.com/user-attachments/assets/f2a67558-6cdb-40e2-a3b4-80789344262a" />
