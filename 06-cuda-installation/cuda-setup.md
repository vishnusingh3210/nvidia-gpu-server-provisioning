# CUDA Setup

- Install CUDA toolkit
- Verify using nvcc

Check:
nvcc --version
# CUDA Installation on Ubuntu 22.04 for NVIDIA H200 (Hopper)

This repository documents the step-by-step process to install **NVIDIA CUDA Toolkit (12.4+)** and **R550+ Data Center Driver** on **Ubuntu 22.04** for **NVIDIA H200 (Hopper, SM 9.0) GPU servers**.

The steps follow NVIDIA’s recommended **APT-based installation method**, suitable for production and data center environments.

---

## 📌 System Requirements

- **OS**: Ubuntu 22.04 LTS
- **GPU**: NVIDIA H200 (Hopper Architecture, SM 9.0)
- **CPU Arch**: x86_64
- **CUDA**: 12.x or newer (Recommended: 12.4+)
- **NVIDIA Driver**: R550 or newer
- **Secure Boot**: Disabled in BIOS

---

## 1️⃣ Verify System

```bash
lsb_release -a
uname -m
Expected:

Ubuntu 22.04

x86_64 architecture

2️⃣ Update System
sudo apt update
sudo apt upgrade -y
sudo reboot

3️⃣ Remove Existing NVIDIA / CUDA (If Installed)
sudo apt purge -y 'nvidia*' 'cuda*'
sudo apt autoremove -y
sudo reboot

4️⃣ Disable Nouveau Driver
echo -e "blacklist nouveau\noptions nouveau modeset=0" | sudo tee /etc/modprobe.d/blacklist-nouveau.conf
sudo update-initramfs -u
sudo reboot


Verify Nouveau is disabled:

lsmod | grep nouveau


(No output means success)

5️⃣ Install Required Dependencies
sudo apt install -y build-essential dkms linux-headers-$(uname -r)

6️⃣ Add NVIDIA CUDA Repository (Ubuntu 22.04)
wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2204/x86_64/cuda-keyring_1.1-1_all.deb
sudo dpkg -i cuda-keyring_1.1-1_all.deb
sudo apt update

7️⃣ Install NVIDIA Data Center Driver (R550+)
sudo apt install -y nvidia-driver-550
sudo reboot


Verify driver installation:

nvidia-smi


You should see NVIDIA H200 listed.

8️⃣ Install CUDA Toolkit (12.4)
sudo apt install -y cuda-toolkit-12-4


⚠️ Note: Avoid installing the cuda meta-package on servers to prevent automatic driver replacement.

9️⃣ Configure Environment Variables

Add the following lines to ~/.bashrc:

export PATH=/usr/local/cuda-12.4/bin:$PATH
export LD_LIBRARY_PATH=/usr/local/cuda-12.4/lib64:$LD_LIBRARY_PATH


Apply changes:

source ~/.bashrc

🔟 Verify CUDA Installation
nvcc --version


Expected output:

Cuda compilation tools, release 12.4

1️⃣1️⃣ Run CUDA Sample Test
cuda-install-samples-12.4.sh ~
cd ~/NVIDIA_CUDA-12.4_Samples/1_Utilities/deviceQuery
make
./deviceQuery


Expected output:

Result = PASS
Detected NVIDIA H200 GPU

1️⃣2️⃣ Enable Persistence Mode (Recommended for Servers)
sudo nvidia-smi -pm 1

📊 Recommended Software Stack
Component	Version
NVIDIA Driver	R550+
CUDA Toolkit	12.4+
cuDNN	9.x
NCCL	2.20+
Operating System	Ubuntu 22.04
🛠 Troubleshooting
Driver Not Loaded
dkms status
sudo apt install --reinstall nvidia-driver-550

CUDA Path Issues
ls /usr/local | grep cuda

📎 Notes

NVIDIA H200 requires CUDA 12+

Disable Secure Boot before installing drivers

For containers, install NVIDIA Container Toolkit

Suitable for bare metal and cloud GPU servers
